# refute: Property-Based Testing with Counterfactual Witnesses

## **Integrantes**

- Dichiara Brenda
- Mansilla Lucio
- Streri Nicolás

## **Objetivo**

Pregunta de investigación:

> ¿Puede un generador de explicaciones contrafactuales reemplazar el paso de shrinking en un pipeline de property-based testing, produciendo contraejemplos más pequeños, más interpretables y más cercanos a datos reales que los generados por herramientas estándar?

La hipótesis central es que un contrafactual mínimo y un minimal witness de property-based testing son el mismo objeto matemático, el resultado de un problema de optimización de la forma:

$$
x^* = \arg\min_{x'} \, d(x, x') \quad \text{sujeto a} \quad \neg P(f, x') \land P(f, x)
$$

donde la única diferencia entre ambos es la métrica $d()$ que se utiliza.

## **Propuesta experimental**

Se propone construir refute, una librería Python que unifica property-based testing y explicaciones contrafactuales. El ciclo estándar de Hypothesis es:

```
generar input -> testear propiedad -> FAIL -> shrinker -> repetir shrinker -> witness mínimo
```

En refute, el paso de shrinking es reemplazado por un generador contrafactual:

```
generar input -> testear propiedad -> FAIL -> DiCE(input) -> witness mínimo
```

El objeto resultante se llama witness: el contraejemplo mínimo que certifica la violación de una propiedad del modelo. Cumple dos roles simultáneamente: es evidencia de que el modelo falla, y es una explicación de por qué falla.

El experimento consiste en comparar, sobre los mismos modelos y las mismas propiedades, qué tan bueno es el witness que produce el shrinking estándar de Hypothesis vs. el witness que produce el shrinker contrafactual de refute. Para eso vamos a usar dos datasets clásicos: Adult Income (predicción de si una persona gana más o menos de 50K anuales) y COMPAS (predicción de reincidencia criminal). Ambos tienen sesgos conocidos respecto a sexo y raza, lo que hace fácil encontrar violaciones reales. En cada caso entrenamos un modelo (a definir) sin ninguna restricción de fairness.

Las propiedades a testear quedan pendientes de definir, entre las cuales podrían incluirse: monotonicidad y equidad individual.

Para cada combinación de modelo y propiedad, ejecutamos los dos pipelines y comparamos los witnesses que producen. Las métricas de comparación son: cuánto se aleja el witness del input original en distancia L1, cuántas features cambia, qué tan cerca está de un punto real del dataset de entrenamiento, y si el witness realmente sigue violando la propiedad o no.

Lo que esperamos ver es que el witness contrafactual cambia muchas menos features que el de Hypothesis y que está mucho más cerca del manifold de datos reales. Si eso se confirma consistentemente en los distintos modelos y propiedades, el resultado apoya la hipótesis central: que el shrinking semántico es estrictamente mejor que el sintáctico para este tipo de modelos.

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
