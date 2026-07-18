
---

### Instrucciones para la Reproducibilidad Computacional del Notebook "DAG_GNN_Informalidad.ipynb"

Este documento detalla los pasos y requisitos necesarios para ejecutar y reproducir exitosamente los resultados del notebook `DAG_GNN_Informalidad.ipynb`.

**1. Requisitos del Entorno**

* **Entorno de Ejecución:** El notebook está diseñado para ejecutarse idealmente en un entorno basado en la nube como **Google Colab** (como lo indican los metadatos y comentarios del notebook). También puede ejecutarse localmente mediante Jupyter Notebook o JupyterLab.
* **Aceleración por Hardware:** Se recomienda utilizar un entorno con soporte de **GPU** (ej. en Google Colab: Entorno de ejecución -> Cambiar tipo de entorno de ejecución -> Acelerador de hardware: T4 GPU o superior). El código incluye la detección automática del dispositivo (`torch.device("cuda" if torch.cuda.is_available() else "cpu")`). La ejecución en CPU funcionará, pero el entrenamiento de la red neuronal será significativamente más lento.
* **Memoria RAM:** Se requiere un entorno con suficiente memoria RAM disponible para cargar y manipular los microdatos de la EPH, especialmente antes de realizar el *Label Encoding*.

**2. Instalación de Dependencias**

Antes de ejecutar el notebook, asegúrese de tener instaladas todas las librerías necesarias. En un entorno estándar de Colab, la mayoría están preinstaladas. Si ejecuta localmente, instálelas usando `pip`:

```bash
pip install pandas requests matplotlib seaborn missingno numpy scikit-learn torch networkx

```

**3. Obtención de Datos**

El notebook está programado para descargar automáticamente los conjuntos de datos requeridos directamente desde los servidores públicos del INDEC.

* **Requisito:** Se requiere una conexión a Internet durante la ejecución de la primera celda de código para descargar los archivos `.zip` de la EPH correspondientes a los cuartos trimestres de 2019, 2021 y 2025.
* *Nota funcional:* La función `obtener_eph_indec` maneja la descompresión en memoria y la extracción del archivo correspondiente a individuos.

**4. Secuencia de Ejecución**

Para reproducir los resultados, debe ejecutar las celdas de forma secuencial, desde arriba hacia abajo. El flujo lógico está estructurado de la siguiente manera:

* **Paso 1: Ingesta y Limpieza Inicial:** Se ejecutan las celdas para descargar los datos de los tres períodos, aplicar los filtros sociológicos (`ESTADO == 1` y `CAT_OCUP == 3`), definir la variable objetivo `Informal` y consolidar el *DataFrame* total (`df_t`).
* **Paso 2: Renombrado y Feature Engineering:** Se ejecuta la celda que traduce los códigos del INDEC a nombres de variables descriptivos (ej. `CH06` -> `Edad`). Luego, se ejecuta la celda que crea la variable binaria `Es_Remoto` a partir del lugar de trabajo.
* **Paso 3: Preprocesamiento de Variables (Label Encoding):** Transforma las variables categóricas (como Rama de Actividad, Región, etc.) en valores enteros continuos usando `scikit-learn`. También maneja la imputación de valores nulos (asignándoles el valor `-1`).
* **Paso 4: Inicialización de la Arquitectura PyTorch:** Ejecute las celdas que definen las clases de PyTorch: `EPHEmbeddings` (para la traducción semántica a vectores continuos) y `DAGGNN` (la arquitectura base de inferencia causal).
* **Paso 5: Entrenamiento del Modelo:** Ejecute el bloque de código correspondiente al "ENTRENAMIENTO COMPARATIVO".
* Este paso implementa un bucle sobre el diccionario de los tres períodos, entrenando un modelo DAG-GNN independiente para cada uno.
* *Advertencia de Rendimiento:* Esta es la parte computacionalmente más intensiva. Toma alrededor de 50 épocas por modelo. No detenga la celda hasta que vea el mensaje "¡Pipeline múltiple finalizado!".
* La función clave que asegura la reproducibilidad aquí es `fijar_semilla(seed=2026)`, la cual congela las fuentes de aleatoriedad del entorno de PyTorch.


* **Paso 6: Visualización de Resultados:** Este paso utiliza `NetworkX` y `Matplotlib` para visualizar las matrices latentes de 75x75 en forma de grafos (o ego-grafos) dirigidos y las dibuja en pantalla. Ajuste la variable `umbral_sensibilidad` si desea ver más o menos aristas en los grafos finales.

* **Paso 7: Obtención de métricas y gráficos comparativos:** Finalmente, de los grafos y ego-grafos resultantes del paso anterior, se obtienen métricas descriptivas de cada uno de ellos, gráficos comparativos e insights relevantes.
---
