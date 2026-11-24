# AlphaEarth en Colombia: Análisis Geoespacial con Google Earth Engine

Repositorio de scripts y ejemplos prácticos desarrollados en **Google Earth Engine (GEE)** para el análisis geoespacial, teledetección y modelado territorial en Colombia utilizando el dataset anual de embeddings satelitales **`GOOGLE/SATELLITE_EMBEDDING/V1/ANNUAL`** (10 m de resolución espacial).

## Contenido del repositorio

El repositorio reúne cuatro flujos de trabajo en JavaScript orientados a distintos enfoques de aprendizaje automático y análisis espacial sobre territorios colombianos:

* **`regression`** — **Estimación de densidad urbana mediante regresión Random Forest:**
  * **Área de estudio:** Rectángulo sobre Medellín y sus laderas (`[-75.65, 6.20, -75.50, 6.35]`), periodo `2025-01-01` a `2025-12-31`.
  * **Variable objetivo / referencia:** Probabilidad promedio de cobertura construida (`built`) derivada de `GOOGLE/DYNAMICWORLD/V1` en el mismo rango temporal.
  * **Metodología:** Muestreo de 2,000 píxeles a 10 m (`scale: 10`), entrenamiento de un clasificador `ee.Classifier.smileRandomForest(100)` en modo `'REGRESSION'` sobre todas las bandas de embeddings y generación de un mapa continuo de densidad de construcción (rango visualizado `0` a `0.8`).


* **`simmilarity search`** — **Búsqueda de similitud espectral/vectorial mediante similitud del coseno:**
  * **Área de estudio:** Ventana cuadrada de 3,000 m de radio (`queryPoint.buffer(3000).bounds()`) centrada en un punto de referencia (`[-77.09021546148223, 3.9051902785370505]`, zona costera/marítima del Pacífico colombiano), periodo `2024-01-01` a `2024-12-31`.
  * **Metodología:** Extracción del vector de embedding del píxel de consulta (`reduceRegion` con `ee.Reducer.first()` a 10 m convertido a imagen multibanda), cálculo explícito del producto punto, normas euclidianas de la imagen y del vector de consulta, y cociente de similitud del coseno ($\text{similitud} = \frac{\mathbf{A} \cdot \mathbf{B}}{\|\mathbf{A}\| \|\mathbf{B}\|}$).
  * **Visualización:** Mapa de similitud del coseno (`min: 0.90`, `max: 1`) orientado a detectar objetivos/estructuras similares al punto consultado (como embarcaciones/buques).

* **`SUPERVISED_CLASSIFICATION`** — **Clasificación supervisada de cobertura boscosa vs. deforestación:**
  * **Área de estudio:** Entorno de 5,000 m de radio alrededor de `[-72.76, 2.45]` (zona amazónica / entorno del Chiribiquete), periodo `2024-01-01` a `2024-12-31`.
  * **Datos de entrenamiento:** Combinación de las colecciones `Forest` (`class: 0`, 31 puntos) y `Deforested` (`class: 1`, 31 puntos) mediante `sampleRegions` a 10 m de resolución.
  * **Metodología:** Entrenamiento de un modelo `ee.Classifier.smileRandomForest(100)` sobre todas las bandas de `GOOGLE/SATELLITE_EMBEDDING/V1/ANNUAL` y clasificación binaria (`0`: bosque en verde `#228B22`, `1`: deforestación en rojo `#FF0000`).

* **`UNSUPERVISED_CLASIFICATION`** — **Agrupamiento no supervisado (K-Means) sobre el terreno:**
  * **Área de estudio:** Rectángulo sobre los Cerros Orientales de Bogotá (`[-74.05, 4.58, -73.98, 4.65]`), periodo `2023-01-01` a `2023-12-31`.
  * **Metodología:** Muestreo aleatorio de 5,000 píxeles a 10 m (`scale: 10`), entrenamiento de `ee.Clusterer.wekaKMeans(7)` para identificar 7 conglomerados a partir de los patrones de embeddings y visualización con una paleta de 7 colores (`paletaCerros`).

## Datasets y variables clave

| Recurso / Parámetro | Uso en los scripts |
| :--- | :--- |
| `GOOGLE/SATELLITE_EMBEDDING/V1/ANNUAL` | Capa base de embeddings satelitales (resolución de 10 m) usada en los 4 scripts. |
| `GOOGLE/DYNAMICWORLD/V1` (`built`) | Capa de referencia de probabilidad de área construida en `regression`. |
| `scale: 10` | Resolución espacial de muestreo y procesamiento coherente con la grilla de 10 m. |
| `ee.Classifier.smileRandomForest(100)` | Random Forest de 100 árboles en modo regresión (`regression`) y clasificación (`SUPERVISED_CLASSIFICATION`). |
| `ee.Clusterer.wekaKMeans(7)` | Algoritmo K-Means con 7 clústeres en `UNSUPERVISED_CLASIFICATION`. |

## Requisitos y guía de ejecución

1. Contar con una cuenta activa y un proyecto registrado en [Google Earth Engine](https://earthengine.google.com/) con acceso a `GOOGLE/SATELLITE_EMBEDDING/V1/ANNUAL`.
2. Abrir el **Code Editor** de Earth Engine (`code.earthengine.google.com`).
3. Copiar el contenido del script deseado desde este repositorio a un nuevo archivo en el Code Editor.
4. *(Opcional)* Ajustar coordenadas de `region` o `queryPoint`, rangos de fechas (`filterDate`), número de árboles/clústeres o umbral de visualización (`min`/`max`) según el caso de estudio.
5. Hacer clic en **Run** para visualizar las capas en el mapa interactivo.

## Contexto académico

Proyecto desarrollado en el marco del **Semillero de Investigación OSGeoLabUD** — Ingeniería Catastral y Geodesia, Universidad Distrital Francisco José de Caldas (Bogotá, Colombia).
