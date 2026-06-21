# Refute - Counterfactual Shrinking for Property-Based Testing

## **Integrantes**

- Dichiara Brenda
- Mansilla Lucio
- Streri Nicolás

## **Objetivo**

Pregunta de investigación:

> ¿Puede un generador de explicaciones contrafactuales reemplazar el paso de shrinking en un pipeline de property-based testing, produciendo contraejemplos más pequeños, más interpretables y más cercanos a datos reales que los generados por herramientas estándar?

La hipótesis central es que un contrafactual mínimo y un minimal witness de property-based testing son instancias del mismo problema de optimización:

$$
x^* = \arg\min_{x'} \, d(x, x') \quad \text{sujeto a} \quad \neg P(f, x') \land P(f, x)
$$

La diferencia no está en la estructura del problema sino en qué se considera "mínimo": el shrinker de Hypothesis minimiza la complejidad sintáctica del input (representación más compacta), mientras que DiCE minimiza la distancia en el espacio semántico de features ponderada por relevancia. La pregunta experimental es si la noción semántica de mínimo produce witnesses más útiles para el análisis de modelos de ML.

## **Propuesta experimental**

Se propone construir refute, una librería Python que unifica property-based testing y explicaciones contrafactuales. El ciclo estándar de Hypothesis es:

```
generar input -> testear propiedad -> FAIL -> shrinker -> repetir shrinker -> witness mínimo
```

En refute, el paso de shrinking es reemplazado por un proceso en dos etapas: DiCE propone candidatos en el vecindario semántico del input fallido, y luego se verifica cuáles de esos candidatos siguen violando la propiedad:

```
generar input x -> testear propiedad P(f, x) -> FAIL -> DiCE genera {x'_1, ..., x'_k} cercanos a x
    -> verificar ¬P(f, x'_i) para cada candidato -> filtrar los que aún violan P
    -> seleccionar el de menor dL1norm(x, x'_i) -> witness mínimo
```

La verificación explícita es necesaria porque DiCE no conoce la propiedad P: genera puntos cercanos a x que son plausibles según el manifold de datos, pero no garantiza que esos puntos violen P. El filtro convierte a DiCE en un explorador de vecindario y deja la decisión de validez a la propiedad misma. Si ningún candidato viola P, refute retorna el input original como witness.

El criterio de selección entre candidatos válidos es la distancia L1 normalizada por rango de feature:

$$d_{L1}^{norm}(x, x') = \sum_{j} \frac{|x_j - x'_j|}{\max_j - \min_j}$$

donde $\max_j$ y $\min_j$ son el máximo y mínimo de la feature $j$ en el training set. La normalización evita que features de escala grande (como `age` o `hours_per_week`) dominen a features binarias (como `sex`). Esta misma distancia se usa como métrica de comparación contra Hypothesis, de modo que el criterio de selección y el criterio de evaluación son consistentes.

El objeto resultante se llama witness: el contraejemplo mínimo que certifica la violación de una propiedad del modelo. Cumple dos roles simultáneamente: es evidencia de que el modelo falla, y es una explicación de por qué falla.

El experimento consiste en comparar, sobre los mismos modelos y las mismas propiedades, qué tan bueno es el witness que produce el shrinking estándar de Hypothesis vs. el witness que produce el shrinker contrafactual de refute. Para eso vamos a usar dos datasets clásicos: Adult Income (predicción de si una persona gana más o menos de 50K anuales) y COMPAS (predicción de reincidencia criminal). Ambos tienen sesgos conocidos respecto a sexo y raza, lo que hace fácil encontrar violaciones reales. En cada caso entrenamos un modelo (a definir) sin ninguna restricción de fairness.

La propiedad a testear es **equidad individual**: dado un input $x$, el modelo debe predecir lo mismo si se cambia únicamente el atributo sensible (sexo o raza) y se mantienen fijas el resto de las features. Formalmente:

$$P(f, x) \iff f(x) = f(\text{flip\_sensitive}(x))$$

Se elige equidad individual por dos razones. Primero, es directamente relevante para los sesgos conocidos de Adult Income y COMPAS. Segundo, su estructura se adapta naturalmente al paradigma de contrafactuales: el witness que viola la propiedad es exactamente el par $(x, x')$ con $x'$ igual a $x$ salvo en el atributo sensible, lo que hace que DiCE opere en el subespacio correcto por diseño.

En una primera etapa se pretende realizar una revisión bibliográfica quedando abierta la posibilidad de incorporar una segunda propiedad. Algunos candidatos naturales son: monotonicidad de feature (aumentar `education_num` no debería reducir la predicción de ingreso alto; reducir `priors_count` no debería aumentar la predicción de reincidencia), robustez local (el modelo debería mantener su predicción en un entorno pequeño alrededor de x, testeando sensibilidad en bordes de decisión), e invariancia a features irrelevantes (cambiar `fnlwgt` en Adult Income no debería afectar la predicción). La elección dependerá de qué estructuras de propiedad resulten más informativas para comparar los dos pipelines.

Para cada combinación de modelo y propiedad, ejecutamos los dos pipelines y comparamos los witnesses que producen. Las métricas de comparación son: cuánto se aleja el witness del input original en distancia L1 normalizada, cuántas features cambia, y qué tan cerca está de un punto real del dataset de entrenamiento (distancia al vecino más cercano en el conjunto de entrenamiento). De esta manera consideramos que un witness $w_1$ domina a $w_2$ si tiene menor distancia L1 al input original y menor distancia al vecino más cercano en el training set. La hipótesis es que el witness contrafactual domina al de Hypothesis en esta definición.

### Interfaz de declaración de propiedades

El diseño pensado para refute es un decorador que permite declarar propiedades junto con los constraints sobre qué features puede explorar el generador contrafactual. La idea es que el usuario especifique la semántica de la propiedad directamente en la firma: qué features son relevantes para la violación y cuáles deben mantenerse fijas como contexto:

```python
@refute.property(fixed=["age", "education", "hours_per_week"], vary=["sex"])
def individual_fairness(model, x):
    return model.predict(x) == model.predict(flip_sex(x))
```

El decorador acepta los siguientes parámetros:

- `vary`: lista de features sobre las que DiCE puede generar variaciones. Los candidatos solo difieren del input original en estas dimensiones.
- `fixed`: lista de features que deben mantenerse idénticas al input original. Si no se especifica, se infiere como el complemento de `vary`.
- `n_witnesses`: cuántos candidatos generar por input fallido (default: 1). Permite seleccionar el de menor distancia al original entre los que verifican la propiedad.
- `diversity_weight`: parámetro pasado a DiCE para controlar cuán distintos entre sí son los candidatos generados cuando `n_witnesses > 1`.

Con esta interfaz DiCE opera únicamente sobre el subespacio declarado en `vary`, y el witness final es el candidato de menor distancia al original entre los que superaron la verificación.

Además del decorador de propiedad, la librería expondrá las siguientes funciones:

- `refute.register_data` registra el dataset de entrenamiento que DiCE usa como referencia del manifold real. Sin este paso DiCE no puede medir proximidad a datos reales ni generar candidatos plausibles:

```python
refute.register_data(
    X_train,
    continuous=["age", "hours_per_week", "education_num"],
    categorical=["sex", "race", "workclass"],
)
```

- `refute.run` ejecuta el pipeline completo sobre un conjunto de inputs y devuelve un `WitnessReport` con todos los witnesses encontrados:

```python
report = refute.run(model, individual_fairness, inputs=X_test, n_samples=200)
```

El objeto `WitnessReport` permite inspeccionar los resultados:

```python
report.witnesses          # lista de Witness encontrados
report.failure_rate       # fracción de inputs que violaron la propiedad
report.summary()          # tabla con métricas por witness: L1, features cambiadas, dist. a vecino más cercano
```

Cada `Witness` expone sus métricas individuales y permite verificar que sigue violando la propiedad en el momento de inspeccionarlo:

```python
w = report.witnesses[0]
w.original        # input original que falló
w.counterfactual  # el witness encontrado por DiCE
w.l1_distance     # distancia L1 entre original y witness
w.nn_distance     # distancia al vecino más cercano en X_train
w.verify(model)   # re-evalúa la propiedad sobre el witness; retorna bool
```

## **Planificación de Actividades**

| Tarea                     | S1  | S2  | S3  | S4  | S5  | S6  |
| :------------------------ | --- | --- | --- | --- | --- | --- |
| Revisión bibliográfica    | X   |     |     |     |     |     |
| Implementar refute        |     | X   | X   |     |     |     |
| Entrenar Modelos          |     |     | X   | X   |     |     |
| Ejecución de Experimentos |     |     | X   | X   |     |     |
| Análisis de Resultados    |     |     |     |     | X   | X   |
| Informe final             |     |     |     |     |     | X   |

## **Relación con los temas del curso**

Week 6: Counterfactual Explanations (or) Algorithmic Recourse
