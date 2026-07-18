### Entorno y Tecnologías Utilizadas

Este documento detalla el entorno subyacente y las librerías específicas requeridas para la ejecución de `DAG_GNN_Informalidad.ipynb` y `EDA_Informalidad.ipynb`.

**Información del Entorno (Kernel)**

* **Lenguaje:** Python
* **Versión Objetivo:** Python 3.x (Específicamente el kernel detectado es `python3` bajo el entorno de Google Colab).

**Librerías utilizadas**

El notebook importa e integra las siguientes bibliotecas del ecosistema científico y de aprendizaje automático de Python:

1. **Manipulación y Procesamiento de Datos**
* `pandas` (`pd`): Utilizada exhaustivamente para la ingesta de los archivos `.txt` crudos del INDEC, manipulación tabular, filtrado condicional de la población, creación de la variable target, agrupación y concatenación de los conjuntos de datos históricos.
* `numpy` (`np`): Utilizada para operaciones matemáticas de bajo nivel, reemplazo de valores faltantes (ej. `np.nan`), manejo de matrices numéricas, cálculos absolutos (`np.abs`) y manipulación directa de la matriz de adyacencia final extraída del modelo.
* `math`: Importada para cálculos matemáticos básicos (ej. redondeo de ejes para visualizaciones).


2. **Ingesta de Datos Externos**
* `requests`: Utilizada en la función `obtener_eph_indec` para realizar peticiones HTTP GET a los servidores del INDEC (gob.ar) y descargar los archivos comprimidos.
* `zipfile`: Permite manipular y extraer los archivos comprimidos (`.zip`) en memoria sin guardarlos en el disco físico.
* `io` (`io.BytesIO`): Utilizada en conjunto con `zipfile` y `requests` para gestionar el flujo de bytes descargados directamente en la memoria.


3. **Machine Learning y Preprocesamiento Tradicional**
* `scikit-learn` (`sklearn.preprocessing`):
* `LabelEncoder`: Usado en la fase de preprocesamiento para codificar los *strings* de las variables categóricas del EPH en enteros secuenciales, una condición estrictamente necesaria antes de pasarlos a las capas de embeddings.
* `StandardScaler`: Usado para normalizar (centrar en 0 y desvío estándar 1) las variables continuas como la edad y los ingresos, facilitando la convergencia estable del modelo durante el entrenamiento.




4. **Deep Learning e Inferencia Causal**
* `torch` (PyTorch): La librería central del notebook. Se utiliza para la creación y manipulación de tensores (`X_cat_tensor`, `X_cont_tensor`) y para enviar la computación a la GPU si está disponible.
* `torch.nn`: Utilizada para definir las arquitecturas orientadas a objetos. Permite crear la capa de reducción dimensional (`nn.Embedding`) y las capas lineales (`nn.Linear`) del Autoencoder Variacional (VAE) dentro de la clase `DAGGNN`.
* `torch.nn.functional` (`F`): Usada para aplicar funciones de activación no lineales (como `F.relu`) y funciones de pérdida (`F.mse_loss`).
* `torch.optim` (`optim`): Utilizada para inicializar y configurar el optimizador estocástico (se utiliza `Adam`) durante el ciclo de entrenamiento (*Training Loop*).
* `torch.utils.data` (`DataLoader`, `TensorDataset`): Importadas para gestionar y empaquetar los datos del INDEC en lotes (*batches*) durante el entrenamiento, evitando la sobrecarga de la memoria.


5. **Visualización de Datos y Análisis de Redes**
* `matplotlib.pyplot` (`plt`): Librería base para la renderización visual, configuración de figuras y de la interfaz gráfica.
* `seaborn` (`sns`): Utilizada para generar gráficos estadísticos estéticos durante la fase de Análisis Exploratorio (EDA).
* `networkx` (`nx`): Librería especializada en el análisis y modelado de sistemas de redes complejas. En el notebook, se utiliza en el bloque final para transformar las matrices matemáticas resultantes en grafos dirigidos (`DiGraph`), aplicarles algoritmos espaciales (`spring_layout`, `kamada_kawai_layout`) y renderizar los nodos y aristas causales con sus respectivos grosores según la fuerza calculada.
* `missingno` (`msno`): Importada como herramienta auxiliar común para la visualización y análisis de patrones de datos nulos/faltantes en *DataFrames*.
* `matplotlib.ticker`: Utilizada para el formateo de los ejes de visualización (ej. para evitar la notación científica en el gráfico de distribución de ingresos).
