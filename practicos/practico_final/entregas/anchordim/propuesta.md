# Anchored dimension

## Integrantes
* Alejandro García

## Introducción

Los modelos de ML, y en particular las redes neuronales, no son naturalmente interpretables. En un intento por lograr algo de comprensión sobre los "criterios" que el modelo aprendió, se suelen analizar los pesos, las activaciones de capas intermedias y el efecto de variar las entradas.

Todo aquel que haya incursionado en el análisis de pesos y activaciones habrá sentido la desilución de no encontrar los claros patrones que esperaba. Hay (por lo menos) dos razones que lo explican:

**Dimensiones no alineadas con las características**. En nuestra imaginación esperamos que las activaciones sucedan en un espacio donde cada dimensión representa una característica útil y con semántica interpretable. Lamentablemente no es así, pero no necesariaente porque esas características no existan. Lo podemos pensar como una base canónica vs. una arbitraria en álgebra lineal. Aunque ambas son equivalentes, la segunda es muy difícil de interpretar. Cuando entrenamos un modelo, se encuenrta una de las posibles soluciones y generalmente en el método de entrenamiento no hay ningún mecanismo que busque alineación de las dimensiones (ejes) con las características.

**Superposición**. Las redes neuronales reutilizan las dimensiones de los embeddings para representar distintas características. Este efecto se llama superposición y, si bien es un efecto no deseado para interpretar la red, se vuelve necesario para obtener arquitecturas con pocos parámetros y evitar el sobreajuste.

En (Elhage et al., 2022) se puede ver una explicación de estas ideas y la demostración de que las redes neuronales superponen características.


## Trabajo propuesto

Analizar el comportamiento de un word embedding entrenado con un anclaje semántico, por ejemplo género. Siguiendo con el ejemplo, la hipótesis es que si el género, y solo el género, representa una dimensión, se podría medir qué componente de género tiene cada palabra. En un caso ideal y un mundo perfecto, la palabra "trabajo" tendría valor bajo (da lo mismo el género) y la palabra "embarazo" un componente alto en la dirección femenina.

La idea del anclaje ya existe y es general (cualquier modelo/tarea y técnica para lograrlo), pero hasta donde sé, no de la forma propuesta.

### Objetivo

Determinar si el anclaje de una dimensión de un word embedding es posible y representa patrones semánticos. Es decir, si las palabras codificadas utilizan esta dimensión de una forma interpretable.

En el caso de lograr resultados positivos, esta técnica permitiría mejorar la comprensión de las relaciones semánticas codificadas en los embeddings, las que claramente dependen del corpus de entrenamiento. Se podrían crear dimensiones de interés, por ejemplo para detectar sesgos existentes en los datos. 

Si bien no es el objetivo del trabajo, notar que el uso de embeddings con dimensiones ad-hoc, no solo podría facilitar el análisis/explicación de los LLM, sino también alterar su funcionamiento.

## Plan de actividades

* Buscar y elegir corpus (1 semana)
* Realizar experimentos (2 semanas)
* Analizar resultados y escribir artículo (2 semanas).

## Propuesta experimental

La idea es usar una red neuronal tipo word2vec (Skip-Gram o CBOW). Si el modelo fuera Skip-Gram y la dimensión "género":
* Elegir un conjunto de palabras frecuentes que representen a las dimensiones femenino y masculino.
* Fijar los pesos de la primer neurona de la capa oculta en 1 para las palabras femeninas, en -1 para las masculinas y en 0 para el resto. Esta neurona/dimensión representa el género. En este caso masculino y femenino son opuestos. Otra forma de representación sería una dimensión para cada género, lo que daría la posibilidad de que las palabras contengan distintas cantidades de cada uno.
* Entrenar el modelo partiendo de valores aleatorios para los pesos no fijados.
* La capa de salida tiene una neurona por palabra del vocabulario. El pimer peso de cada neurona indicaría el componente de género de esa palabra. Analizar los resultados.

## Referencias

Elhage, N., Hume, T., Olsson, C., Schiefer, N., Henighan, T., Kravec, S., ... & Olah, C. (2022). Toy models of superposition. arXiv preprint arXiv:2209.10652.


