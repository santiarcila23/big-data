# Análisis y Predicción de Precios de Vehículos Usados

**Integrantes:** Natalia Flores Pérez, Santiago Arcila Gutiérrez, Alejandro Restrepo Uribe

Proyecto del curso de Big Data — Institución Universitaria de Envigado (GITESI)
Dataset: *Vehicle Dataset from CarDekho* (`Car details v3.csv`), tomado de
[Kaggle](https://www.kaggle.com/datasets/nehalbirla/vehicle-dataset-from-cardekho).

## Contenido de la entrega

| Archivo | Qué es |
|---|---|
| `proyecto_vehiculos_usados.ipynb` | Notebook completo con las siete tareas, sin ejecutar (para correrlo ustedes) |
| `proyecto_EJECUTADO.ipynb` | El mismo notebook con todas las salidas y gráficos ya generados |
| `Conclusiones_no_tecnicas.docx` | Documento breve para el área comercial (entregable 2) |
| `Car details v3.csv` | Dataset, para que el notebook corra sin descargas |
| `README.md` | Este archivo |

## Cómo ejecutarlo

```bash
pip install pandas numpy matplotlib seaborn scikit-learn jupyter
jupyter notebook proyecto_vehiculos_usados.ipynb
```

El notebook corre de principio a fin con *Run All*. Tarda unos 4–5 minutos, casi todo en la
búsqueda de hiperparámetros de la Tarea 6. El CSV debe estar en la misma carpeta.

Todos los procesos aleatorios usan `RANDOM_STATE = 42`, así que los resultados son reproducibles:
cualquier integrante que lo ejecute debe obtener exactamente los mismos números.

## Resultados principales

**Regresión**  predicción del precio exacto (métricas sobre el conjunto de prueba, en rupias):

| Modelo | RMSE | R² |
|---|---|---|
| **Random Forest** | **139.870** | **0,916** |
| Ridge (alpha óptimo) | 146.834 | 0,907 |
| Lasso (alpha óptimo) | 146.992 | 0,907 |
| Regresión lineal (log) | 147.557 | 0,907 |
| Árbol de decisión (podado) | 161.808 | 0,888 |
| Regresión lineal (precio directo) | 270.210 | 0,686 |

**Clasificación** — rango de precio (Bajo / Medio / Alto):

| Modelo | Exactitud | F1 macro |
|---|---|---|
| **Gradient Boosting** | **0,837** | **0,839** |
| Random Forest | 0,836 | 0,837 |
| Regresión logística | 0,811 | 0,813 |
| Árbol de decisión | 0,799 | 0,800 |
| KNN (k=5) | 0,795 | 0,797 |

## Las cuatro decisiones que hay que poder defender

1. **Partición antes que todo.** Los cortes que definen las categorías de precio se calculan solo
   sobre el conjunto de entrenamiento. Calcularlos sobre el dataset completo filtraría
   información del test.
2. **Preprocesamiento dentro del `Pipeline`.** El escalador y el codificador se reajustan en cada
   pliegue de la validación cruzada. Escalar todo antes de validar infla las métricas.
3. **Objetivo en escala logarítmica.** Sube el R² de la regresión lineal de 0,686 a 0,907. Es la
   decisión que más impacto tuvo en todo el proyecto, por encima de cualquier elección de
   algoritmo.
4. **Escalar solo donde hace falta.** Modelos lineales y KNN sí; árboles y ensambles no, porque
   parten por umbrales y una transformación monótona no cambia ningún corte.

## Reparto de roles (rotativo)

El enunciado exige que todos puedan explicar cualquier parte. La rotación por bloques evita el
reparto por tareas, donde cada quien solo conoce lo suyo.

| Bloque | Días | Quién lidera | Qué hacen los demás |
|---|---|---|---|
| Datos: limpieza, EDA, reflexión de negocio | 1–5 | Integrante A | B y C ejecutan el notebook por su cuenta y anotan dudas |
| Modelado: regresión, clasificación, ensambles, optimización | 6–11 | Integrante B | A y C reproducen resultados y cuestionan decisiones |
| Comunicación: gráficos, conclusiones, documento | 12–15 | Integrante C | A y B ensayan explicando bloques que **no** lideraron |

Regla que hace que funcione: **en el ensayo, cada quien explica el bloque que no lideró.** Ahí se
detecta quién no entendió qué, con tiempo de arreglarlo.

## Limitación honesta declarada

Se eliminaron 1.202 filas duplicadas exactas suponiendo que eran el mismo aviso repetido en el
scraping. Pero el dataset no tiene identificador único, así que es posible que dos Maruti Swift
del mismo año, kilometraje redondeado y precio sean autos distintos. Si eran duplicados reales,
conservarlos habría inflado las métricas; si eran vehículos distintos, eliminarlos costó el 15%
de la muestra. La decisión se tomó priorizando el riesgo de fuga entre entrenamiento y prueba,
pero es defendible en ambos sentidos.
