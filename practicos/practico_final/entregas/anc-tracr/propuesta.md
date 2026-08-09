# Integración de Adversarial Neural Cryptography y Tracr para la Interpretabilidad de Algoritmos de Cifrado

**Integrantes:** Juan Rodrigo Anabalón R.  
**Curso:** Explainable Artificial Intelligence. FAMAF. UNC  
**Fecha:** Mayo 2026  

---

## Objetivo
Explorar si los modelos compilados con **Tracr** pueden servir como referencia interpretativa para entender los mecanismos internos de **Adversarial Neural Cryptography (ANC)**.  
La hipótesis central es que Tracr puede representar versiones simplificadas de cifrados (XOR, sustitución, permutación) y permitir comparar su estructura explícita con los cifrados emergentes aprendidos por Alice y Bob en un entorno adversarial.

---

## Plan de actividades

| Tarea                   | Descripción                                                                 | S1 | S2 | S3 | S4 | S5 | S6 |
|--------------------------|-----------------------------------------------------------------------------|----|----|----|----|----|----|
| Revisión bibliográfica   | Estudiar trabajos de Abadi (2016) sobre ANC y DeepMind (2023) sobre Tracr   | X  | X  |    |    |    |    |
| Diseño RASP              | Codificar operaciones criptográficas básicas (XOR, sustitución) en RASP     |    | X  | X  |    |    |    |
| Compilación Tracr        | Generar modelos con pesos conocidos que implementen dichos cifrados         |    |    | X  | X  |    |    |
| Entrenamiento adversarial| Implementar Alice, Bob y Eve siguiendo el esquema de ANC                   |    |    |    | X  | X  |    |
| Comparación              | Analizar diferencias entre el cifrado emergente (ANC) y el explícito (Tracr)|    |    |    |    | X  |    |
| Informe final            | Elaborar documento con hallazgos y limitaciones                            |    |    |    |    |    | X  |

---

## Propuesta experimental
1. Implementar un cifrado básico (XOR con clave compartida) en RASP y compilarlo con Tracr.  
2. Entrenar redes Alice y Bob para cifrar/descifrar mensajes, y Eve como adversario, siguiendo el esquema de ANC.  
3. Comparar las operaciones emergentes de Alice y Bob con las operaciones explícitas compiladas en Tracr.  
4. Evaluar interpretabilidad: verificar si Tracr permite identificar las variables críticas (difusión de bits, dependencia de la clave) que en ANC aparecen de forma opaca.  

---

Este bosquejo busca tender un puente entre **interpretabilidad** y **Adversarial Neural Cryptography (ANC)**, mostrando cómo Tracr puede ser un laboratorio para explicar y analizar los mecanismos internos de la criptografía neuronal adversarial.

---

## Referencias
- Weiss, G., Goldberg, Y., & Yahav, E. (2021). *Thinking Like Transformers*. Proceedings of the 38th International Conference on Machine Learning (ICML), PMLR 139.  
- Lindner, D., Kramár, J., Farquhar, S., Rahtz, M., McGrath, T., & Mikulik, V. (2023). *Tracr: Compiled Transformers as a Laboratory for Interpretability*. NeurIPS 2023. arXiv:2301.05062.  
- Abadi, M., & Andersen, D. G. (2016). *Learning to Protect Communications with Adversarial Neural Cryptography*. arXiv:1610.06918.  


