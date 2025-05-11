# Data Transformation, Integrity and Feature Engineering

## 1. Elastic MapReduce (EMR) and Hadoop Overview

### 1.1. Introducción a Amazon EMR

Amazon EMR (Elastic MapReduce) es un framework de Hadoop administrado que se ejecuta sobre instancias EC2. Aunque su nombre incluye "MapReduce", EMR es mucho más que eso, ya que MapReduce como tecnología ha quedado algo obsoleta.

EMR incluye un ecosistema completo de herramientas de procesamiento de datos:
- Apache Spark
- HBase
- Presto
- Apache Flink
- Apache Hive
- Otros componentes complementarios

Además, EMR ofrece EMR Notebooks, similares a los Jupyter Notebooks, que permiten interactuar con el clúster de manera más visual e intuitiva.

### 1.2. Relevancia de EMR para Machine Learning

EMR es especialmente útil en machine learning cuando trabajamos con conjuntos de datos masivos que necesitan ser procesados antes de entrenar modelos. El procesamiento distribuido que ofrece EMR permite:

- Preparar grandes volúmenes de datos en paralelo
- Normalizar o escalar características más rápidamente
- Realizar transformaciones complejas de manera eficiente
- Distribuir la carga de procesamiento entre múltiples computadoras

Esto es crucial cuando un conjunto de datos es demasiado grande para ser procesado en una sola máquina, permitiendo preparar datos a escala para tareas de machine learning.

### 1.3. Arquitectura de un Clúster EMR

Un clúster de EMR está compuesto por instancias EC2 (nodos), cada una con un rol específico:

#### 1.3.1. Master Node (Nodo Maestro)
El cerebro del clúster que administra y coordina todo el procesamiento.

**Características principales**:
- Coordina la distribución de datos y tareas
- Monitorea el estado del clúster
- Todo clúster tiene exactamente un nodo maestro
- También conocido como "leader node"
- Puede existir como un clúster de un solo nodo

#### 1.3.2. Core Nodes (Nodos Core)
Nodos que realizan el procesamiento y también almacenan datos.

**Características principales**:
- Ejecutan tareas de procesamiento 
- Almacenan datos en HDFS (sistema de archivos de Hadoop)
- Todo clúster multi-nodo tiene al menos uno
- Son fundamentales para el funcionamiento del clúster
- Si fallan, pueden provocar pérdida de datos (aunque Hadoop los replica)

#### 1.3.3. Task Nodes (Nodos de Tarea)
Nodos dedicados exclusivamente al procesamiento.

**Características principales**:
- Solo ejecutan tareas, NO almacenan datos en HDFS
- Son opcionales en el clúster
- Pueden añadirse o quitarse sin afectar el almacenamiento
- Ideales para usar con instancias spot de EC2
- Aumentan la capacidad de procesamiento temporalmente

### 1.4. Tipos de Clústeres EMR

EMR ofrece dos enfoques principales para utilizar sus clústeres:

#### 1.4.1. Transient Cluster (Clúster Transitorio)
**Descripción**: Se termina automáticamente después de completar los pasos definidos.

**Flujo típico**:
1. Cargar datos de entrada
2. Procesar datos
3. Almacenar resultados
4. Terminar automáticamente

**Ventaja principal**: Ahorro de costos al no mantener recursos inactivos.

**Caso de uso**: Procesamiento periódico con pasos predefinidos.

#### 1.4.2. Long Running Cluster (Clúster de Larga Duración)
**Descripción**: Permanece activo hasta terminación manual.

**Características**:
- Permite interacción directa con aplicaciones
- Requiere terminación manual cuando ya no se necesita

**Casos de uso**:
- Análisis exploratorio de datos
- Consultas ad-hoc
- Experimentación con transformaciones

### 1.5. Interacción con un Clúster EMR

Al lanzar un clúster, se seleccionan los frameworks y aplicaciones según las necesidades. Después, se puede interactuar con él de tres formas principales:

1. **Conexión directa**: Acceso SSH al nodo maestro para ejecutar comandos desde terminal
2. **Envío de pasos**: Definir "steps" ordenados a través de la consola AWS
3. **EMR Notebooks**: Interfaz interactiva similar a Jupyter para análisis visual

### 1.6. Integración de EMR con Servicios AWS

EMR se integra estrechamente con otros servicios AWS:

| Servicio | Integración con EMR |
|----------|---------------------|
| **EC2** | Proporciona las instancias para los nodos |
| **VPC** | Aloja el clúster en una red virtual privada |
| **S3** | Almacena datos de entrada/salida de forma persistente |
| **CloudWatch** | Monitorea rendimiento y configura alarmas |
| **IAM** | Gestiona permisos y acceso al clúster |
| **CloudTrail** | Crea pistas de auditoría para solicitudes |
| **AWS Data Pipeline** | Programa y automatiza clústeres |

### 1.7. Opciones de Almacenamiento en EMR

EMR ofrece varias opciones de almacenamiento con diferentes características:

#### 1.7.1. HDFS (Hadoop Distributed File System)
**Descripción**: Sistema de archivos distribuido predeterminado de Hadoop.

**Funcionamiento**:
- Distribuye datos entre todos los nodos core
- Almacena múltiples copias para redundancia
- Divide archivos en bloques de 128MB por defecto

**Ventajas**:
- Alto rendimiento (datos locales en los nodos)
- Optimizado para localidad de datos

**Desventaja principal**: 
- Almacenamiento efímero (los datos se pierden al terminar el clúster)

#### 1.7.2. EMRFS (EMR File System)
**Descripción**: Permite usar S3 como si fuera un sistema HDFS.

**Características**:
- Proporciona almacenamiento persistente
- Ofrece buen rendimiento (aunque no tan rápido como HDFS)
- Tiene vista consistente opcional (usa DynamoDB)

**Ventaja principal**: Los datos persisten después de terminar el clúster.

#### 1.7.3. Sistema de Archivos Local
- Almacenamiento en nodos individuales
- No distribuido y efímero
- Útil para datos temporales o preparación en el nodo maestro

#### 1.7.4. HDFS respaldado por EBS
- Utiliza volúmenes EBS como almacenamiento para HDFS
- Proporciona mayor durabilidad dentro del ciclo de vida del clúster

### 1.8. Consideraciones Operativas

#### Modelo de costos
- Facturación por hora de EMR más cargos de EC2
- Importante optimizar duración y tamaño del clúster

#### Recuperación automática
- EMR aprovisiona nuevos nodos si un core node falla
- Proporciona resiliencia integrada

#### Escalabilidad dinámica
- Se pueden añadir/quitar task nodes en caliente
- Posibilidad de usar instancias spot para capacidad adicional
- Los core nodes también pueden redimensionarse en ejecución

---

## Resumen para el Examen

### Amazon EMR - Conceptos Clave
EMR es un servicio administrado para ejecutar frameworks de big data (Hadoop, Spark, etc.) en AWS. Es especialmente valioso para machine learning porque permite preparar grandes volúmenes de datos de forma distribuida antes de alimentarlos a algoritmos de ML.

### Tipos de Nodos en EMR
- **Master Node (obligatorio)**: Coordina el clúster, distribuye tareas y monitorea el estado
- **Core Nodes**: Procesan datos Y almacenan datos en HDFS
- **Task Nodes (opcionales)**: Solo procesan datos, no almacenan en HDFS, ideales para instancias spot

### Tipos de Clústeres
- **Transient**: Se terminan automáticamente tras completar los pasos definidos
- **Long Running**: Permanecen activos hasta terminación manual

### Opciones de Almacenamiento
- **HDFS**: Rápido pero efímero (los datos desaparecen al terminar el clúster)
- **EMRFS (S3)**: Persistente aunque algo más lento que HDFS
- **Sistema local**: Para uso temporal en nodos individuales
- **HDFS+EBS**: Mayor durabilidad durante la vida del clúster

### Escalabilidad y Gestión
- Task nodes pueden añadirse/quitarse sin afectar el almacenamiento
- EMR reaprovisiona automáticamente core nodes que fallan
- Posibilidad de redimensionar incluso los core nodes en ejecución

**Consejo para el examen**: Entiende bien la diferencia entre los tres tipos de nodos, especialmente que los task nodes no almacenan datos, lo que los hace ideales para instancias spot. Recuerda las ventajas de EMRFS (persistencia) frente a HDFS (rendimiento) y cómo esto afecta a la arquitectura de procesamiento de datos para machine learning.


## 2. EMR Serverless y EMR on EKS

### 2.1. EMR Serverless

#### 2.1.1. Concepto y diferenciación
EMR Serverless funciona con la misma idea que EMR tradicional, pero elimina el paso intermedio de definir la capacidad necesaria. EMR Serverless determina automáticamente cuántos nodos necesita para ejecutar las tareas, ajustando los recursos según las necesidades reales.

#### 2.1.2. Funcionamiento básico
Para utilizar EMR Serverless:
- Se elige el EMR release y runtime (Spark, Hive o Presto)
- Se envían consultas y scripts mediante job run requests
- No se interactúa directamente con un nodo maestro
- Se proporciona la ruta a scripts en S3 que el servicio ejecutará
- EMR gestiona automáticamente la capacidad subyacente

#### 2.1.3. Configuración de capacidad
Aunque EMR Serverless gestiona automáticamente los recursos, también permite:
- Especificar tamaños de worker predeterminados 
- Pre-inicializar la capacidad base
- Establecer límites máximos de recursos

Esta capacidad pre-inicializada ayuda a EMR a comenzar con recursos suficientes, pero el servicio sigue ajustando los trabajadores según sea necesario, programándolos y reprogramándolos dinámicamente.

#### 2.1.4. Ventajas principales
- **Eliminación de cálculos de capacidad**: No es necesario determinar cuántos workers se necesitan
- **Prevención de fallos por recursos insuficientes**: Evita errores comunes como "out of memory"
- **Enfoque en scripts y lógica**: Permite centrarse en el trabajo real en lugar de la infraestructura
- **Provisión automática**: Asigna automáticamente la capacidad necesaria para cada trabajo

#### 2.1.5. Limitaciones
- No es completamente "serverless" en el sentido tradicional
- Sigue utilizando tecnologías como Spark, Hive o Presto que requieren conocimiento de nodos
- Aún se requiere configuración a nivel de worker y driver para optimizaciones
- La diferencia es que no se necesita gestionar la cantidad o ubicación de los nodos

### 2.2. Uso de EMR Serverless

#### 2.2.1. Configuración inicial
1. **IAM User**: Crear un usuario IAM que interactuará con el servicio a través de AWS CLI
2. **Job Execution Role**: Configurar un rol de ejecución en IAM que proporcione permisos para:
   - Acceso a EMR Serverless
   - Scripts en S3
   - Datos en S3
   - AWS Glue (para metadatos si se usa Spark SQL)
   - Claves KMS (si se necesitan)

#### 2.2.2. Creación y ejecución de trabajos
Actualmente, EMR Serverless solo se puede usar a través de la CLI (Interfaz de Línea de Comandos), aunque se espera que en el futuro tenga soporte para consola y SDK.

**Ejemplo de comando para iniciar un trabajo**:
```
aws emr-serverless start-job-run \
  --application-id <application-id> \
  --execution-role-arn <role-arn> \
  --job-driver '{
    "sparkSubmit": {
      "entryPoint": "s3://bucket/script.py",
      "entryPointArguments": ["s3://bucket/output/"],
      "sparkSubmitParameters": "--conf spark.executor.cores=4 --conf spark.executor.memory=8g --conf spark.driver.cores=4 --conf spark.driver.memory=8g"
    }
  }' \
  --configuration-overrides '{
    "monitoringConfiguration": {
      "s3MonitoringConfiguration": {
        "logUri": "s3://bucket/logs/"
      }
    }
  }'
```

Aunque es "serverless", se pueden especificar parámetros como:
- Cores de ejecutor
- Memoria de ejecutor
- Cores de driver
- Memoria de driver

#### 2.2.3. Ciclo de vida de una aplicación EMR Serverless

1. **Creación**: Se crea la aplicación mediante un comando CLI
   - Si tiene éxito: la aplicación queda creada
   - Si falla: termina automáticamente

2. **Inicio**: Se emite un comando start para iniciar la aplicación

3. **Ejecución**: La aplicación se ejecuta y puede:
   - Tener éxito y pasar a fase de detención
   - Fallar y pasar a fase de detención automáticamente

4. **Detención**: La aplicación se detiene y puede:
   - Volver a iniciarse
   - Terminarse definitivamente

5. **Terminación**: La aplicación se elimina completamente

**Importante**: Estos pasos no ocurren automáticamente. Se deben invocar comandos a través de la API:
- create-application
- start-application
- stop-application
- delete-application

**Nota de precaución**: A diferencia de lo que algunos podrían pensar, EMR Serverless no elimina la necesidad de gestionar recursos. Aunque ajusta automáticamente la capacidad, sigue siendo necesario eliminar explícitamente la aplicación cuando ya no se necesita para evitar cargos innecesarios.

### 2.3. Capacidad Pre-inicializada en EMR Serverless

EMR Serverless permite especificar una capacidad inicial para optimizar el rendimiento:

**Ejemplo de configuración**:
```
--initial-capacity '{
  "driver": {
    "count": 5,
    "workerConfiguration": {
      "cpu": "4vCPU",
      "memory": "8GB"
    }
  },
  "executor": {
    "count": 50,
    "workerConfiguration": {
      "cpu": "4vCPU",
      "memory": "8GB"
    }
  }
}' \
--maximum-capacity '{
  "cpu": "400vCPU",
  "memory": "1024GB"
}'
```

Esta configuración:
- Establece 5 drivers iniciales con 4 vCPUs y 8GB de memoria cada uno
- Configura 50 executors iniciales con 4 vCPUs y 8GB de memoria cada uno
- Limita el uso máximo a 400 vCPUs y 1TB de memoria total

**Punto importante para el examen**: Spark añade un 10% de overhead a cualquier memoria solicitada para drivers y executors. Asegúrate de que tu solicitud de capacidad inicial tenga en cuenta este overhead.

### 2.4. Seguridad en EMR Serverless

EMR Serverless mantiene las mismas características de seguridad que EMR tradicional:

- **Almacenamiento**:
  - Utiliza EMRFS (S3) con todas sus capacidades de seguridad
  - Soporta cifrado del lado del servidor o cliente en S3 (at-rest)
  - Utiliza TLS para comunicación entre nodos y S3 (in-transit)

- **Almacenamiento efímero**:
  - Los discos locales están cifrados
  - La comunicación entre drivers y executors está cifrada automáticamente

- **Integración con Hive**:
  - La comunicación con AWS Glue Metastore está cifrada con TLS

**Nota adicional**: Si utilizas S3, puedes forzar HTTPS en el tráfico in-transit hacia S3 mediante la política AWS Secure Transport.

### 2.5. EMR on EKS (Elastic Kubernetes Service)

#### 2.5.1. Concepto y aplicación
EMR on EKS es otra aproximación serverless que permite ejecutar EMR y especialmente Apache Spark en clústeres de Kubernetes, sin preocuparse por aprovisionar la infraestructura.

#### 2.5.2. Ventajas principales
- **Integración con ecosistema Kubernetes**: Ideal para organizaciones que ya utilizan Kubernetes
- **Compartición de recursos**: Permite compartir recursos entre Spark y otras aplicaciones en el mismo clúster EKS
- **Eficiencia de recursos**: Mayor eficiencia en la utilización de hardware
- **Flexibilidad**: Posibilidad de ejecutar diferentes versiones de Spark junto con otras aplicaciones

#### 2.5.3. Funcionamiento
1. Se selecciona la versión de Apache Spark deseada
2. Se despliega la carga de trabajo de EMR en Amazon EKS
3. EMR empaqueta automáticamente la carga de trabajo en un contenedor
4. Proporciona conectores pre-construidos para integración con otros servicios AWS
5. Despliega el contenedor en el clúster EKS
6. Gestiona el escalado, logging y monitorización de la carga de trabajo

#### 2.5.4. Arquitectura
EMR on EKS opera a través de Amazon EKS, permitiendo que aplicaciones Spark coexistan con otras aplicaciones en el mismo clúster Kubernetes:

- Múltiples aplicaciones pueden compartir el mismo clúster
- Diferentes versiones de Spark pueden ejecutarse simultáneamente
- Los recursos se distribuyen eficientemente a través de Kubernetes
- El clúster puede extenderse a través de múltiples zonas de disponibilidad
- Mantiene integración con otros servicios AWS

---

## Resumen para el Examen

### EMR Serverless
- **Concepto clave**: EMR Serverless determina automáticamente la capacidad necesaria sin que el usuario especifique el número de nodos
- **Funcionamiento**: Se selecciona el runtime (Spark, Hive, Presto) y se envían trabajos como job run requests a través de scripts almacenados en S3
- **Ciclo de vida**: Requiere comandos explícitos para crear, iniciar, detener y eliminar aplicaciones (no es completamente automático)
- **Capacidad**: Aunque es "serverless", permite pre-inicializar capacidad y establecer límites máximos
- **Importante**: Spark añade un 10% de overhead a la memoria solicitada para drivers y executors
- **Gestión de recursos**: Aún es necesario eliminar explícitamente las aplicaciones cuando no se necesitan para evitar cargos

### EMR on EKS
- **Concepto clave**: Ejecuta EMR (principalmente Spark) en clústeres Kubernetes sin gestionar la infraestructura
- **Caso de uso**: Ideal para organizaciones que ya utilizan Kubernetes y quieren integrar Spark
- **Ventaja principal**: Permite compartir recursos eficientemente entre Spark y otras aplicaciones
- **Funcionamiento**: EMR empaqueta automáticamente la carga de trabajo en contenedores y los despliega en el clúster EKS

**Consejo para el examen**: Presta especial atención a las diferencias entre EMR tradicional, EMR Serverless y EMR on EKS. Recuerda que aunque EMR Serverless gestiona automáticamente la capacidad, sigue siendo necesario gestionar el ciclo de vida completo de la aplicación mediante comandos explícitos. No olvides el 10% de overhead de memoria que Spark añade a los recursos solicitados para drivers y executors.


## 3. Apache Hadoop y Spark en EMR

### 3.1. Componentes de Hadoop

Hadoop es un framework fundamental en el ecosistema de procesamiento distribuido y constituye la base de EMR. Consta de varios módulos clave:

#### 3.1.1. Hadoop Core (Hadoop Common)
- Base de bibliotecas y utilidades necesarias para que funcionen los demás módulos
- Proporciona archivos JAR, scripts de arranque y utilidades a nivel de sistema operativo
- Permite a Hadoop funcionar independientemente del sistema operativo subyacente

#### 3.1.2. HDFS (Hadoop Distributed File System)
- Sistema de archivos distribuido y escalable para Hadoop
- Distribuye los datos a través de las instancias del clúster
- Almacena múltiples copias de datos en diferentes instancias para garantizar la tolerancia a fallos
- Características principales:
  - Almacenamiento efímero (se pierde al terminar el clúster)
  - Útil para cachear resultados intermedios
  - Eficiente para cargas de trabajo con E/S aleatoria significativa

#### 3.1.3. YARN (Yet Another Resource Negotiator)
- Introducido en Hadoop 2.0
- Administra centralmente los recursos del clúster 
- Permite utilizar múltiples frameworks de procesamiento de datos (no solo MapReduce)
- Facilita la integración con otras tecnologías como Spark

#### 3.1.4. MapReduce
- Framework para escribir aplicaciones que procesan grandes volúmenes de datos en paralelo
- Basado en conceptos originados en Google para procesamiento de big data
- Consta de dos tipos de funciones principales:
  1. **Map Functions**: Transforman datos en pares clave-valor (resultados intermedios)
     - Transforman, reformatean o extraen datos necesarios
     - Relevantes para análisis exploratorio de datos
  2. **Reduce Functions**: Combinan resultados intermedios y producen la salida final
     - Agregan datos y los destilan a una respuesta final

### 3.2. Apache Spark: Alternativa Moderna a MapReduce

#### 3.2.1. Visión General de Spark
- Sistema de procesamiento distribuido de código abierto para cargas de trabajo de big data
- Ha reemplazado en gran medida a MapReduce como la tecnología preferida
- Puede instalarse opcionalmente en clústeres EMR
- Utiliza caché en memoria y ejecución de consultas optimizada

#### 3.2.2. Ventajas de Spark sobre MapReduce
- Utiliza un Grafo Acíclico Dirigido (DAG) para optimizar el procesamiento
- Es más inteligente al gestionar dependencias y programar tareas
- Ofrece mayor velocidad gracias al procesamiento en memoria
- Proporciona APIs para múltiples lenguajes: Java, Scala, Python y R
- Permite reutilización de código entre diferentes tipos de cargas de trabajo

#### 3.2.3. Casos de uso de Spark
- Análisis interactivos
- Procesamiento en tiempo real
- Aprendizaje automático
- Procesamiento de grafos
- Consultas SQL 

**No recomendado para**:
- OLTP (Procesamiento de Transacciones En Línea)
- Trabajos tradicionales de procesamiento por lotes

### 3.3. Arquitectura de Spark

#### 3.3.1. Componentes clave
- **Driver Program**: Contiene el objeto SparkContext y define el código principal
- **Cluster Managers**: Asignan recursos (Yarn, Mesos o el gestor propio de Spark)
- **Executors**: Procesos que ejecutan los cálculos y almacenan datos en los nodos

#### 3.3.2. Flujo de trabajo
1. Las aplicaciones Spark se ejecutan como procesos independientes en un clúster
2. El SparkContext del programa principal coordina estos procesos
3. SparkContext se conecta a los gestores de clúster para obtener recursos
4. Spark adquiere executors en los nodos del clúster
5. El código de la aplicación se envía a los executors
6. SparkContext envía tareas a los executors para su ejecución

### 3.4. Componentes Principales de Spark

#### 3.4.1. Spark Core
- Fundamento de la plataforma
- Gestiona memoria, recuperación ante fallos, programación y distribución de tareas
- Proporciona APIs para varios lenguajes
- Implementa el concepto de RDD (Resilient Distributed Dataset):
  - Colección lógica de datos particionados entre nodos
  - Base de las operaciones distribuidas en Spark

#### 3.4.2. Spark SQL
- Motor de consultas distribuido que proporciona consultas interactivas
- Hasta 100 veces más rápido que MapReduce
- Características principales:
  - Optimizador basado en costos
  - Almacenamiento columnar
  - Generación de código para consultas rápidas
  - Compatibilidad con diversas fuentes de datos (JDBC, ODBC, JSON, HDFS, Hive, ORC, Parquet)
  - Soporte para consultas a tablas Hive usando HiveQL

- **Conceptos clave**:
  - **DataFrame** (Python) / **DataSet** (Scala): Abstracción que reemplaza a los RDD
  - Permite interactuar con datos de forma similar a pandas o tablas relacionales
  - Posibilidad de ejecutar comandos SQL contra el clúster de Spark

#### 3.4.3. Spark Streaming
- Solución en tiempo real que aprovecha las capacidades de programación rápida de Spark Core
- Ingiere datos en mini-lotes y realiza análisis en tiempo real
- Permite aplicar el mismo código de análisis por lotes a procesamiento en tiempo real
- Soporta ingesta desde:
  - Twitter
  - Kafka
  - Flume
  - HDFS
  - ZeroMQ
  - AWS Kinesis (integración específica)

#### 3.4.4. MLlib (Machine Learning Library)
- Biblioteca de aprendizaje automático distribuido para Spark
- Implementaciones de algoritmos diseñadas específicamente para entornos distribuidos
- **Algoritmos disponibles**:
  - **Clasificación**: Regresión logística, Naive Bayes
  - **Regresión**: Árboles de decisión
  - **Recomendación**: Alternating Least Squares (ALS)
  - **Clustering**: K-means
  - **Modelado de tópicos**: LDA (Latent Dirichlet Allocation)
  - **Utilidades de flujo de trabajo**: Pipelines, transformación de características, persistencia
  - **Álgebra lineal distribuida**: SVD, PCA
  - **Funciones estadísticas**

- **Ventaja principal**: Estos algoritmos están implementados de forma que sean escalables y distribuibles, a diferencia de las implementaciones tradicionales (como scikit-learn) que no funcionan nativamente en clústeres.

#### 3.4.5. GraphX
- Framework de procesamiento de grafos distribuido
- Construido sobre Spark
- Proporciona ETL y análisis exploratorio para estructuras de datos de grafos
- Permite construir y transformar estructuras de grafos a gran escala
- **Nota**: No se refiere a gráficos visuales, sino a estructuras de datos de grafos (como redes sociales)

### 3.5. Características Avanzadas de Spark

#### 3.5.1. Structured Streaming
- En Spark Streaming, los datos entrantes se modelan como una tabla de base de datos sin límites
- La tabla "crece" a medida que llegan nuevos datos en tiempo real
- Se pueden realizar consultas usando ventanas de tiempo (por ejemplo, la última hora de datos)
- **Integración con Kinesis**:
  - Los productores de Kinesis publican datos a un stream de Kinesis
  - Spark puede crear un DataFrame basado en ese stream
  - Se puede consultar como cualquier otro conjunto de datos de Spark

#### 3.5.2. Zeppelin Notebooks
- Entorno de notebook para Spark similar a Jupyter
- Permite ejecutar código Spark interactivamente en un navegador
- Funcionalidades principales:
  - Ejecución de consultas SQL contra datos de Spark
  - Visualización de resultados con bibliotecas como matplotlib y seaborn
  - Experiencia similar a herramientas de ciencia de datos
  - Facilita el preprocesamiento de datos en un formato familiar para científicos de datos

---

## Resumen para el Examen

### Hadoop y sus Componentes
- **Hadoop Core**: Bibliotecas y utilidades base
- **HDFS**: Sistema de archivos distribuido (datos efímeros, múltiples copias)
- **YARN**: Gestor de recursos (permite usar frameworks además de MapReduce)
- **MapReduce**: Modelo de programación con funciones Map (transformación) y Reduce (agregación)

### Apache Spark
- **Concepto clave**: Framework de procesamiento distribuido que sustituye a MapReduce, más rápido gracias al procesamiento en memoria
- **Arquitectura**: Driver Program (SparkContext) → Cluster Managers → Executors en los nodos
- **Ventajas**: Uso de DAG para optimización, APIs para múltiples lenguajes, reutilización de código

### Componentes de Spark
| Componente | Función | Características clave |
|------------|---------|------------------------|
| **Spark Core** | Base de la plataforma | RDDs, gestión de memoria, recuperación ante fallos |
| **Spark SQL** | Motor de consultas | DataFrames/DataSets, consultas 100x más rápidas que MapReduce |
| **Spark Streaming** | Análisis en tiempo real | Mini-lotes, integración con Kinesis, ventanas temporales |
| **MLlib** | Machine Learning | Algoritmos distribuidos (clasificación, regresión, clustering) |
| **GraphX** | Procesamiento de grafos | Análisis de estructuras de datos tipo grafo |

### MLlib - Algoritmos Disponibles
- **Clasificación**: Regresión logística, Naive Bayes
- **Regresión**: Árboles de decisión
- **Recomendación**: ALS (Alternating Least Squares)
- **Clustering**: K-means
- **Modelado de tópicos**: LDA
- **Álgebra lineal distribuida**: SVD, PCA

### Herramientas Adicionales
- **Zeppelin**: Notebooks interactivos para Spark (similar a Jupyter)

**Consejo para el examen**: Comprende bien la relación entre Hadoop y Spark. Recuerda que Spark funciona sobre YARN (componente de Hadoop) pero reemplaza a MapReduce como modelo de procesamiento. Presta especial atención a MLlib y los algoritmos específicos que ofrece, ya que son relevantes para el examen de Machine Learning. Entiende también cómo Spark Streaming modela los datos como una tabla sin límites que crece con el tiempo, lo que permite consultas en ventanas temporales.

## 4. Feature Engineering and the Curse of Dimensionality

### 4.1. Conceptos Fundamentales de Feature Engineering

Feature Engineering (Ingeniería de Características) es el proceso de aplicar conocimiento sobre los datos para seleccionar, transformar o crear características que mejorarán el rendimiento de los modelos de machine learning.

#### 4.1.1. ¿Qué son las características (features)?
Las características son los atributos o variables de los datos de entrenamiento que se utilizan para entrenar un modelo. Por ejemplo, si estamos intentando predecir el ingreso de una persona, las características podrían incluir:
- Edad
- Altura
- Peso
- Dirección
- Tipo de vehículo
- Nivel educativo

Estos atributos son las "entradas" que el modelo utilizará para hacer predicciones.

#### 4.1.2. Importancia del Feature Engineering
El Feature Engineering representa la verdadera "arte" del machine learning por varias razones:
- No se puede simplemente introducir todos los datos disponibles y esperar buenos resultados
- Distingue a los buenos profesionales de ML de los menos experimentados
- Se aprende principalmente a través de la experiencia y la práctica
- Es un foco importante en el examen AWS Certified Machine Learning - Associate

#### 4.1.3. Procesos clave en Feature Engineering
Feature Engineering abarca varios procesos fundamentales:

1. **Selección de características**: Identificar qué características son relevantes para la predicción que estamos intentando realizar
   
2. **Transformación de características**: Modificar características existentes para hacerlas más útiles para algoritmos específicos
   - Normalización
   - Escalado
   - Codificación 

3. **Manejo de datos faltantes**: Decidir cómo tratar información incompleta
   - Eliminación
   - Imputación (reemplazar con valores como la media, mediana, etc.)
   - Predicción de valores faltantes

4. **Creación de características**: Generar nuevas características a partir de las existentes
   - Aplicar transformaciones matemáticas (logaritmos, cuadrados, etc.)
   - Combinar características existentes
   - Crear características basadas en conocimiento del dominio

### 4.2. La Maldición de la Dimensionalidad

#### 4.2.1. ¿Qué es la maldición de la dimensionalidad?
La maldición de la dimensionalidad se refiere al conjunto de problemas que surgen cuando se trabaja con datos en espacios de alta dimensión (es decir, con muchas características).

#### 4.2.2. Problema de representación vectorial
Cada instancia de datos puede representarse como un vector en un espacio multidimensional:
- Con una característica (ej: edad): vector en una dimensión (línea)
- Con dos características (ej: edad y altura): vector en dos dimensiones (plano)
- Con tres características (ej: edad, altura e ingresos): vector en tres dimensiones (espacio)
- Con más características: vector en espacios de mayor dimensión (difíciles de visualizar)

#### 4.2.3. Consecuencias de la alta dimensionalidad

1. **Datos dispersos**: A medida que aumentan las dimensiones, el espacio disponible crece exponencialmente, haciendo que los datos se vuelvan más "dispersos"

2. **Dificultad para encontrar soluciones óptimas**: En espacios más grandes, resulta mucho más difícil ubicar la solución óptima

3. **Problemas de rendimiento**: Los modelos con muchas características pueden volverse:
   - Computacionalmente costosos
   - Difíciles de entrenar (convergencia lenta)
   - Propensos al sobreajuste

4. **Mayor complejidad de modelos**: Por ejemplo, una red neuronal tendría que ser extremadamente amplia y profunda para manejar todas las relaciones entre numerosas características

### 4.3. Técnicas para Reducción de Dimensionalidad

Para combatir la maldición de la dimensionalidad, existen métodos que permiten reducir el número de características mientras se preserva la información esencial:

#### 4.3.1. Métodos basados en conocimiento del dominio
- Selección manual de características importantes
- Eliminación de características redundantes o irrelevantes
- Creación de características compuestas basadas en experiencia

#### 4.3.2. Principal Component Analysis (PCA)
- Técnica no supervisada que reduce dimensionalidad
- Transforma las características originales en un conjunto más pequeño de componentes principales
- Cada componente principal captura la máxima variabilidad posible
- Las nuevas características (componentes) no son interpretables directamente como las originales
- Útil cuando se tienen muchas características potencialmente correlacionadas

#### 4.3.3. K-means Clustering
- Puede utilizarse para agrupar características similares
- Permite identificar patrones subyacentes en los datos
- Ayuda a crear representaciones más compactas de los datos

#### 4.3.4. Enfoque iterativo
En la práctica, el feature engineering suele ser un proceso iterativo:
1. Seleccionar un conjunto inicial de características
2. Entrenar el modelo
3. Evaluar el rendimiento
4. Modificar las características (añadir, eliminar, transformar)
5. Repetir hasta lograr un rendimiento satisfactorio

### 4.4. Consideraciones Prácticas

#### 4.4.1. Balance entre exhaustividad y dimensionalidad
Es necesario encontrar un equilibrio entre:
- Incluir suficiente información para que el modelo sea preciso
- Evitar demasiadas características que puedan perjudicar el rendimiento

#### 4.4.2. Importancia del conocimiento del dominio
El conocimiento específico sobre el problema que se está tratando es fundamental para:
- Identificar qué características son potencialmente relevantes
- Transformar características de manera que tengan más sentido para el problema
- Crear nuevas características que capturen relaciones importantes

#### 4.4.3. Experimentación y validación
- Probar diferentes conjuntos de características y transformaciones
- Utilizar validación cruzada para evaluar el impacto de los cambios
- Medir objetivamente qué enfoques producen mejores resultados

---

## Resumen para el Examen

### Feature Engineering - Conceptos Clave
- **Definición**: Proceso de seleccionar, transformar y crear características para mejorar modelos de ML
- **Importancia**: Elemento crítico que distingue a los buenos profesionales de ML; foco importante en el examen
- **Procesos fundamentales**:
  - Selección de características relevantes
  - Transformación de datos (normalización, escalado)
  - Manejo de datos faltantes
  - Creación de nuevas características

### La Maldición de la Dimensionalidad
- **Concepto**: Problemas que surgen al trabajar con datos de alta dimensionalidad
- **Consecuencias**:
  - Datos más dispersos en espacios más grandes
  - Mayor dificultad para encontrar soluciones óptimas
  - Rendimiento computacional reducido
  - Modelos más complejos y difíciles de entrenar

### Técnicas de Reducción
- **Basadas en conocimiento**: Selección manual de características importantes
- **PCA**: Transforma características originales en componentes principales que maximizan varianza
- **K-means**: Agrupa características similares para crear representaciones más compactas
- **Proceso iterativo**: Seleccionar → Entrenar → Evaluar → Modificar → Repetir

**Consejo para el examen**: El Feature Engineering es un tema que puede aparecer tanto en preguntas teóricas como en escenarios prácticos del examen. Comprende bien por qué "más características" no siempre significa "mejor modelo" debido a la maldición de la dimensionalidad. Recuerda que las técnicas de reducción como PCA y K-means son especialmente útiles cuando los datos tienen muchas dimensiones, pero que el conocimiento del dominio es igualmente importante para seleccionar las características relevantes. Familiarízate con las diferentes formas de manejar datos faltantes, ya que este es un problema común en conjuntos de datos del mundo real.


## 5. Imputing Missing Data

En el mundo real, los conjuntos de datos rara vez están completos. Para cada observación suele haber algunos valores faltantes, lo que plantea un desafío significativo en machine learning. La imputación de datos faltantes es un componente crucial del feature engineering.

### 5.1. Técnicas Básicas de Imputación

#### 5.1.1. Mean Replacement (Sustitución por la Media)

Este método sustituye los valores faltantes con la media de todos los valores existentes para esa característica (columna).

**Proceso**:
- Calcular la media de todos los valores no faltantes en la columna
- Sustituir cada valor faltante con esa media

**Ventajas**:
- Rápido y sencillo de implementar
- No afecta la media general del conjunto de datos
- Preserva el tamaño de la muestra

**Desventajas**:
- No considera relaciones entre características
- Sensible a valores atípicos (outliers)
- No aplicable a datos categóricos
- Generalmente produce resultados menos precisos que métodos más avanzados

**Consideración importante**: Si hay muchos outliers, usar la mediana en lugar de la media puede ser más apropiado, ya que la mediana es menos sensible a valores extremos.

#### 5.1.2. Dropping Missing Data (Eliminación de Datos Faltantes)

Esta técnica simplemente elimina las filas (observaciones) que contienen valores faltantes.

**Ventajas**:
- Extremadamente simple (puede hacerse con una línea de código)
- Rápido de implementar

**Desventajas**:
- Reduce el tamaño de la muestra
- Puede introducir sesgo si los datos faltantes tienen algún patrón
- Puede eliminar información valiosa
- No recomendado si hay muchos datos faltantes

**Ejemplo de sesgo potencial**: Si personas con ingresos muy altos o muy bajos tienden a no reportar sus ingresos, eliminar estas filas alteraría la distribución de ingresos en el conjunto de datos.

#### 5.1.3. Sustitución por Categoría Más Frecuente

Para datos categóricos, una alternativa a la media es usar el valor (categoría) más frecuente.

**Proceso**:
- Identificar la categoría que aparece con mayor frecuencia en la columna
- Sustituir los valores faltantes con esta categoría

**Ventajas y desventajas**: Similares a la sustitución por la media, pero aplicable a datos categóricos.

#### 5.1.4. Sustitución por Campo Similar

En algunos casos, puede ser apropiado usar un campo relacionado para sustituir valores faltantes.

**Ejemplo**: En un conjunto de datos de reseñas de películas, si falta el texto completo de la reseña pero existe un resumen, se podría usar el resumen para sustituir el texto completo.

### 5.2. Técnicas Avanzadas de Imputación

#### 5.2.1. K-Nearest Neighbors (KNN)

KNN es un método que aprovecha las relaciones entre características para imputar valores faltantes.

**Proceso**:
- Identificar las K observaciones más similares (vecinos más cercanos) a la observación con datos faltantes
- Promediar los valores de esa característica de los K vecinos más cercanos
- Usar ese promedio para sustituir el valor faltante

**Ventajas**:
- Considera relaciones entre características
- Generalmente más preciso que métodos simples como la media
- Funciona bien para datos numéricos

**Desventajas**:
- Computacionalmente más costoso
- Requiere definir una métrica de distancia apropiada
- Menos efectivo para datos categóricos
- Necesita normalización de datos para funcionar correctamente

#### 5.2.2. Deep Learning para Imputación

Los modelos de deep learning (aprendizaje profundo) pueden ser particularmente efectivos para imputar datos categóricos.

**Proceso**:
- Entrenar una red neuronal para predecir los valores faltantes basándose en las demás características
- Utilizar este modelo para predecir e imputar valores faltantes

**Ventajas**:
- Extremadamente preciso, especialmente para datos categóricos
- Puede capturar relaciones complejas y no lineales entre características

**Desventajas**:
- Alta complejidad de implementación
- Requiere considerable poder computacional
- Necesita ajuste fino de hiperparámetros

#### 5.2.3. Multiple Regression (Regresión Múltiple)

Este enfoque utiliza técnicas de regresión para predecir valores faltantes basándose en otras características.

**Proceso**:
- Identificar relaciones entre la característica con valores faltantes y otras características
- Construir un modelo de regresión usando estas relaciones
- Predecir los valores faltantes utilizando el modelo

**Ventajas**:
- Puede capturar relaciones lineales y no lineales
- Generalmente más preciso que métodos básicos
- Menos complejo que deep learning

**Desventajas**:
- Asume relaciones específicas entre variables
- Puede ser sensible a outliers
- Menos efectivo si hay poca correlación entre características

#### 5.2.4. MICE (Multiple Imputation by Chained Equations)

MICE es considerado el estado del arte en técnicas de imputación.

**Proceso**:
- Imputar inicialmente los valores faltantes usando un método simple (como la media)
- Para cada característica con valores faltantes:
  - Marcar los valores imputados previamente
  - Entrenar un modelo de regresión usando otras características
  - Reemplazar los valores imputados con predicciones de este modelo
- Repetir este proceso varias veces para refinar las imputaciones

**Ventajas**:
- Alta precisión
- Considera las relaciones entre todas las características
- Maneja bien tanto datos numéricos como categóricos

**Desventajas**:
- Computacionalmente intensivo
- Complejo de implementar
- Puede ser lento para conjuntos de datos grandes

### 5.3. Consideraciones Prácticas

#### 5.3.1. Evitar el Sesgo
Antes de aplicar cualquier técnica de imputación, es importante analizar si hay patrones en los datos faltantes que podrían sesgar los resultados.

**Preguntas clave**:
- ¿Hay alguna relación entre valores faltantes y otras características?
- ¿Hay algún grupo demográfico o categoría que tiende a tener más datos faltantes?
- ¿Los datos faltantes son aleatorios o siguen algún patrón?

#### 5.3.2. Método Óptimo: Obtener Más Datos
En última instancia, la mejor solución para el problema de datos faltantes es obtener más datos completos y de mejor calidad.

**Consideraciones**:
- Mejorar procesos de recolección de datos
- Identificar y corregir fuentes de datos incompletos
- Diseñar sistemas que minimicen la posibilidad de datos faltantes

#### 5.3.3. Elección del Método de Imputación
La elección del método de imputación depende de varios factores:
- Tipo de datos (numéricos vs. categóricos)
- Cantidad de datos faltantes
- Relaciones entre características
- Recursos computacionales disponibles
- Tiempo disponible para la implementación

---

## Resumen para el Examen

### Métodos de Imputación de Datos Faltantes

| Método | Descripción | Pros | Contras | Mejor para |
|--------|-------------|------|---------|------------|
| **Mean Replacement** | Sustituye valores faltantes con la media de la columna | Simple, rápido, preserva la media | No considera relaciones, sensible a outliers | Aproximación rápida inicial |
| **Median Replacement** | Sustituye con la mediana | Menos sensible a outliers que la media | Similar a mean replacement | Datos con outliers |
| **Dropping Data** | Elimina filas con valores faltantes | Extremadamente simple | Reduce muestra, puede introducir sesgo | Pocos datos faltantes |
| **KNN** | Usa valores de observaciones similares | Considera relaciones entre características | Costoso computacionalmente | Datos numéricos |
| **Deep Learning** | Predice valores con redes neuronales | Muy preciso, maneja relaciones complejas | Complejo, computacionalmente intensivo | Datos categóricos |
| **Multiple Regression** | Usa regresión para predecir valores | Captura relaciones entre variables | Asume relaciones específicas | Datos con correlaciones claras |
| **MICE** | Método iterativo de imputación encadenada | Estado del arte, alta precisión | Complejo, computacionalmente intensivo | Soluciones robustas |

### Puntos Clave a Recordar
- La imputación de datos es un componente crítico del feature engineering
- Los métodos simples (media, mediana) son rápidos pero generalmente menos precisos
- Dropping data puede introducir sesgo si los datos faltantes siguen patrones
- KNN funciona bien para datos numéricos, capturando relaciones entre características
- Deep Learning es especialmente efectivo para datos categóricos
- MICE es considerado el estado del arte en imputación
- La mejor solución es obtener más datos completos y de calidad cuando sea posible

**Consejo para el examen**: Para preguntas sobre el "mejor" método de imputación, los métodos avanzados como MICE o Deep Learning suelen ser las respuestas correctas en términos de precisión, mientras que Mean Replacement o Dropping Data rara vez serán la mejor opción, aunque sean las más simples de implementar. Recuerda que el método óptimo depende del contexto, pero en general, cualquier método que considere las relaciones entre características será superior a los métodos que tratan cada característica aisladamente.


## 6. Dealing with Unbalanced Data

### 6.1. Comprendiendo los Datos No Balanceados

#### 6.1.1. Definición
Los datos no balanceados (unbalanced data) ocurren cuando existe una gran disparidad entre el número de casos positivos y negativos en un conjunto de datos de entrenamiento. Este es un problema común en situaciones donde lo que intentamos detectar es un evento raro o poco frecuente.

#### 6.1.2. Ejemplo típico: Detección de fraude
En detección de fraude, las transacciones fraudulentas suelen representar un porcentaje muy pequeño del total (quizás 0.01%). Esto significa que por cada caso fraudulento, puede haber miles de casos legítimos.

#### 6.1.3. El problema con los datos no balanceados
Cuando un modelo de machine learning se entrena con datos extremadamente no balanceados, puede:
- Favorecer la clase mayoritaria, simplemente porque tiene más ejemplos para aprender
- Desarrollar un sesgo hacia predecir siempre la clase mayoritaria
- Aparentar tener alta precisión general, cuando en realidad está fallando en detectar la clase minoritaria

**Ejemplo**: Un modelo que siempre predice "no fraude" tendría un 99.99% de precisión en un conjunto de datos donde solo el 0.01% de las transacciones son fraudulentas, pero sería completamente inútil para detectar fraudes.

#### 6.1.4. Aclaración terminológica
En machine learning, los términos "positivo" y "negativo" no implican juicios de valor:
- **Positivo**: Se refiere al caso que estamos tratando de detectar (por ejemplo, fraude)
- **Negativo**: Se refiere a la ausencia del caso que estamos detectando (por ejemplo, no fraude)

Así, aunque el fraude es algo negativo en términos de valor, en el contexto de machine learning sería el "caso positivo" si es lo que tratamos de detectar.

### 6.2. Técnicas para Manejar Datos No Balanceados

#### 6.2.1. Oversampling (Sobremuestreo)
Consiste en aumentar artificialmente el número de casos de la clase minoritaria.

**Método simple de oversampling**:
- Duplicar aleatoriamente ejemplos de la clase minoritaria
- Crear múltiples copias de estos ejemplos hasta alcanzar un mejor balance

**Ventajas**:
- Simple de implementar
- No se pierde información
- Funciona sorprendentemente bien, especialmente con redes neuronales

**Desventajas**:
- Puede llevar a sobreajuste (overfitting)
- No introduce variedad real en los datos

#### 6.2.2. Undersampling (Submuestreo)
Consiste en reducir el número de casos de la clase mayoritaria.

**Método**:
- Eliminar aleatoriamente ejemplos de la clase mayoritaria
- Mantener solamente un subconjunto hasta alcanzar un mejor balance con la clase minoritaria

**Ventajas**:
- Reduce el tiempo de entrenamiento
- Puede ayudar a resolver problemas de memoria o escalabilidad

**Desventajas**:
- Descarta información potencialmente valiosa
- Generalmente no es la mejor opción a menos que haya restricciones de recursos

**Casos de uso recomendados**:
- Cuando tienes demasiados datos para procesar con tus recursos computacionales
- Como parte de un enfoque híbrido (combinado con otras técnicas)

#### 6.2.3. SMOTE (Synthetic Minority Oversampling Technique)
Técnica más avanzada que genera sintéticamente nuevos ejemplos de la clase minoritaria.

**Funcionamiento**:
1. Para cada muestra de la clase minoritaria, encuentra sus K vecinos más cercanos (KNN)
2. Selecciona aleatoriamente uno de esos vecinos
3. Crea un nuevo ejemplo sintético ubicado aleatoriamente en algún punto de la línea que conecta la muestra original con el vecino seleccionado
4. Repite este proceso hasta alcanzar el balance deseado

**Ventajas**:
- Genera ejemplos nuevos en lugar de simples copias
- Mantiene las características de distribución de la clase minoritaria
- Generalmente produce mejores resultados que el oversampling simple

**Desventajas**:
- Más complejo de implementar
- Puede generar ejemplos poco realistas si se aplica sin consideraciones adicionales

### 6.3. Ajuste de Umbrales de Decisión

#### 6.3.1. Concepto de umbral
La mayoría de los modelos de clasificación no producen simplemente "sí" o "no", sino una probabilidad. El umbral determina a partir de qué probabilidad clasificamos un caso como positivo.

**Ejemplo**: Por defecto, si un modelo predice una probabilidad de fraude > 0.5 (50%), se clasifica como fraude. Pero este umbral puede ajustarse.

#### 6.3.2. Modificando el umbral
- **Aumentar el umbral**: Requiere una mayor probabilidad para clasificar como positivo
  - Reduce falsos positivos
  - Aumenta falsos negativos
  
- **Disminuir el umbral**: Requiere menor probabilidad para clasificar como positivo
  - Reduce falsos negativos
  - Aumenta falsos positivos

#### 6.3.3. Consideraciones para el ajuste de umbrales
- **Costo relativo de los errores**: ¿Qué es peor, un falso positivo o un falso negativo?
- **Contexto del problema**: En fraude, puede ser preferible tener más falsos positivos (molestias para clientes) que falsos negativos (pérdidas económicas)
- **Métricas de evaluación**: Analizar más allá de la exactitud (accuracy), considerando recall, precisión y F1-score

### 6.4. Evaluación de Modelos con Datos No Balanceados

#### 6.4.1. El problema con la métrica de exactitud (accuracy)
En conjuntos muy desbalanceados, la exactitud puede ser engañosa, ya que un modelo que siempre predice la clase mayoritaria tendrá una alta exactitud.

#### 6.4.2. Métricas alternativas
- **Precision**: De todos los casos que predecimos como positivos, ¿cuántos son realmente positivos?
- **Recall**: De todos los casos positivos reales, ¿cuántos detectamos correctamente?
- **F1-Score**: Media armónica entre precision y recall
- **AUC-ROC**: Área bajo la curva ROC, que muestra el rendimiento en diferentes umbrales
- **Matriz de confusión**: Visualización que muestra verdaderos positivos, falsos positivos, falsos negativos y verdaderos negativos

#### 6.4.3. Validación cruzada estratificada
Para evaluar modelos con datos no balanceados, es importante usar validación cruzada estratificada, que mantiene la proporción de clases en cada partición.

---

## Resumen para el Examen

### Datos No Balanceados - Conceptos Clave
- **Definición**: Gran disparidad entre casos positivos y negativos en los datos
- **Problema principal**: Los modelos tienden a favorecer la clase mayoritaria
- **Ejemplo común**: Detección de fraude (muy pocos ejemplos positivos)
- **Terminología**: "Positivo" se refiere al caso que intentamos detectar, independientemente de si es deseable o no

### Técnicas para Manejar Datos No Balanceados

| Técnica | Descripción | Ventajas | Desventajas | Cuándo usar |
|---------|-------------|----------|-------------|-------------|
| **Oversampling** | Duplicar ejemplos de la clase minoritaria | Simple, preserva toda la información | Posible sobreajuste, no añade variedad | Cuando se necesita una solución rápida |
| **Undersampling** | Eliminar ejemplos de la clase mayoritaria | Reduce tiempo de entrenamiento | Descarta información | Cuando hay limitaciones de recursos |
| **SMOTE** | Generar ejemplos sintéticos usando KNN | Crea nuevos ejemplos en lugar de copias | Más complejo, puede crear ejemplos poco realistas | Cuando se necesita una solución más robusta |
| **Ajuste de Umbrales** | Modificar el punto de corte para clasificación | No requiere reentrenamiento | No mejora el aprendizaje del modelo | Como ajuste fino después del entrenamiento |

### Evaluación de Modelos
- **No usar solo accuracy**: En datos no balanceados, la exactitud puede ser engañosa
- **Métricas recomendadas**: Precision, Recall, F1-Score, AUC-ROC
- **Considerar costos de error**: Evaluar el impacto relativo de falsos positivos vs. falsos negativos

**Consejo para el examen**: Si se pregunta por la mejor técnica para manejar datos no balanceados, SMOTE generalmente será una mejor respuesta que oversampling simple o undersampling. Cuando se trate de métricas de evaluación, recuerda que accuracy no es adecuada para datos muy desbalanceados, y se deberían preferir métricas como F1-score o AUC-ROC. Entiende también cómo el ajuste de umbrales afecta el balance entre falsos positivos y falsos negativos, y que la elección depende del costo relativo de estos errores en tu aplicación específica.


## 7. Handling Outliers

### 7.1. Fundamentos Estadísticos

Para comprender adecuadamente los outliers (valores atípicos), es importante tener claros algunos conceptos estadísticos básicos.

#### 7.1.1. Varianza

La varianza es una medida de dispersión que indica qué tan alejados están los valores de un conjunto de datos con respecto a su media.

**Cálculo de la varianza (σ²)**:
1. Calcular la media del conjunto de datos
2. Restar la media de cada valor para obtener las diferencias
3. Elevar al cuadrado cada diferencia
4. Calcular el promedio de estas diferencias al cuadrado

**Ejemplo**:
Para el conjunto de datos [1, 4, 5, 4, 8]:
1. Media = (1 + 4 + 5 + 4 + 8) / 5 = 4.4
2. Diferencias: [1 - 4.4, 4 - 4.4, 5 - 4.4, 4 - 4.4, 8 - 4.4] = [-3.4, -0.4, 0.6, -0.4, 3.6]
3. Diferencias al cuadrado: [11.56, 0.16, 0.36, 0.16, 12.96]
4. Varianza = (11.56 + 0.16 + 0.36 + 0.16 + 12.96) / 5 = 5.04

**¿Por qué elevamos al cuadrado?**:
- Evita que los valores negativos y positivos se cancelen entre sí
- Da mayor peso a los valores más alejados de la media
- Convierte todas las diferencias a valores positivos

#### 7.1.2. Desviación Estándar

La desviación estándar (σ) es simplemente la raíz cuadrada de la varianza y es una medida más intuitiva de la dispersión.

**Cálculo**:
- σ = √σ²

**Ejemplo**:
Para el conjunto anterior con varianza = 5.04:
- Desviación estándar = √5.04 = 2.24

La desviación estándar nos permite interpretar qué tan lejos está un valor de la media en términos de "unidades de desviación".

### 7.2. Identificación de Outliers

#### 7.2.1. Método de la Desviación Estándar

Un enfoque común para identificar outliers es considerar como tales aquellos valores que se encuentran a más de cierto número de desviaciones estándar de la media.

**Regla típica**:
- Outliers leves: valores a más de 1 desviación estándar de la media
- Outliers moderados: valores a más de 2 desviaciones estándar de la media
- Outliers extremos: valores a más de 3 desviaciones estándar de la media

**Ejemplo**:
En nuestro conjunto [1, 4, 5, 4, 8] con media = 4.4 y desviación estándar = 2.24:
- Rango de valores no outliers (1 desviación): [4.4 - 2.24, 4.4 + 2.24] = [2.16, 6.64]
- Los valores 1 y 8 están fuera de este rango, por lo que serían considerados outliers

#### 7.2.2. Método del Rango Intercuartil (IQR)

Este método se basa en los cuartiles del conjunto de datos y es menos sensible a valores extremos que la desviación estándar.

**Cálculo**:
1. Ordenar los datos
2. Encontrar Q1 (primer cuartil, percentil 25)
3. Encontrar Q3 (tercer cuartil, percentil 75)
4. Calcular IQR = Q3 - Q1
5. Definir límites: [Q1 - 1.5×IQR, Q3 + 1.5×IQR]
6. Los valores fuera de estos límites son outliers

Este método está relacionado con los diagramas de caja y bigotes (box and whisker plots) para visualizar outliers.

#### 7.2.3. Random Cut Forest (AWS)

AWS implementa su propio algoritmo de detección de outliers llamado Random Cut Forest, que está disponible en varios servicios:

- Amazon QuickSight
- Amazon Kinesis Analytics
- Amazon SageMaker
- Otros servicios de AWS

Random Cut Forest es un algoritmo no supervisado que identifica puntos de datos anómalos asignándoles puntuaciones de anomalía. Es especialmente útil para la detección de anomalías en tiempo real y en flujos de datos.

### 7.3. Decisiones para el Manejo de Outliers

#### 7.3.1. ¿Eliminar o mantener?

La decisión de eliminar outliers debe tomarse con responsabilidad y depende del contexto:

**Casos donde puede ser apropiado eliminar outliers**:
- Cuando representan errores de medición o entrada de datos
- Cuando provienen de fuentes no relevantes para el modelo (bots, tráfico malicioso)
- En sistemas de recomendación, cuando algunos usuarios tienen una influencia desproporcionada

**Casos donde NO es apropiado eliminar outliers**:
- Cuando son parte legítima de la distribución que se está modelando
- Cuando el objetivo específicamente requiere incluirlos (como calcular la media de ingresos)
- Cuando representan casos extremos pero reales que el modelo debe considerar

#### 7.3.2. Considerar el impacto en el negocio

Siempre evalúa el impacto en el resultado final:
- ¿Cómo afecta la inclusión o exclusión de outliers al objetivo del modelo?
- ¿Qué implicaciones tiene para las decisiones de negocio?

### 7.4. Ejemplo Práctico: Distribución de Ingresos

Consideremos un conjunto de datos que representa ingresos anuales con un valor medio de aproximadamente $27,000. Si añadimos un solo billonario (con ingresos de $1,000,000,000), esto distorsiona completamente la distribución:

1. **Distribución original**: Centrada alrededor de $27,000
2. **Distribución con outlier**: La mayoría de los datos se comprimen en un pequeño segmento del gráfico, mientras que el billonario crea una enorme distorsión
3. **Distribución después de eliminar outliers**: Al eliminar valores que están a más de 2 desviaciones estándar, se recupera una distribución más representativa

Este ejemplo ilustra cómo un solo valor extremo puede afectar dramáticamente análisis estadísticos como la media.

### 7.5. Enfoques Alternativos

#### 7.5.1. Transformación en lugar de eliminación

En lugar de eliminar outliers, se pueden aplicar transformaciones que reduzcan su impacto:
- Transformación logarítmica
- Escalado robusto (usando medianas y rangos intercuartiles)
- Winsorización (recortar valores extremos a un percentil específico)

#### 7.5.2. Tratamiento separado

Otra opción es modelar los outliers por separado:
- Crear modelos específicos para datos "normales" y para outliers
- Utilizar técnicas de modelado que sean robustas a outliers
- Implementar reglas de negocio específicas para casos extremos

---

## Resumen para el Examen

### Conceptos Estadísticos Clave
- **Varianza (σ²)**: Promedio de las diferencias al cuadrado respecto a la media
- **Desviación Estándar (σ)**: Raíz cuadrada de la varianza, medida de dispersión

### Métodos para Identificar Outliers

| Método | Descripción | Ventajas | Cuándo usar |
|--------|-------------|----------|-------------|
| **Desviación Estándar** | Identifica valores a más de N desviaciones estándar de la media | Simple, intuitivo | Datos con distribución aproximadamente normal |
| **Rango Intercuartil (IQR)** | Utiliza Q1, Q3 y define outliers como valores fuera de [Q1-1.5×IQR, Q3+1.5×IQR] | Robusto frente a valores extremos | Datos sesgados o no normalmente distribuidos |
| **Random Cut Forest** | Algoritmo de AWS para detectar anomalías | Escalable, funciona en streaming | Servicios AWS (QuickSight, Kinesis, SageMaker) |

### Decisiones sobre Outliers
- **Eliminar cuando**: Son errores, no son parte del fenómeno estudiado, distorsionan excesivamente
- **Mantener cuando**: Son parte legítima de los datos, el objetivo requiere incluirlos
- **Alternativas**: Transformar datos, modelar outliers por separado

### AWS y Outliers
- El algoritmo **Random Cut Forest** es la solución preferida de AWS para la detección de outliers
- Disponible en múltiples servicios como QuickSight, Kinesis Analytics y SageMaker

**Consejo para el examen**: Si ves preguntas sobre detección de outliers en servicios AWS, busca opciones que mencionen Random Cut Forest, ya que es la solución preferida de Amazon para este propósito. Recuerda también que la decisión de eliminar outliers debe tomarse con responsabilidad y depende del contexto específico del problema que estás tratando de resolver. El método más apropiado (desviación estándar, IQR, etc.) dependerá de la distribución de tus datos.


## 8. Binning, Transforming, Encoding, Scaling, and Shuffling

Durante el proceso de feature engineering, existen varias técnicas adicionales que pueden mejorar significativamente el rendimiento de los modelos de machine learning. Estas técnicas ayudan a transformar los datos originales en formatos más adecuados para los algoritmos de aprendizaje.

### 8.1. Binning (Discretización)

#### 8.1.1. Concepto básico
El binning es una técnica que transforma datos numéricos continuos en datos categóricos discretos, agrupando valores en rangos o "bins" (contenedores).

**Ejemplo práctico**:
- Datos originales: Edades exactas (22.5, 31.7, 25.3, 45.8...)
- Datos transformados: Grupos de edad (20-29, 30-39, 40-49...)

#### 8.1.2. Razones para usar binning
- **Manejo de incertidumbre**: Reduce el impacto de pequeños errores de medición
- **Simplificación de datos**: Facilita el análisis y la interpretación
- **Compatibilidad con algoritmos**: Algunos modelos trabajan mejor con datos categóricos
- **Reducción de ruido**: Mitiga el impacto de fluctuaciones menores

#### 8.1.3. Quantile Binning (Discretización por cuantiles)
Una variante específica del binning que asegura que cada bin contenga aproximadamente el mismo número de muestras.

**Características principales**:
- Asegura una distribución uniforme de datos entre categorías
- Útil cuando se busca balance entre grupos
- Menos sensible a valores atípicos que el binning por rangos iguales

**Ejemplo**:
Si tenemos 1,000 registros y queremos 4 bins con quantile binning, cada bin contendrá aproximadamente 250 registros, independientemente del rango de valores que abarque.

### 8.2. Transforming (Transformación)

#### 8.2.1. Concepto básico
La transformación consiste en aplicar funciones matemáticas a las características para modificar su distribución o escala, haciendo que sean más adecuadas para los algoritmos de ML.

#### 8.2.2. Tipos comunes de transformaciones
- **Logarítmica**: Útil para datos con distribución exponencial
- **Raíz cuadrada**: Menos drástica que la logarítmica para valores extremos
- **Potencias**: Elevar los datos a diferentes potencias
- **Box-Cox**: Familia de transformaciones que incluye logaritmos y potencias

#### 8.2.3. Caso de estudio: YouTube
YouTube, en su sistema de recomendaciones, utiliza transformaciones para capturar relaciones no lineales:
- Para cada característica numérica X (por ejemplo, tiempo desde que se vio un video):
  - Incluyen X (valor original)
  - Incluyen X² (valor al cuadrado)
  - Incluyen √X (raíz cuadrada)

Este enfoque permite al sistema aprender tanto funciones superlineales como sublineales, mejorando la calidad de las recomendaciones.

#### 8.2.4. Beneficios de la transformación
- **Linearización**: Convertir relaciones no lineales en lineales
- **Normalización**: Hacer que distribuciones sesgadas sean más simétricas
- **Captura de diferentes comportamientos**: Identificar relaciones complejas en los datos

![Ejemplo de transformación logarítmica](https://ejemplo.com/imagen-transformacion.png)
*La transformación logarítmica puede convertir una relación exponencial (línea verde) en una relación lineal, facilitando el trabajo de muchos algoritmos.*

### 8.3. Encoding (Codificación)

#### 8.3.1. Concepto básico
La codificación transforma datos categóricos en formatos numéricos que pueden ser utilizados por algoritmos de machine learning.

#### 8.3.2. One-Hot Encoding
Una técnica popular especialmente para deep learning, donde cada categoría se convierte en una columna binaria (0 o 1).

**Proceso**:
1. Identificar todas las categorías posibles
2. Crear una columna nueva para cada categoría
3. Para cada registro, asignar 1 en la columna correspondiente a su categoría y 0 en las demás

**Ejemplo**: Codificación de dígitos (0-9) para reconocimiento de escritura a mano
- Número original: 8
- Representación One-Hot: [0,0,0,0,0,0,0,0,1,0]
  - El 1 está en la novena posición (índice 8)
  - Todas las demás posiciones tienen 0

**Aplicación en Deep Learning**:
- Las neuronas de entrada suelen activarse (1) o no activarse (0)
- No podemos simplemente alimentar el número 8 a una neurona
- El one-hot encoding permite representar categorías de manera que las redes neuronales puedan procesarlas efectivamente

#### 8.3.3. Otras técnicas de codificación
- **Label Encoding**: Asigna un número entero a cada categoría
- **Target Encoding**: Reemplaza categorías por la media de la variable objetivo para esa categoría
- **Binary Encoding**: Convierte el número de la categoría a binario y crea columnas para cada bit

### 8.4. Scaling (Escalado)

#### 8.4.1. Concepto básico
El escalado ajusta los valores numéricos a un rango específico para evitar que características con magnitudes mayores dominen el modelo.

#### 8.4.2. Importancia del escalado
La mayoría de los algoritmos de ML son sensibles a la escala de las características:
- Sin escalado, las características con valores grandes (ej. ingreso: $50,000) tendrían más influencia que las características con valores pequeños (ej. edad: 35)
- Las redes neuronales funcionan mejor con datos centrados alrededor de cero

**Ejemplo práctico**:
- Características originales: Ingreso ($50,000), Edad (35)
- Sin escalado: El ingreso tendría aproximadamente 1,400 veces más peso que la edad
- Con escalado: Ambas características tendrían rangos comparables

#### 8.4.3. Técnicas comunes de escalado
- **Min-Max Scaling**: Escala los datos al rango [0,1] o [-1,1]
- **Normalización Z-score**: Transforma los datos para tener media 0 y desviación estándar 1
- **Robust Scaling**: Usa medianas y rangos intercuartiles, menos sensible a outliers

#### 8.4.4. Implementación en Python
La biblioteca scikit-learn proporciona herramientas como `MinMaxScaler` para facilitar el escalado.

**Consideración importante**: Si estás prediciendo valores numéricos, recuerda invertir el escalado para obtener predicciones en la escala original.

### 8.5. Shuffling (Aleatorización)

#### 8.5.1. Concepto básico
El shuffling consiste en aleatorizar el orden de los datos antes de entrenar un modelo para eliminar cualquier sesgo relacionado con el orden de recolección.

#### 8.5.2. Importancia del shuffling
- Elimina patrones temporales que podrían afectar el aprendizaje
- Previene que el modelo "memorice" secuencias específicas
- Mejora la generalización del modelo

#### 8.5.3. Casos de uso
- **Series temporales**: Cuando no queremos que el modelo aprenda el orden cronológico
- **Procesamiento por lotes**: Para asegurar que cada lote sea representativo del conjunto total
- **Validación cruzada**: Para asegurar que las particiones sean aleatorias

#### 8.5.4. Impacto práctico
Muchos proyectos de ML han experimentado mejoras significativas simplemente por implementar el shuffling de datos antes del entrenamiento.

---

## Resumen para el Examen

### Técnicas Clave de Feature Engineering

| Técnica | Descripción | Casos de Uso | Consideraciones |
|---------|-------------|--------------|-----------------|
| **Binning** | Transforma datos numéricos en categorías | Reducir ruido, manejar incertidumbre | Puede perder información |
| **Quantile Binning** | Crea bins con igual número de muestras | Cuando se necesita balance entre categorías | Mantiene distribución uniforme |
| **Transforming** | Aplica funciones matemáticas para modificar distribuciones | Linearizar relaciones, normalizar datos | La transformación depende de la distribución |
| **One-Hot Encoding** | Convierte categorías en vectores binarios | Necesario para deep learning con datos categóricos | Aumenta dimensionalidad |
| **Scaling** | Ajusta valores a rangos comparables | Cuando las características tienen diferentes escalas | La mayoría de algoritmos lo requieren |
| **Shuffling** | Aleatoriza el orden de los datos | Eliminar sesgos de recolección | Casi siempre beneficioso |

### Puntos Clave a Recordar
- **Binning**: Transforma datos numéricos a categóricos; quantile binning asegura igual número de muestras por bin
- **Transforming**: Las transformaciones como logaritmos pueden linearizar relaciones y mejorar el rendimiento
- **One-Hot Encoding**: Esencial para convertir categorías en formato utilizable por redes neuronales
- **Scaling**: Crítico para la mayoría de algoritmos, evita que características con valores grandes dominen
- **Shuffling**: Simple pero efectivo, puede mejorar dramáticamente los resultados

**Consejo para el examen**: Presta especial atención al one-hot encoding y su aplicación en deep learning, ya que es un concepto fundamental. Recuerda también que, aunque técnicas como el binning pueden simplificar los datos, generalmente implican una pérdida de información, por lo que su uso debe justificarse (como manejar la incertidumbre en mediciones). Para el escalado, comprende que es necesario para casi todos los algoritmos excepto árboles de decisión, y que necesitas revertir el escalado si predices valores numéricos.


## 9. SageMaker AI Overview

### 9.1. Introducción a Amazon SageMaker

Amazon SageMaker es el servicio integral de AWS diseñado para el desarrollo, entrenamiento y despliegue de modelos de machine learning. Aunque puede utilizarse para aplicaciones de IA generativa, SageMaker tiene un alcance más amplio, abarcando todo el espectro de algoritmos de machine learning tradicionales y deep learning.

#### 9.1.1. Alcance y características principales
SageMaker está diseñado para manejar el ciclo de vida completo del machine learning:
- Obtención y preparación de datos
- Entrenamiento de modelos
- Evaluación de rendimiento
- Despliegue y monitoreo en producción

Esta plataforma unificada permite a los científicos de datos y desarrolladores trabajar con eficiencia en proyectos de ML, sin tener que preocuparse por gestionar la infraestructura subyacente.

### 9.2. Arquitectura de Entrenamiento y Despliegue

El flujo de trabajo en SageMaker sigue un proceso estructurado que podemos visualizar de la siguiente manera:

#### 9.2.1. Componentes del despliegue de modelos
- **Cliente/Aplicación**: Aplicación externa que solicita predicciones
- **Endpoints**: Puntos de acceso para comunicarse con el modelo desplegado
- **Modelo desplegado**: Instancia del modelo en ejecución que procesa las solicitudes
- **Artefactos del modelo**: Componentes almacenados en S3 que contienen los parámetros aprendidos
- **Código de inferencia**: Código (generalmente almacenado en ECR - Elastic Container Registry) que implementa cómo el modelo realiza predicciones

#### 9.2.2. Componentes del entrenamiento de modelos
- **Código de entrenamiento**: Almacenado en ECR, define cómo se entrena el modelo
- **Datos de entrenamiento**: Almacenados en S3, son los datos utilizados para entrenar el modelo
- **Proceso de entrenamiento**: Ejecuta el código de entrenamiento sobre los datos para producir artefactos del modelo

#### 9.2.3. Flujo de trabajo completo
1. El código de entrenamiento consume datos desde S3
2. El proceso de entrenamiento produce artefactos del modelo
3. Estos artefactos se combinan con el código de inferencia para crear un modelo desplegable
4. El modelo se despliega a través de endpoints
5. Las aplicaciones cliente acceden al modelo mediante estos endpoints

### 9.3. Métodos de Interacción con SageMaker

SageMaker ofrece múltiples formas de interactuar con la plataforma, adaptándose a diferentes preferencias y necesidades:

#### 9.3.1. SageMaker Notebooks
- Basados en Jupyter Notebooks pero ejecutados en instancias EC2
- Incluyen acceso integrado a S3
- Vienen con bibliotecas preinstaladas como scikit-learn, Spark, TensorFlow y PySpark
- Ofrecen acceso a modelos incorporados de SageMaker
- Permiten orquestar todo el ciclo de vida mediante código Python:
  - Procesamiento de datos
  - Entrenamiento de modelos
  - Ajuste de hiperparámetros
  - Despliegue de modelos

#### 9.3.2. Interfaz de Consola AWS
- Permite configurar trabajos de entrenamiento sin escribir código
- Ofrece acceso a algoritmos incorporados
- Facilita la selección de orígenes de datos
- Permite configurar y monitorear despliegues
- Ideal para usuarios que prefieren interfaces gráficas a la programación

### 9.4. Dominios de SageMaker AI

Un concepto fundamental en SageMaker es el "dominio", que actúa como contenedor organizativo para todos los recursos y actividades.

#### 9.4.1. Definición y componentes
Un dominio de SageMaker AI incluye:
- **Volumen EFS compartido**: Sistema de archivos compartido para todo el dominio
- **Lista de usuarios autorizados**: Personas con acceso al dominio
- **Configuraciones**: Incluye políticas y configuraciones de red (VPC)

#### 9.4.2. Perfiles de usuario
Dentro de cada dominio existen perfiles de usuario que:
- Representan a usuarios individuales dentro del dominio
- Poseen aplicaciones personales (como instancias de SageMaker Studio)
- Tienen directorios EFS privados para trabajo individual
- Pueden acceder a recursos compartidos en el dominio

#### 9.4.3. Espacios compartidos
Los dominios también incluyen:
- Directorios EFS compartidos para colaboración entre usuarios
- Aplicaciones IDE compartidas (SageMaker Studio)
- Recursos que pueden ser utilizados por todos los usuarios del dominio

### 9.5. Configuración de Red en SageMaker

La configuración de red es un aspecto importante de los dominios de SageMaker que afecta la seguridad y conectividad.

#### 9.5.1. Configuración predeterminada
Por defecto, un dominio SageMaker utiliza dos VPCs:
- **VPC gestionada por SageMaker**: Para tráfico hacia/desde Internet
- **VPC propia del cliente**: Para tráfico cifrado hacia el volumen EFS

Para la VPC del cliente, se deben especificar:
- Subredes (generalmente todas las disponibles)
- Grupos de seguridad apropiados

#### 9.5.2. Configuración "VPC only"
Como alternativa, se puede seleccionar la opción "VPC only" al crear el dominio:
- Todo el tráfico pasa únicamente por la VPC del cliente
- Permite mayor control sobre la conectividad
- Posibilita restringir accesos según necesidades específicas

### 9.6. Organización de Recursos en SageMaker

SageMaker organiza sus recursos de manera jerárquica:

**Dominio**
↓
**Perfiles de Usuario**
↓
**Aplicaciones Personales**

Adicionalmente, los dominios contienen:
- **Recursos compartidos**: Accesibles para todos los usuarios
- **Espacios compartidos**: Áreas colaborativas con sus propias aplicaciones

---

## Resumen para el Examen

### Conceptos Clave de SageMaker

| Componente | Descripción | Propósito |
|------------|-------------|-----------|
| **SageMaker** | Servicio integral de ML en AWS | Abarca todo el ciclo de vida de machine learning |
| **Dominio** | Unidad organizativa principal | Contiene usuarios, aplicaciones y configuraciones |
| **Perfiles de Usuario** | Representación de usuarios individuales | Gestiona aplicaciones y espacios personales |
| **Notebooks** | Entornos Jupyter en EC2 | Desarrollo y orquestación de flujos de ML |
| **Endpoints** | Puntos de acceso al modelo | Permiten a aplicaciones solicitar predicciones |
| **VPC** | Redes virtuales asociadas | Gestionan el tráfico interno y externo |

### Arquitectura de SageMaker
- **Entrenamiento**: Código (ECR) + Datos (S3) → Artefactos del modelo (S3)
- **Despliegue**: Artefactos (S3) + Código de inferencia (ECR) → Endpoints
- **Inferencia**: Aplicación → Endpoints → Modelo desplegado → Predicciones

### Dominio de SageMaker AI
- Requiere un volumen EFS compartido
- Incluye usuarios autorizados y configuraciones de red
- Puede configurarse con acceso a Internet o solo VPC
- Organiza usuarios y recursos en una jerarquía lógica

**Consejo para el examen**: Presta especial atención a la arquitectura de entrenamiento y despliegue, ya que muestra cómo diferentes servicios de AWS (S3, ECR, etc.) interactúan con SageMaker. También es importante entender el concepto de dominio como unidad organizativa en SageMaker, y las implicaciones de las diferentes configuraciones de VPC (predeterminada vs. "VPC only") en términos de seguridad y accesibilidad.


## 10. Data Processing, Training, and Deployment with SageMaker AI

Amazon SageMaker proporciona un entorno integral para el ciclo de vida completo del desarrollo de modelos de machine learning, desde la preparación de datos hasta el despliegue y monitoreo. A continuación, exploraremos en detalle cada fase de este proceso.

### 10.1. Preparación de Datos en SageMaker

La fase de preparación de datos es crucial para el éxito de cualquier proyecto de machine learning, y SageMaker ofrece diversas opciones para gestionar esta etapa.

#### 10.1.1. Fuentes de datos
SageMaker puede ingerir datos de múltiples fuentes:
- **Amazon S3**: Principal método de almacenamiento para conjuntos de datos
- **FSx for Lustre**: Para procesamiento de datos a gran escala que requiere alto rendimiento
- **Amazon Athena**: Para datos almacenados en lagos de datos
- **Amazon EMR**: Para procesamiento distribuido
- **Amazon Redshift**: Para datos almacenados en data warehouses
- **Amazon Keyspaces**: Para datos de bases de datos NoSQL
- **Otras fuentes de datos** compatibles con AWS

#### 10.1.2. Formatos de datos
El formato ideal depende del algoritmo específico que se utilizará:
- **RecordIO**: Formato optimizado para ciertos algoritmos de SageMaker
- **Protobuf**: Eficiente para serialización de datos estructurados
- **Formatos columnares**: Como Parquet o ORC, optimizados para análisis

#### 10.1.3. Integración con herramientas de procesamiento
SageMaker se integra con diversas herramientas para el procesamiento de datos:
- **Apache Spark**: Para procesamiento distribuido
- **Bibliotecas Python**: scikit-learn, NumPy, pandas, etc.
- **Contenedores personalizados**: Para implementar lógica de procesamiento propia

#### 10.1.4. Flujo de procesamiento
El proceso de preparación de datos en SageMaker sigue generalmente estos pasos:
1. Se obtienen los datos desde S3 u otra fuente
2. Se inicia un contenedor de procesamiento (propio o integrado en SageMaker)
3. El contenedor procesa los datos según la lógica definida
4. Los datos procesados se almacenan en S3 para su uso posterior en entrenamiento

### 10.2. Entrenamiento de Modelos en SageMaker

Una vez que los datos están preparados, SageMaker facilita el entrenamiento de modelos con diferentes opciones y niveles de personalización.

#### 10.2.1. Creación de trabajos de entrenamiento
Para entrenar un modelo en SageMaker:
1. Se crea un trabajo de entrenamiento
2. Se especifica la ubicación de los datos procesados (URL de S3)
3. Se asignan recursos computacionales (tipo y número de instancias)
4. Se define la ubicación de salida para los artefactos del modelo
5. Se proporciona la ruta al código de entrenamiento (generalmente en ECR)

#### 10.2.2. Opciones de recursos computacionales
El hardware asignado afecta significativamente el rendimiento y costo:
- Diferentes algoritmos tienen diferentes recomendaciones de hardware
- Algunos algoritmos funcionan mejor con una sola instancia
- Otros se benefician de entrenamiento distribuido en múltiples instancias
- SageMaker ofrece instancias optimizadas para diversos casos de uso (CPU, GPU, memoria)

#### 10.2.3. Opciones de entrenamiento
SageMaker ofrece múltiples opciones para el código de entrenamiento:

| Opción | Descripción | Casos de uso |
|--------|-------------|------------|
| **Algoritmos integrados** | Implementaciones optimizadas por AWS | Escenarios comunes de ML |
| **Apache Spark ML** | Biblioteca de ML de Spark | Procesamiento distribuido |
| **Frameworks populares** | TensorFlow, PyTorch, MXNet | Deep learning personalizado |
| **Scikit-learn** | Biblioteca popular de ML para Python | ML tradicional |
| **RL Estimator** | Reforzamiento | Aprendizaje por refuerzo |
| **XGBoost** | Implementación optimizada | Problemas de clasificación/regresión |
| **Hugging Face** | Modelos preentrenados | NLP, IA generativa |
| **Chainer** | Framework de deep learning | Investigación y experimentación |
| **Docker personalizado** | Contenedor totalmente personalizable | Casos de uso específicos |
| **Modelos del Marketplace** | Soluciones comerciales | Aplicaciones específicas de industria |

#### 10.2.4. Almacenamiento de modelos entrenados
Al finalizar el entrenamiento:
- Los artefactos del modelo se guardan automáticamente en la ubicación S3 especificada
- Estos artefactos contienen los parámetros aprendidos y la estructura del modelo
- Son utilizados posteriormente para el despliegue e inferencia

### 10.3. Despliegue de Modelos en SageMaker

Tras entrenar un modelo, SageMaker ofrece diversas opciones para desplegarlo y utilizarlo para realizar predicciones.

#### 10.3.1. Modos de despliegue
Dependiendo de los requisitos, SageMaker ofrece diferentes modos de despliegue:

- **Endpoint en tiempo real**: 
  - Proporciona predicciones bajo demanda con baja latencia
  - Escalable según la demanda
  - Ideal para aplicaciones interactivas

- **Transformación por lotes (Batch Transform)**:
  - Procesa conjuntos completos de datos a la vez
  - No requiere endpoint persistente
  - Más eficiente para predicciones en grandes volúmenes de datos no urgentes

#### 10.3.2. Características avanzadas de despliegue
SageMaker proporciona capacidades adicionales para optimizar y gestionar despliegues:

- **Inference Pipelines**: 
  - Orquestación de flujos de inferencia complejos
  - Permite encadenar múltiples modelos y pasos de preprocesamiento

- **SageMaker Neo**: 
  - Optimiza modelos para despliegue en dispositivos edge
  - Útil cuando se requiere procesamiento local sin conexión a la nube
  - Reduce latencia eliminando la necesidad de comunicación con la nube

- **Elastic Inference**: 
  - Acelera la inferencia adjuntando capacidad de GPU fraccional
  - Reduce costos manteniendo buen rendimiento

- **Auto Scaling**: 
  - Ajusta automáticamente el número de instancias según la carga
  - Optimiza costos y rendimiento

- **Shadow Testing**:
  - Evalúa nuevos modelos contra modelos desplegados
  - Detecta problemas antes del despliegue completo
  - Minimiza riesgos al introducir nuevos modelos

#### 10.3.3. Gestión de despliegues
SageMaker facilita la gestión segura de modelos en producción:

- **Despliegue controlado**: Introducción gradual de nuevos modelos
- **Monitoreo de rendimiento**: Seguimiento de métricas en tiempo real
- **Capacidad de rollback**: Reversión rápida a versiones anteriores si se detectan problemas
- **Versionado de modelos**: Gestión de diferentes versiones del mismo modelo

#### 10.3.4. Flujo de despliegue
El proceso típico de despliegue en SageMaker:
1. Recuperar artefactos del modelo desde S3
2. Seleccionar el modo de despliegue apropiado
3. Configurar recursos computacionales para la inferencia
4. Implementar estrategias de monitoreo y escalado
5. Establecer mecanismos de reversión en caso de problemas

### 10.4. Ciclo de vida completo en SageMaker

SageMaker está diseñado para facilitar todo el ciclo de vida de machine learning, proporcionando una experiencia integrada:

1. **Preparación de datos**:
   - Ingestión desde múltiples fuentes
   - Procesamiento con herramientas familiares
   - Almacenamiento optimizado para entrenamiento

2. **Entrenamiento de modelos**:
   - Algoritmos integrados o personalizados
   - Escalabilidad según necesidades computacionales
   - Gestión eficiente de recursos

3. **Despliegue y monitoreo**:
   - Opciones flexibles de despliegue
   - Herramientas avanzadas de optimización
   - Capacidades robustas de gestión de producción

Esta integración reduce la fricción entre fases, permitiendo a los equipos de data science centrarse en crear valor a partir de sus datos, en lugar de gestionar infraestructura.

---

## Resumen para el Examen

### Preparación de Datos en SageMaker
- **Fuentes principales**: S3 (más común), FSx for Lustre (alto rendimiento)
- **Formatos**: Dependen del algoritmo (RecordIO, Protobuf, formatos columnares)
- **Integración**: Apache Spark, bibliotecas Python (scikit-learn, NumPy, pandas)
- **Otras fuentes**: Athena, EMR, Redshift, Keyspaces

### Entrenamiento de Modelos
- **Recursos**: Configurables según necesidades (instancias optimizadas para ML)
- **Almacenamiento**: Artefactos guardados en S3
- **Opciones de entrenamiento**:
  - Algoritmos integrados de SageMaker
  - Frameworks populares (TensorFlow, PyTorch)
  - Hugging Face para modelos preentrenados y LLMs
  - Contenedores personalizados para máxima flexibilidad

### Despliegue de Modelos
- **Modos**: Endpoints en tiempo real vs. Batch Transform
- **Características avanzadas**:
  - Inference Pipelines para flujos complejos
  - SageMaker Neo para dispositivos edge
  - Elastic Inference para aceleración
  - Auto Scaling para optimización de recursos
  - Shadow Testing para validación de modelos

**Consejo para el examen**: Recuerda las diferentes opciones de despliegue y sus casos de uso. Comprende especialmente la diferencia entre endpoints en tiempo real (para predicciones bajo demanda) y Batch Transform (para procesamiento por lotes). También es importante conocer las diversas fuentes de datos compatibles con SageMaker y entender que el formato óptimo depende del algoritmo específico que se utilizará.

## 11. Amazon SageMaker Ground Truth and Label Generation

### 11.1. Introducción a SageMaker Ground Truth

Amazon SageMaker Ground Truth es un servicio de AWS diseñado para solucionar uno de los desafíos más comunes en proyectos de machine learning: la obtención de datos etiquetados de alta calidad. Este servicio facilita la creación de conjuntos de datos etiquetados mediante el uso de anotadores humanos.

#### 11.1.1. Propósito y aplicación
Ground Truth aborda el problema de los datos no etiquetados o con etiquetas faltantes, que es un aspecto fundamental del feature engineering. Aunque se ubica en esta categoría principalmente por su relación con datos faltantes, su utilidad se extiende a:

- Creación de conjuntos de entrenamiento etiquetados
- Generación de etiquetas para datos existentes
- Verificación y mejora de etiquetas existentes
- Obtención de información que solo puede ser inferida por humanos

#### 11.1.2. Casos de uso comunes
El escenario más típico para el uso de Ground Truth es la clasificación de imágenes, donde se requiere:
- Identificar objetos en imágenes
- Clasificar imágenes en categorías
- Marcar ubicaciones específicas dentro de imágenes
- Segmentar objetos en imágenes

Otros casos de uso incluyen etiquetado de texto, análisis de sentimiento, clasificación de documentos y etiquetado de audio.

### 11.2. Funcionamiento de SageMaker Ground Truth

#### 11.2.1. El proceso de etiquetado
SageMaker Ground Truth funciona siguiendo estos pasos básicos:

1. Se envían datos no etiquetados al servicio
2. Ground Truth distribuye los trabajos a anotadores humanos
3. Los anotadores etiquetan los datos según instrucciones específicas
4. Las etiquetas se recopilan, verifican y consolidan
5. El conjunto de datos etiquetado resultante puede usarse para entrenar modelos

#### 11.2.2. Aprendizaje activo
Lo que distingue a Ground Truth de otros servicios de etiquetado es su capacidad de aprendizaje activo:

- A medida que los humanos etiquetan datos, Ground Truth entrena un modelo en segundo plano
- Este modelo aprende patrones de las etiquetas humanas ya realizadas
- Con el tiempo, el modelo identifica qué elementos puede etiquetar con confianza
- Solo los casos ambiguos o de baja confianza se envían a los anotadores humanos
- Este enfoque reduce progresivamente la necesidad de intervención humana

**Beneficio clave**: Reducción de hasta un 70% en los costos de etiquetado, ya que se minimiza el trabajo humano necesario.

### 11.3. Opciones de Fuerza Laboral

Ground Truth ofrece flexibilidad en cuanto a quién realiza el trabajo de etiquetado, con tres opciones principales:

#### 11.3.1. Amazon Mechanical Turk
- Una fuerza laboral global distribuida
- Miles de trabajadores disponibles 24/7
- Costos relativamente bajos
- Ideal para tareas generales que no requieren conocimientos especializados
- Mejor para datos no sensibles

#### 11.3.2. Fuerza laboral privada
- Empleados de la propia organización
- Mayor seguridad para datos sensibles
- Conocimiento especializado del dominio
- Control total sobre quién accede a los datos
- Ideal para información confidencial o especializada

#### 11.3.3. Proveedores externos
- Empresas especializadas en etiquetado de datos
- Personal capacitado para tareas específicas
- Mayor calidad y consistencia en las etiquetas
- Experiencia en dominios particulares
- Más costoso pero con mejores garantías de calidad

### 11.4. Alternativas a la Etiquetación Humana

Además del etiquetado humano, existen otras formas de generar etiquetas o características para conjuntos de datos:

#### 11.4.1. Amazon Rekognition
- Servicio de AWS para reconocimiento de imágenes
- Modelos pre-entrenados para identificar objetos comunes
- Puede utilizarse para generar automáticamente etiquetas para imágenes
- Útil como complemento o alternativa a etiquetas humanas
- Especialmente eficaz para categorías generales y objetos comunes

#### 11.4.2. Amazon Comprehend
- Servicio de AWS para análisis de texto
- Puede generar:
  - Clasificación de temas
  - Análisis de sentimiento
  - Extracción de entidades
  - Frases clave
- Útil tanto para generar etiquetas como nuevas características
- Permite enriquecer conjuntos de datos textuales

#### 11.4.3. Otras técnicas
- Cualquier modelo pre-entrenado relevante para el dominio
- Técnicas de aprendizaje no supervisado para generar agrupaciones
- Transfer learning desde modelos existentes
- Combinación de múltiples fuentes de etiquetado

### 11.5. SageMaker Ground Truth Plus

Para organizaciones que buscan una solución completamente gestionada, AWS ofrece Ground Truth Plus:

#### 11.5.1. Características principales
- Solución "llave en mano" para proyectos de etiquetado
- Gestionada por expertos de AWS
- Configuración y gestión del flujo de trabajo completo
- Selección y administración de anotadores

#### 11.5.2. Proceso de implementación
1. Completar un formulario describiendo las necesidades del proyecto
2. Los expertos de AWS contactan para discutir detalles y precios
3. AWS configura y gestiona todo el proceso de etiquetado
4. Se puede seguir el progreso a través del portal de proyecto
5. Los datos etiquetados se entregan en S3 cuando están completos

#### 11.5.3. Ventajas y consideraciones
- **Ventajas**: Mínimo esfuerzo de gestión, experiencia especializada, alta calidad
- **Consideraciones**: Precio no divulgado públicamente (presumiblemente más alto), menor control directo sobre el proceso

### 11.6. Integración con el Flujo de Trabajo de ML

Ground Truth no es solo una herramienta independiente, sino que se integra perfectamente en el flujo de trabajo de machine learning:

#### 11.6.1. Como parte del feature engineering
- Genera etiquetas faltantes (variable objetivo)
- Puede crear nuevas características mediante anotaciones humanas
- Enriquece conjuntos de datos con información difícil de inferir automáticamente

#### 11.6.2. En el ciclo de vida de ML
- Preparación de datos: Creación de conjuntos de entrenamiento etiquetados
- Entrenamiento: Los datos etiquetados alimentan directamente los algoritmos
- Evaluación: Comparación de predicciones con etiquetas de alta calidad
- Mejora continua: Nuevos datos pueden etiquetarse para mejorar modelos existentes

---

## Resumen para el Examen

### SageMaker Ground Truth - Conceptos Clave
- **Propósito**: Servicio para obtener etiquetas de alta calidad mediante anotadores humanos
- **Diferenciador**: Implementa aprendizaje activo para reducir costos hasta en un 70%
- **Proceso**: A medida que se etiquetan datos, un modelo aprende para minimizar la intervención humana
- **Beneficio principal**: Solo los casos ambiguos se envían a humanos, optimizando costos y tiempo

### Opciones de Anotadores
| Opción | Descripción | Cuando usarla |
|--------|-------------|---------------|
| **Mechanical Turk** | Trabajadores globales a bajo costo | Datos no sensibles, tareas generales |
| **Fuerza laboral privada** | Empleados de la organización | Datos confidenciales, conocimiento especializado |
| **Proveedores externos** | Empresas especializadas en etiquetado | Alta calidad, dominios específicos |

### Alternativas y Complementos
- **Amazon Rekognition**: Para etiquetado automático de imágenes
- **Amazon Comprehend**: Para análisis y etiquetado de texto
- **Modelos pre-entrenados**: Como alternativa o complemento al etiquetado humano

### Ground Truth Plus
- Solución totalmente gestionada por expertos de AWS
- Mínimo esfuerzo pero presumiblemente mayor costo
- Ideal para proyectos grandes o complejos donde la gestión interna sería costosa

**Consejo para el examen**: Recuerda que lo que distingue a Ground Truth de simples servicios de etiquetado es su capacidad de aprendizaje activo, que reduce progresivamente la necesidad de intervención humana. También es importante entender su papel en el feature engineering: no solo se trata de generar etiquetas (targets) sino que también puede usarse para crear nuevas características para el entrenamiento de modelos.


## 12. Amazon Mechanical Turk

### 12.1. Origen e Historia

Amazon Mechanical Turk debe su nombre a un famoso artefacto histórico del siglo XVIII que tiene una relevancia sorprendente para el servicio actual.

#### 12.1.1. El Turco original
- Creado en la década de 1770
- Era un supuesto "autómata" capaz de jugar ajedrez
- En realidad, era una ilusión: un maestro de ajedrez humano se ocultaba dentro del mecanismo
- El operador humano controlaba los movimientos del "robot", creando la ilusión de una máquina inteligente
- Fue una sensación en su época, jugando contra figuras notables de la historia

#### 12.1.2. Analogía con el servicio actual
El nombre refleja perfectamente el concepto del servicio: tecnología que parece automatizada pero que en realidad está impulsada por inteligencia humana oculta tras bambalinas.

### 12.2. Descripción del Servicio

Amazon Mechanical Turk (MTurk) es una plataforma de crowdsourcing que permite acceder a una fuerza laboral humana distribuida globalmente para realizar tareas que las computadoras aún no pueden hacer eficazmente.

#### 12.2.1. Concepto básico
- Mercado de crowdsourcing para tareas humanas simples
- Proporciona acceso a una fuerza laboral virtual distribuida
- Los trabajadores ("Turkers") realizan pequeñas tareas a cambio de pagos
- Las tareas son generalmente sencillas pero requieren inteligencia humana

#### 12.2.2. Funcionamiento
1. Los solicitantes (empresas, investigadores, etc.) publican tareas denominadas HITs (Human Intelligence Tasks)
2. Establecen el precio por tarea y criterios de calificación para los trabajadores
3. Trabajadores de todo el mundo aceptan y completan estas tareas
4. El solicitante revisa el trabajo y aprueba el pago

#### 12.2.3. Aspecto económico
- El solicitante establece la recompensa por cada tarea
- Los precios pueden variar desde centavos hasta varios dólares por tarea
- Ejemplo: etiquetar 10 millones de imágenes a 10 centavos cada una costaría un millón de dólares
- Los costos totales dependen de la complejidad de la tarea y la velocidad requerida

### 12.3. Casos de Uso

Mechanical Turk tiene numerosas aplicaciones en diversos campos, especialmente en aquellos relacionados con inteligencia artificial y machine learning.

#### 12.3.1. Casos de uso generales
- **Clasificación de imágenes**: Etiquetar objetos o conceptos en fotografías
- **Recolección de datos**: Buscar y verificar información específica
- **Procesamiento de negocios**: Transcripción, categorización, verificación
- **Encuestas y estudios**: Investigación de mercado, estudios académicos
- **Moderación de contenido**: Identificar contenido inapropiado

#### 12.3.2. Aplicaciones en AI/ML
- **Creación de conjuntos de datos de entrenamiento**: Etiquetar datos para entrenar modelos de ML
- **Validación de resultados**: Verificar la precisión de las predicciones de los modelos
- **Revisión de recomendaciones**: Evaluar la calidad de sistemas de recomendación
- **Anotación de datos complejos**: Etiquetar entidades en textos, segmentar objetos en imágenes

### 12.4. Integración con Servicios AWS de AI/ML

Mechanical Turk se integra con varios servicios de AWS orientados a la inteligencia artificial y el machine learning.

#### 12.4.1. Integración con Amazon A2I
Amazon Augmented AI (A2I) permite incorporar revisión humana a las predicciones de modelos de ML:
- Configuración de flujos de trabajo de revisión humana
- Envío automático de predicciones de baja confianza a MTurk
- Mejora continua de modelos con feedback humano

#### 12.4.2. Integración con SageMaker Ground Truth
Como vimos en el tema anterior, Ground Truth utiliza MTurk como una de sus opciones de fuerza laboral:
- Etiquetado de datos para entrenamiento de modelos
- Verificación de etiquetas existentes
- Creación de conjuntos de datos de alta calidad

### 12.5. Experiencia del Trabajador

Comprender la experiencia de los trabajadores de MTurk ayuda a diseñar tareas más efectivas y obtener resultados de mayor calidad.

#### 12.5.1. Interfaz del trabajador
- Los trabajadores acceden a un panel que muestra HITs disponibles
- Pueden ver la descripción, recompensa y tiempo estimado para cada tarea
- Filtran tareas según sus habilidades e intereses
- Aceptan tareas y las completan a través de formularios web

#### 12.5.2. Factores de éxito
Para obtener resultados de calidad y atraer a los mejores trabajadores:
- **Compensación justa**: Establecer recompensas acordes a la complejidad y tiempo
- **Instrucciones claras**: Proporcionar directrices precisas y ejemplos
- **Interfaz intuitiva**: Diseñar HITs fáciles de entender y completar
- **Reputación**: Mantener una buena reputación entre los trabajadores (aprobando trabajo rápidamente, pagando justamente)

### 12.6. Consideraciones Prácticas

Al utilizar Mechanical Turk para proyectos de AI/ML, hay varios aspectos importantes a considerar.

#### 12.6.1. Calidad del trabajo
- Implementar verificaciones de control de calidad
- Utilizar tareas redundantes (asignar la misma tarea a múltiples trabajadores)
- Calificar a los trabajadores y filtrar por calificaciones

#### 12.6.2. Aspectos éticos
- Pagar salarios justos por el tiempo invertido
- Respetar la privacidad de los datos
- Ser transparente sobre el propósito del trabajo
- Considerar el impacto de la automatización en los trabajadores

#### 12.6.3. Limitaciones
- Variabilidad en la calidad del trabajo
- Posibles sesgos culturales o demográficos
- No adecuado para tareas que requieren alta especialización
- Consideraciones de confidencialidad con datos sensibles

---

## Resumen para el Examen

### Amazon Mechanical Turk - Conceptos Clave
- **Definición**: Plataforma de crowdsourcing que proporciona acceso a una fuerza laboral humana global para realizar tareas que las computadoras aún no pueden hacer eficazmente
- **Origen del nombre**: Basado en un "autómata" de ajedrez del siglo XVIII que en realidad era operado por un humano oculto
- **Modelo de trabajo**: Los solicitantes publican HITs (Human Intelligence Tasks) y los trabajadores los completan a cambio de pagos

### Principales Casos de Uso en ML
- Etiquetado de conjuntos de datos para entrenamiento de modelos
- Validación de resultados de modelos
- Revisión de recomendaciones de sistemas
- Anotación de datos complejos (texto, imágenes, audio)

### Integración con AWS
- **Amazon A2I**: Para revisión humana de predicciones de baja confianza
- **SageMaker Ground Truth**: Como fuente de fuerza laboral para etiquetado de datos

### Consideraciones Importantes
- El costo depende del precio por tarea y el volumen de datos
- La calidad del trabajo puede variar y requiere controles
- Adecuado para tareas simples, repetitivas que requieren inteligencia humana
- Ofrece escalabilidad para procesar grandes volúmenes de datos

**Consejo para el examen**: Recuerda que Mechanical Turk es una de las tres opciones de fuerza laboral disponibles en SageMaker Ground Truth (junto con fuerza laboral privada y proveedores externos). Es la opción más accesible y escalable para tareas generales, pero puede no ser apropiada para datos sensibles o que requieren conocimiento especializado. Comprende también que el costo total depende directamente del precio establecido por tarea y el volumen de datos, lo que puede hacer que proyectos grandes sean costosos a pesar del bajo precio unitario.


## 13. SageMaker Data Wrangler

### 13.1. Introducción a SageMaker Data Wrangler

SageMaker Data Wrangler representa una de las herramientas más directamente aplicables al data engineering dentro del ecosistema de SageMaker. Está diseñado específicamente para preparar datos para proyectos de machine learning de manera eficiente y visual.

#### 13.1.1. ¿Qué es Data Wrangler?
- Una herramienta ETL (Extract, Transform, Load) integrada en SageMaker Studio
- Similar a AWS Glue Data Studio, pero optimizada para flujos de trabajo de machine learning
- Un generador de código que produce las transformaciones necesarias para preparar datos

#### 13.1.2. Capacidades principales
SageMaker Data Wrangler ofrece cuatro funcionalidades esenciales:

1. **Importación de datos**: Conexión con múltiples fuentes de datos
2. **Visualización de datos**: Exploración y análisis de la distribución de los datos
3. **Transformación de datos**: Aplicación de más de 300 transformaciones predefinidas
4. **Evaluación con Quick Model**: Entrenamiento rápido de modelos para validar transformaciones

### 13.2. Arquitectura e Integración

Data Wrangler funciona como un puente entre las fuentes de datos y los diferentes servicios de procesamiento de SageMaker.

#### 13.2.1. Fuentes de datos compatibles
- **Amazon S3**: Para datos almacenados en buckets
- **AWS Lake Formation**: Para datos en lagos de datos
- **Amazon Athena**: Para consultas SQL en S3
- **SageMaker Feature Store**: Para reutilización de características
- **Amazon Redshift**: Para datos en data warehouses
- **Fuentes JDBC**: Para conexión con bases de datos externas y SaaS
- **Otras fuentes**: Como Databricks u otras plataformas externas

#### 13.2.2. Destinos de exportación
Una vez procesados los datos, pueden exportarse a:
- **SageMaker Processing**: Para entrenamiento de modelos
- **SageMaker Pipelines**: Como parte de flujos de trabajo automatizados
- **SageMaker Feature Store**: Para almacenar características procesadas
- **Jupyter Notebooks**: Como código Python ejecutable

#### 13.2.3. Naturaleza del resultado
Es crucial entender que Data Wrangler no ejecuta directamente las transformaciones en su pipeline de producción, sino que:
- Genera código Python que implementa las transformaciones definidas
- Este código puede integrarse en notebooks o pipelines
- Funciona como un generador de código para el flujo de procesamiento de datos

### 13.3. Flujo de Trabajo con Data Wrangler

A continuación, se detalla un flujo de trabajo típico utilizando SageMaker Data Wrangler.

#### 13.3.1. Importación de datos
1. Se accede a la pestaña "Import" en la interfaz de SageMaker Studio
2. Se selecciona la fuente de datos (por ejemplo, un archivo CSV en S3)
3. Se especifica la ubicación del archivo y su formato
4. Data Wrangler muestra automáticamente una vista previa de los datos
5. Se pueden verificar y modificar los tipos de datos inferidos

#### 13.3.2. Visualización y análisis
- Permite examinar la distribución de cada característica
- Ayuda a identificar valores atípicos o distribuciones inesperadas
- Proporciona estadísticas descriptivas de los datos
- Funciona como verificación de sanidad antes del entrenamiento

#### 13.3.3. Transformación de datos
Data Wrangler ofrece múltiples opciones para transformar datos:

- **Transformaciones predefinidas**: Más de 300 operaciones comunes
- **Custom Transforms**: Posibilidad de usar código personalizado en:
  - Pandas
  - PySpark
  - PySpark SQL

**Ejemplo de transformación**: One-hot encoding para variables categóricas
- Convierte categorías (como clases 1, 2, 3) en columnas binarias individuales
- Esencial para muchos algoritmos de machine learning que requieren entradas numéricas
- Mejora la capacidad del modelo para interpretar datos categóricos

#### 13.3.4. Evaluación con Quick Model
Una característica distintiva de Data Wrangler es la capacidad de evaluar transformaciones:

- Permite entrenar rápidamente un modelo con un subconjunto de datos
- Evalúa el impacto de las transformaciones en el rendimiento del modelo
- Facilita la experimentación con diferentes preparaciones de datos
- Ayuda a optimizar el pipeline de datos específicamente para el modelo destino

#### 13.3.5. Exportación del flujo de datos
Una vez definidas las transformaciones:
1. Se exporta el flujo como código Python en un Jupyter Notebook
2. Este código implementa todo el proceso ETL definido visualmente
3. Puede integrarse en pipelines de entrenamiento o inferencia
4. Permite aplicar las mismas transformaciones a nuevos datos consistentemente

### 13.4. Ejemplo Práctico: Dataset del Titanic

El ejemplo mostrado en la transcripción utiliza el conocido dataset del Titanic para ilustrar el flujo de trabajo.

#### 13.4.1. Importación del dataset
- Archivo CSV que contiene información de pasajeros del Titanic
- Incluye datos como clase de pasajero, supervivencia, nombre, edad, etc.
- La interfaz permite previsualizar los datos y verificar tipos

#### 13.4.2. Visualización de distribuciones
- Se examina la distribución de edades para verificar que sea razonable
- Permite detectar anomalías o sesgos en los datos antes de proceder

#### 13.4.3. Transformación: One-hot encoding
- Se aplica "encode categorical" a la columna "class"
- Convierte la clase (1, 2, 3) en tres columnas binarias
- Más adecuado para modelos de ML que no manejan bien valores categóricos directamente

#### 13.4.4. Evaluación y exportación
- Se puede utilizar Quick Model para evaluar el impacto de las transformaciones
- Finalmente, se exporta el flujo como código Python ejecutable

### 13.5. Solución de Problemas Comunes

Al trabajar con Data Wrangler, pueden surgir algunos problemas típicos que conviene conocer.

#### 13.5.1. Problemas de permisos
- Asegurarse de que el usuario de SageMaker Studio tenga los roles IAM apropiados
- Verificar que las fuentes de datos permitan acceso a Data Wrangler
- La política "AmazonSageMakerFullAccess" suele ser necesaria para las fuentes de datos

#### 13.5.2. Límites de recursos
Si aparece un error como "The following instance type is not available", probablemente indica un problema de cuota:
1. Acceder a Service Quotas en la consola AWS
2. Navegar a Amazon SageMaker → Studio Kernel Gateway apps
3. Solicitar un aumento de cuota para instancias "ml.m5.4xlarge"
4. Esperar la aprobación del aumento de cuota

---

## Resumen para el Examen

### SageMaker Data Wrangler - Conceptos Clave
- **Definición**: Herramienta ETL visual integrada en SageMaker Studio para preparación de datos de ML
- **Función principal**: Generador de código Python que implementa transformaciones definidas visualmente
- **Capacidades**: Importación, visualización, transformación y evaluación de datos

### Arquitectura y Flujo
| Fase | Descripción | Características |
|------|-------------|-----------------|
| **Importación** | Conexión con fuentes de datos | S3, Athena, Redshift, Feature Store, JDBC |
| **Visualización** | Exploración de distribuciones | Histogramas, estadísticas, detección de anomalías |
| **Transformación** | Aplicación de operaciones a los datos | +300 transformaciones predefinidas, código personalizado |
| **Evaluación** | Quick Model para validar transformaciones | Entrenamiento rápido para verificar impacto |
| **Exportación** | Generación de código Python | Jupyter Notebooks, integración con procesos |

### Puntos Importantes
- Data Wrangler NO ejecuta las transformaciones en producción; genera código que implementa esas transformaciones
- La funcionalidad Quick Model permite evaluar el impacto de las transformaciones en el modelo antes de implementarlas
- Admite código personalizado en Pandas, PySpark y PySpark SQL para transformaciones no estándar
- Es fundamental verificar permisos IAM y cuotas de servicio para evitar problemas comunes

**Consejo para el examen**: Recuerda que Data Wrangler es principalmente una herramienta de generación de código, no un servicio que ejecuta transformaciones en tiempo real. Entiende la diferencia entre Data Wrangler y otros servicios como Glue (Data Wrangler está optimizado específicamente para preparación de datos de ML). Presta atención a la funcionalidad Quick Model, que es única y permite evaluar rápidamente el impacto de las transformaciones en el rendimiento del modelo.


## 14. SageMaker Model Monitor and SageMaker Clarify

### 14.1. Introducción a SageMaker Model Monitor

Los modelos de machine learning desplegados en producción no mantienen necesariamente su rendimiento de forma constante a lo largo del tiempo. La naturaleza de los datos puede cambiar, aparecen nuevos patrones, o surgen problemas inesperados que pueden afectar la calidad de las predicciones. SageMaker Model Monitor es una solución diseñada para abordar estos desafíos.

#### 14.1.1. Propósito y funcionalidades principales
SageMaker Model Monitor es un servicio que permite:
- Monitorear de forma continua los modelos desplegados en producción
- Detectar desviaciones en la calidad de los datos o del modelo
- Recibir alertas automáticas cuando se identifican anomalías
- Visualizar cambios y tendencias en los datos a lo largo del tiempo

#### 14.1.2. Beneficios clave
- **Detección proactiva**: Identifica problemas antes de que afecten significativamente el rendimiento del modelo
- **Sin código**: Puede configurarse y gestionarse visualmente desde SageMaker Studio
- **Automatización**: Programa tareas de monitoreo que se ejecutan sin intervención manual
- **Integración completa**: Trabaja con otros servicios AWS como CloudWatch para notificaciones

### 14.2. Detección de Data Drift

Uno de los problemas más comunes en modelos desplegados es el data drift, donde las características de los datos de entrada cambian con el tiempo.

#### 14.2.1. Tipos de data drift que puede detectar
- **Cambios en distribuciones estadísticas**: Variaciones en promedios, desviaciones estándar u otras propiedades
- **Anomalías y outliers**: Valores atípicos que no encajan en los patrones esperados
- **Características faltantes**: Datos incompletos o atributos ausentes
- **Nuevas características**: Aparición de atributos que no estaban presentes en los datos de entrenamiento

#### 14.2.2. Ejemplo práctico
En un modelo para evaluar solvencia crediticia, SageMaker Model Monitor podría detectar:
- Cambios en la distribución de edades o ingresos de los solicitantes
- Valores atípicos en la cantidad solicitada
- Información faltante en campos importantes
- Nuevos campos de datos que no se consideraron en el entrenamiento original

### 14.3. SageMaker Clarify

SageMaker Clarify complementa a Model Monitor proporcionando explicabilidad y detección de sesgos en modelos de machine learning.

#### 14.3.1. Funcionalidades principales
- **Detección de sesgos**: Identifica desequilibrios en las predicciones entre diferentes grupos
- **Explicabilidad del modelo**: Determina qué características tienen mayor influencia en las predicciones
- **Transparencia**: Ayuda a entender por qué un modelo toma determinadas decisiones

#### 14.3.2. Métricas de sesgo
SageMaker Clarify ofrece diversas métricas para medir sesgos:
- **Kullback-Leibler divergence**: Mide la diferencia entre distribuciones de probabilidad
- **Otras métricas especializadas**: Incluye múltiples medidas para diferentes tipos de sesgos y aplicaciones

Estas métricas permiten identificar si el modelo está favoreciendo o perjudicando sistemáticamente a ciertos grupos en sus predicciones.

### 14.4. Tipos de Monitoreo

SageMaker Model Monitor ofrece varios tipos específicos de monitoreo adaptados a diferentes aspectos del rendimiento del modelo.

#### 14.4.1. Monitoreo de calidad de datos
- **Definición**: Compara las propiedades estadísticas de los datos de producción con una línea base establecida
- **Configuración**: Requiere definir qué constituye "calidad" para el caso específico
- **Detección**: Alerta cuando las características de los datos se desvían significativamente de la línea base

#### 14.4.2. Monitoreo de calidad del modelo
- **Definición**: Evalúa la precisión y rendimiento general del modelo en producción
- **Método**: Compara las predicciones del modelo con una línea base de precisión esperada
- **Integración con ground truth**: Puede comparar las predicciones con etiquetas generadas por humanos

#### 14.4.3. Monitoreo de deriva de sesgo
- **Definición**: Detecta si el sesgo en las predicciones cambia con el tiempo
- **Integración**: Trabaja con SageMaker Clarify para identificar sesgos en diferentes grupos
- **Aplicación**: Especialmente importante en modelos con impacto en decisiones que afectan a personas

#### 14.4.4. Monitoreo de atribución de características
- **Definición**: Supervisa cómo cambia la importancia relativa de las características en las predicciones
- **Propósito**: Identifica si el modelo está comenzando a depender de características diferentes
- **Beneficio**: Ayuda a entender si el comportamiento fundamental del modelo está cambiando

### 14.5. Arquitectura e Implementación

#### 14.5.1. Almacenamiento y seguridad
- Los datos de monitoreo se almacenan en Amazon S3
- Están protegidos con las políticas de seguridad estándar de AWS
- Solo los usuarios autorizados pueden acceder a la información de monitoreo

#### 14.5.2. Programación y ejecución
- Las tareas de monitoreo se configuran mediante "monitoring schedules"
- Pueden ejecutarse con la frecuencia necesaria (horaria, diaria, semanal, etc.)
- El proceso es automatizado una vez configurado

#### 14.5.3. Integración con CloudWatch
- Las métricas recopiladas se envían a Amazon CloudWatch
- Las alarmas de CloudWatch pueden configurarse para notificar cuando se detecten anomalías
- Las notificaciones pueden enviarse por email, SMS, o desencadenar acciones correctivas automatizadas

#### 14.5.4. Visualización
SageMaker Model Monitor ofrece múltiples opciones de visualización:
- Visualizaciones integradas en SageMaker Studio
- Integración con TensorBoard para análisis detallados
- Compatibilidad con herramientas de BI como Amazon QuickSight y Tableau

### 14.6. Flujo de Trabajo Típico

Un flujo de trabajo común con SageMaker Model Monitor incluye:

1. **Establecer líneas base**: Definir los parámetros normales de datos y rendimiento del modelo
2. **Configurar monitoreo**: Seleccionar tipos de monitoreo y programar ejecuciones
3. **Implementar alertas**: Configurar notificaciones en CloudWatch para desviaciones
4. **Revisar métricas**: Analizar tendencias y patrones en los datos monitoreados
5. **Tomar acciones correctivas**: Reentrenar modelos o ajustar pipelines de datos cuando sea necesario

### 14.7. Acciones Correctivas

Cuando Model Monitor detecta problemas, las acciones correctivas habituales incluyen:

- **Reentrenamiento del modelo**: Actualizar el modelo con datos más recientes
- **Auditoría de datos**: Investigar y corregir problemas en las fuentes de datos
- **Ajuste de características**: Modificar el conjunto de características utilizadas
- **Revisión de sesgos**: Implementar técnicas para mitigar sesgos identificados
- **Actualización de líneas base**: Ajustar los umbrales de monitoreo si han cambiado las expectativas

---

## Resumen para el Examen

### SageMaker Model Monitor - Conceptos Clave
- **Propósito**: Monitorea modelos ML en producción para detectar degradación de rendimiento
- **Funcionamiento**: Compara métricas de datos y modelo con líneas base establecidas
- **Automatización**: Programa tareas de monitoreo sin necesidad de código
- **Alertas**: Integra con CloudWatch para notificar sobre anomalías

### Tipos de Monitoreo

| Tipo | Descripción | Detecta |
|------|-------------|---------|
| **Calidad de Datos** | Evalúa propiedades estadísticas de datos entrantes | Cambios en distribuciones, outliers, datos faltantes |
| **Calidad del Modelo** | Evalúa precisión y rendimiento | Degradación de precisión, comparación con ground truth |
| **Deriva de Sesgo** | Monitorea desequilibrios entre grupos | Cambios en patrones de sesgo (con SageMaker Clarify) |
| **Atribución de Características** | Supervisa importancia de características | Cambios en qué características influyen en predicciones |

### SageMaker Clarify
- **Propósito**: Proporciona explicabilidad y detección de sesgos
- **Integración**: Trabaja con Model Monitor para supervisar sesgos a lo largo del tiempo
- **Métricas**: Incluye Kullback-Leibler divergence y otras medidas de sesgo
- **Valor**: Ayuda a entender por qué un modelo hace determinadas predicciones

### Aspectos Prácticos
- Datos de monitoreo almacenados de forma segura en S3
- Programación flexible mediante monitoring schedules
- Visualización disponible en SageMaker Studio, TensorBoard, QuickSight o Tableau
- Acciones correctivas incluyen reentrenamiento o auditoría de datos

**Consejo para el examen**: Recuerda la diferencia entre los cuatro tipos de monitoreo (calidad de datos, calidad del modelo, deriva de sesgo y atribución de características) y cómo se integran con otros servicios AWS. Comprende que Model Monitor es principalmente una herramienta de detección que envía métricas a CloudWatch, pero las acciones correctivas (como reentrenar el modelo) deben implementarse por separado.


## 15. Partial Dependence Plots (PDPs), Shapley values, and SHAP

El examen AWS Certified Machine Learning - Associate espera un conocimiento detallado sobre el funcionamiento de SageMaker Clarify y sus métodos de interpretabilidad de modelos. En esta sección exploraremos las herramientas y técnicas fundamentales que Clarify utiliza para explicar las predicciones de los modelos.

### 15.1. Partial Dependence Plots (PDPs)

Los gráficos de dependencia parcial (PDPs) son una herramienta visual clave para entender cómo influyen las características individuales en las predicciones de un modelo.

#### 15.1.1. Definición y propósito
Un PDP muestra la dependencia de la variable objetivo predicha respecto a un conjunto de características de entrada, manteniendo todas las demás características constantes.

#### 15.1.2. Interpretación
- El eje X representa los valores de una característica específica (generalmente agrupados en rangos)
- El eje Y muestra los valores de predicción del modelo
- La línea o puntos indican cómo varía la predicción al cambiar únicamente esa característica

#### 15.1.3. Ejemplo práctico
Como se muestra en la transcripción, si analizamos un PDP para la característica "edad":
- Podemos ver que las predicciones del modelo cambian significativamente hasta aproximadamente los 50 años
- Después de los 50 años, las predicciones permanecen relativamente constantes
- Esto podría indicar que la edad es un factor importante para el modelo, pero con un "punto de inflexión" alrededor de los 50 años

#### 15.1.4. Identificación de desbalances
Los PDPs pueden revelar posibles problemas en los datos de entrenamiento:
- Un "plateau" o meseta después de cierto valor podría indicar escasez de datos en ese rango
- Cambios abruptos pueden señalar áreas donde el modelo podría estar sobreajustando
- Patrones inusuales pueden alertar sobre sesgos potenciales

#### 15.1.5. Datos adicionales disponibles
Además de los gráficos visuales, SageMaker Clarify también proporciona:
- Distribuciones de datos para cada rango de valores
- Datos numéricos subyacentes a través de la API
- Estadísticas que pueden revelar desbalances de clases en los datos

### 15.2. Shapley Values

Los valores de Shapley son el fundamento teórico sobre el que operan muchas de las explicaciones de Clarify.

#### 15.2.1. Origen e idea fundamental
- Concepto originado en la teoría de juegos, aplicado al machine learning
- Asigna a cada característica una "contribución" a la predicción final
- Responde a la pregunta: "¿Cuánto contribuye cada característica al resultado?"

#### 15.2.2. Metodología conceptual
El enfoque básico de los valores de Shapley es:
1. Remover una característica del modelo
2. Medir el impacto que tiene esta remoción en la predicción
3. Repetir este proceso con diferentes combinaciones de características
4. Asignar un valor de importancia basado en estos cambios

#### 15.2.3. Desafío computacional
El cálculo exacto de los valores de Shapley presenta desafíos:
- Requiere evaluar todas las posibles combinaciones de características
- Con n características, hay 2^n combinaciones posibles
- Para modelos con muchas características, esto se vuelve computacionalmente prohibitivo

### 15.3. SHAP (SHapley Additive exPlanations)

Debido a la complejidad computacional de los valores de Shapley exactos, SageMaker Clarify utiliza una aproximación llamada SHAP.

#### 15.3.1. Definición y propósito
SHAP (Shapley Additive exPlanations) es:
- Un método de aproximación para calcular valores de Shapley
- Una técnica más eficiente que mantiene las propiedades teóricas importantes
- Una forma de hacer que la explicabilidad sea factible en modelos complejos

#### 15.3.2. Ventajas de SHAP
- Proporciona explicaciones locales (para predicciones individuales)
- Ofrece también explicaciones globales (importancia general de características)
- Mantiene consistencia y precisión en comparación con los valores de Shapley exactos
- Es computacionalmente más eficiente

#### 15.3.3. Implementación en SageMaker Clarify
SageMaker Clarify utiliza SHAP internamente para:
- Determinar la importancia relativa de las características
- Identificar posibles sesgos en el modelo
- Proporcionar explicaciones comprensibles de las predicciones

### 15.4. Asymmetric Shapley Values para Series Temporales

Cuando trabajamos con datos de series temporales, se requiere un enfoque especializado debido a la naturaleza secuencial de los datos.

#### 15.4.1. Contexto específico
- Los datos de series temporales presentan dependencias temporales
- La importancia de una característica puede variar en diferentes puntos temporales
- Las técnicas estándar no capturan adecuadamente estas dependencias

#### 15.4.2. Asymmetric Shapley Values
Para datos de series temporales, SageMaker Clarify utiliza valores de Shapley asimétricos:
- Evalúan la contribución de las características en cada paso temporal
- Consideran la importancia acumulativa a lo largo del tiempo
- Toman en cuenta la dirección temporal (pasado → futuro)

#### 15.4.3. Beneficios para análisis de series temporales
- Permite entender qué características influyen en cada punto de la predicción
- Identifica patrones temporales en la importancia de las características
- Facilita la detección de anomalías o cambios en la influencia de las características a lo largo del tiempo

### 15.5. Aplicaciones Prácticas en AWS

Los métodos de explicabilidad que hemos discutido son fundamentales para varios servicios y casos de uso en AWS.

#### 15.5.1. Integración con SageMaker Model Monitor
- Monitoreo de atribución de características basado en valores SHAP
- Alertas cuando la importancia de las características cambia significativamente
- Detección de drift en la relación entre características y predicciones

#### 15.5.2. Casos de uso comunes
- **Cumplimiento regulatorio**: Explicabilidad para sectores regulados como finanzas o salud
- **Depuración de modelos**: Identificación de características problemáticas
- **Mejora de modelos**: Enfoque en las características más influyentes
- **Detección de sesgos**: Identificación de patrones injustos en las predicciones

---

## Resumen para el Examen

### Conceptos Clave

| Concepto | Descripción | Propósito |
|----------|-------------|-----------|
| **Partial Dependence Plots (PDPs)** | Gráficos que muestran cómo una característica afecta las predicciones | Visualizar el impacto de características individuales |
| **Shapley Values** | Concepto de teoría de juegos aplicado a ML para atribución de importancia | Cuantificar la contribución de cada característica |
| **SHAP** | Método de aproximación para calcular valores de Shapley | Hacer factible la explicabilidad en modelos complejos |
| **Asymmetric Shapley Values** | Adaptación para datos de series temporales | Explicar predicciones que dependen del tiempo |

### Puntos Importantes para el Examen
- **PDPs**: Permiten visualizar cómo diferentes valores de una característica influyen en las predicciones del modelo y pueden revelar posibles problemas en los datos de entrenamiento.
- **Shapley Values**: Son el fundamento teórico de las explicaciones de Clarify, pero su cálculo exacto es computacionalmente costoso para modelos con muchas características.
- **SHAP**: Es la implementación práctica que SageMaker Clarify utiliza para aproximar los valores de Shapley de manera eficiente.
- **Asymmetric Shapley Values**: Son específicos para datos de series temporales y evalúan la contribución de características en cada paso temporal.

**Consejo para el examen**: Asegúrate de comprender la relación entre estos conceptos. Los PDPs son herramientas visuales para interpretar el impacto de las características, mientras que los valores de Shapley (aproximados mediante SHAP) son el mecanismo subyacente que cuantifica ese impacto. Para series temporales, recuerda que se utilizan valores de Shapley asimétricos. Estos conceptos son fundamentales para explicar cómo SageMaker Clarify detecta y monitorea sesgos en los modelos.



## 16. SageMaker Feature Store

### 16.1. Concepto de Features en Machine Learning

Antes de adentrarnos en SageMaker Feature Store, es importante comprender a qué nos referimos con "features" en el contexto de machine learning.

#### 16.1.1. Definición de features
En machine learning, una feature (característica) es una propiedad o atributo medible utilizado para entrenar un modelo. Conceptualmente, las features son:
- Columnas o campos en un conjunto de datos
- Propiedades individuales que describen cada ejemplo o instancia
- Información que el modelo utiliza para hacer predicciones

#### 16.1.2. Ejemplo práctico
En un escenario donde intentamos predecir la afiliación política de una persona:
- **Features**: Dirección, ingresos, edad, etc.
- **Label (etiqueta)**: Partido político (lo que intentamos predecir)

Las features proporcionan la información que el modelo necesita para aprender patrones y realizar predicciones precisas.

### 16.2. Introducción a SageMaker Feature Store

SageMaker Feature Store es un repositorio centralizado para almacenar, gestionar y reutilizar features para el entrenamiento y la inferencia de modelos de machine learning.

#### 16.2.1. Propósito y necesidad
Los modelos de machine learning requieren:
- Acceso rápido a grandes volúmenes de datos
- Seguridad para información potencialmente sensible
- Organización efectiva de datos
- Capacidad para compartir features entre diferentes modelos

Feature Store resuelve estos desafíos proporcionando un repositorio centralizado y optimizado para features.

#### 16.2.2. Beneficios clave
- **Reutilización**: Evita duplicación de features para diferentes modelos
- **Consistencia**: Garantiza que las mismas features se utilicen en entrenamiento e inferencia
- **Eficiencia**: Optimiza el acceso y almacenamiento de datos
- **Gobierno**: Facilita el seguimiento y control de versiones de features

### 16.3. Arquitectura de SageMaker Feature Store

SageMaker Feature Store se sitúa como un componente central en el ecosistema de procesamiento de datos y machine learning.

#### 16.3.1. Posición en el ecosistema AWS
Feature Store puede recibir datos de múltiples fuentes:
- SageMaker Studio
- Pipelines (Step Functions, SageMaker Pipelines, Apache Airflow)
- Procesamiento distribuido (EMR, Glue, SageMaker Processing)
- Streaming (Kinesis, Kafka)
- Herramientas de preparación de datos (SageMaker Data Wrangler, Glue DataBrew)

#### 16.3.2. Estructura organizativa
Feature Store organiza las features en:
- **Feature Store**: El repositorio completo que contiene todas las features
- **Feature Groups**: Agrupaciones lógicas de features relacionadas
  
Cada Feature Group contiene:
- **Record Identifiers**: Identificadores únicos para cada registro
- **Feature Names**: Nombres de las features almacenadas
- **Event Times**: Marcas temporales asociadas a cada feature

### 16.4. Modos de Funcionamiento

SageMaker Feature Store opera en dos modos principales, permitiendo tanto acceso en tiempo real como procesamiento por lotes.

#### 16.4.1. Almacenamiento online (Online Store)
- **Propósito**: Servir features con baja latencia para inferencia en tiempo real
- **Acceso**: A través de la API GetRecord
- **Casos de uso**: Aplicaciones que requieren respuestas inmediatas (recomendaciones en tiempo real, detección de fraude, etc.)
- **Característica**: Optimizado para consultas rápidas por ID de registro

#### 16.4.2. Almacenamiento offline (Offline Store)
- **Propósito**: Almacenar grandes volúmenes de datos históricos para entrenamiento y análisis
- **Ubicación**: Amazon S3
- **Acceso**: Compatible con herramientas como Athena, Data Wrangler, o cualquier servicio que pueda leer de S3
- **Casos de uso**: Entrenamiento de modelos, análisis histórico, exploración de datos
- **Característica**: Optimizado para procesamiento por lotes y análisis

### 16.5. Flujos de Datos en Feature Store

Feature Store admite dos patrones principales para la ingesta y el consumo de datos.

#### 16.5.1. Flujo de streaming
1. Los datos fluyen desde fuentes de streaming (Kinesis, MSK, Apache Spark, etc.)
2. Se utiliza la API PutRecord para insertar datos en Feature Store
3. Feature Store procesa los datos y los almacena simultáneamente en:
   - Online Store para acceso de baja latencia
   - Offline Store (S3) para almacenamiento histórico
4. Las aplicaciones pueden acceder a las features en tiempo real mediante la API GetRecord

#### 16.5.2. Flujo por lotes
1. Los datos se cargan directamente en el Offline Store (S3)
2. Feature Store automáticamente crea un catálogo de datos en AWS Glue
3. Los datos pueden ser consultados y procesados mediante servicios como Athena
4. Opcionalmente, los datos pueden ser sincronizados con el Online Store para inferencia en tiempo real

#### 16.5.3. Flexibilidad de implementación
Feature Store ofrece flexibilidad para adaptarse a diferentes requisitos:
- Puede utilizarse solo el Offline Store para casos de uso por lotes
- Puede utilizarse solo el Online Store para casos de uso de baja latencia
- Pueden utilizarse ambos almacenes para aprovechar las ventajas de cada uno

### 16.6. Seguridad en Feature Store

SageMaker Feature Store incorpora múltiples capas de seguridad para proteger datos potencialmente sensibles.

#### 16.6.1. Cifrado
- Cifrado automático de datos en reposo (en almacenamiento)
- Cifrado automático de datos en tránsito (durante transferencia)
- Soporte para claves maestras de cliente (CMK) a través de AWS KMS

#### 16.6.2. Control de acceso
- Control de acceso granular mediante políticas de IAM
- Posibilidad de definir permisos a nivel de Feature Group
- Segregación de acceso entre equipos o proyectos

#### 16.6.3. Conectividad segura
- Soporte para AWS PrivateLink
- Posibilidad de acceder a Feature Store sin exponer tráfico a internet pública
- Integración con VPC para mayor aislamiento

### 16.7. Casos de Uso Comunes

SageMaker Feature Store es especialmente útil en varios escenarios de machine learning.

#### 16.7.1. Entrenamiento de modelos
- Almacenamiento consistente de features para entrenar múltiples modelos
- Captura de point-in-time para entrenamiento con datos históricos
- Reutilización de features para diferentes proyectos

#### 16.7.2. Inferencia en tiempo real
- Acceso de baja latencia a features actualizadas
- Consistencia entre entrenamiento e inferencia
- Reducción del trabajo de preprocesamiento en tiempo de inferencia

#### 16.7.3. Colaboración entre equipos
- Repositorio centralizado para compartir features entre equipos
- Documentación y metadatos sobre features
- Reducción de la duplicación de esfuerzos

---

## Resumen para el Examen

### SageMaker Feature Store - Conceptos Clave
- **Definición**: Repositorio centralizado para almacenar, gestionar y compartir features de machine learning
- **Propósito**: Proporcionar acceso rápido, seguro y consistente a features para entrenamiento e inferencia
- **Estructura**: Organiza features en Feature Groups, cada una con identificadores de registro, nombres de features y marcas temporales

### Modos de Almacenamiento

| Almacenamiento | Propósito | Características | Casos de Uso |
|----------------|-----------|-----------------|--------------|
| **Online Store** | Inferencia en tiempo real | Baja latencia, acceso por API GetRecord | Predicciones en tiempo real, sistemas interactivos |
| **Offline Store** | Entrenamiento e histórico | Basado en S3, compatible con análisis, catálogo en AWS Glue | Entrenamiento de modelos, análisis histórico |

### Flujos de Datos
- **Streaming**: API PutRecord para insertar datos en tiempo real, almacenados tanto en Online como en Offline Store
- **Batch**: Carga directa en Offline Store (S3), consulta mediante Athena u otras herramientas

### Seguridad
- Cifrado automático en reposo y en tránsito
- Soporte para claves KMS personalizadas
- Control de acceso granular mediante IAM
- Integración con PrivateLink para mayor seguridad de red

**Consejo para el examen**: Comprende bien la diferencia entre Online Store y Offline Store, así como sus casos de uso. El Online Store está optimizado para inferencia en tiempo real con baja latencia, mientras que el Offline Store (S3) está diseñado para entrenamiento por lotes y análisis. Feature Store no solo almacena datos, sino que proporciona una estructura organizativa (Feature Groups) y garantiza consistencia entre entrenamiento e inferencia.

## 17. SageMaker Canvas

### 17.1. Introducción a SageMaker Canvas

SageMaker Canvas representa la solución de AWS para democratizar el machine learning, permitiendo que personas sin conocimientos de programación o experiencia en ML puedan crear y utilizar modelos predictivos.

#### 17.1.1. Definición y concepto
SageMaker Canvas es:
- Un entorno de machine learning **sin código** (no-code)
- Orientado principalmente a analistas de negocio y usuarios no técnicos
- Una plataforma visual para crear modelos predictivos sin necesidad de programar
- Una forma de implementar AutoML (Automated Machine Learning)

#### 17.1.2. Relevancia para el examen
Según la transcripción, SageMaker Canvas aparecerá con certeza en el examen AWS Certified Machine Learning - Associate, probablemente con una o dos preguntas. Es importante entender su propósito y capacidades básicas.

### 17.2. Principales Capacidades

SageMaker Canvas ofrece diversas funcionalidades que facilitan el ciclo de vida completo del machine learning para usuarios no técnicos.

#### 17.2.1. Preparación de datos
- **Importación**: Acepta archivos CSV como fuente de datos
- **Unión de datos**: Permite combinar múltiples conjuntos de datos
- **Limpieza automática**: Detecta y maneja automáticamente:
  - Valores faltantes
  - Valores atípicos (outliers)
  - Filas duplicadas
  - Otros problemas comunes de calidad de datos

#### 17.2.2. Construcción de modelos
- **Selección de objetivo**: El usuario simplemente especifica la columna que desea predecir
- **AutoML**: Utiliza técnicas automatizadas para:
  - Seleccionar algoritmos apropiados
  - Ajustar hiperparámetros
  - Evaluar diferentes modelos
  - Elegir el mejor modelo para el caso de uso
- **Tipos de predicción**:
  - Clasificación (para predecir categorías)
  - Regresión (para predecir valores numéricos)

#### 17.2.3. Predicciones y uso
- **Generación de predicciones**: Permite usar el modelo para hacer predicciones sobre nuevos datos
- **Visualización de resultados**: Muestra resultados de forma comprensible y visual
- **Retroalimentación**: Facilita el mejoramiento iterativo de los modelos

### 17.3. Integración con SageMaker Studio

Canvas no es un entorno aislado, sino que se integra con otras partes del ecosistema SageMaker.

#### 17.3.1. Compartir recursos
- Capacidad para compartir modelos con usuarios de SageMaker Studio
- Posibilidad de compartir los conjuntos de datos utilizados en el entrenamiento

#### 17.3.2. Flujo de trabajo colaborativo
- Los analistas de negocio pueden crear modelos iniciales en Canvas
- Los ingenieros de ML pueden importar estos modelos a SageMaker Studio
- Permite refinamiento adicional y personalización por parte de especialistas

#### 17.3.3. Exportación de modelos
- Los modelos creados en Canvas pueden exportarse a SageMaker Studio
- Facilita la transición de un prototipo rápido a una implementación más robusta
- Permite aprovechar lo mejor de ambos entornos

### 17.4. Capacidades de IA Generativa

Una característica destacada de las versiones recientes de SageMaker Canvas es su soporte para modelos de IA generativa.

#### 17.4.1. Acceso a modelos base
- Integración con modelos de Amazon Bedrock
- Acceso a modelos disponibles en SageMaker JumpStart
- Interfaz simplificada para utilizar modelos de lenguaje de gran escala

#### 17.4.2. Personalización de modelos
- Capacidad para realizar fine-tuning (ajuste fino) de modelos base
- Adaptación de modelos generativos a datos y casos de uso específicos
- Todo sin necesidad de experiencia en IA o ML

#### 17.4.3. Creación de aplicaciones generativas
- Permite desarrollar aplicaciones basadas en IA generativa
- Facilita la implementación de casos de uso como:
  - Generación de texto
  - Resumen de documentos
  - Respuesta a preguntas
  - Otras aplicaciones de procesamiento de lenguaje natural

### 17.5. Casos de Uso Ideales

SageMaker Canvas es particularmente adecuado para ciertos escenarios y usuarios.

#### 17.5.1. Usuarios objetivo
- Analistas de negocio sin conocimientos de programación
- Profesionales de dominio con experiencia en sus campos pero sin habilidades técnicas de ML
- Equipos que necesitan prototipos rápidos antes de involucrar a especialistas

#### 17.5.2. Escenarios de aplicación
- **Análisis predictivo**: Previsión de ventas, estimación de demanda
- **Segmentación de clientes**: Clasificación basada en comportamientos o características
- **Detección de anomalías**: Identificación de transacciones inusuales
- **Análisis de sentimiento**: Evaluación de feedback de clientes
- **Generación de contenido**: Creación de textos, resúmenes o respuestas utilizando IA generativa

#### 17.5.3. Ventajas para la organización
- Acelera la adopción de ML en diferentes departamentos
- Reduce la dependencia de recursos técnicos escasos
- Permite a los expertos de dominio aplicar ML directamente a sus problemas
- Sirve como puente entre el negocio y los equipos técnicos

---

## Resumen para el Examen

### SageMaker Canvas - Conceptos Clave
- **Definición**: Entorno de machine learning sin código (no-code) para usuarios no técnicos
- **Usuarios objetivo**: Principalmente analistas de negocio sin experiencia en programación o ML
- **Funcionalidad principal**: Permite crear modelos predictivos automáticamente a partir de datos CSV

### Capacidades Principales

| Categoría | Características |
|-----------|-----------------|
| **Preparación de datos** | Importación de CSV, unión de datos, limpieza automática (valores faltantes, outliers, duplicados) |
| **Construcción de modelos** | AutoML, selección de columna objetivo, clasificación y regresión |
| **Colaboración** | Compartir modelos y datos con SageMaker Studio, exportación de modelos |
| **IA Generativa** | Acceso a modelos de Bedrock y JumpStart, capacidad de fine-tuning, creación de aplicaciones generativas |

### Beneficios Clave
- Democratiza el acceso al ML para usuarios no técnicos
- Automatiza tareas complejas como limpieza de datos y selección de algoritmos
- Integra flujos de trabajo entre usuarios técnicos y no técnicos
- Permite aprovechar la IA generativa sin conocimientos especializados

**Consejo para el examen**: Recuerda que SageMaker Canvas es más que una simple herramienta de AutoML; también ofrece capacidades de preparación de datos, limpieza automática e integración con modelos generativos. Su valor principal está en permitir que usuarios sin conocimientos técnicos creen y utilicen modelos de machine learning, funcionando como "ML para las masas".


## 18. AWS Glue

### 18.1. Introducción a AWS Glue

AWS Glue es un servicio fundamental para la preparación y transformación de datos dentro del ecosistema AWS. Según la transcripción, este tema tiene una gran relevancia en el examen AWS Certified Machine Learning - Associate.

#### 18.1.1. Definición y propósito
AWS Glue es:
- Un servicio serverless (sin servidor) para ETL (Extract, Transform, Load)
- Un sistema que descubre y define automáticamente esquemas de datos no estructurados
- Un repositorio centralizado de metadatos para lagos de datos (data lakes)
- Un servicio que permite consultar datos no estructurados usando herramientas SQL

#### 18.1.2. Importancia en el examen
La transcripción menciona explícitamente que AWS Glue "es un gran tema en el examen" y que "juega un papel importante en el examen actual", por lo que es crucial comprender bien este servicio y su integración con otras herramientas de AWS.

### 18.2. Componentes Principales de AWS Glue

AWS Glue consta de varios componentes clave que trabajan juntos para proporcionar sus funcionalidades de ETL y catalogación.

#### 18.2.1. Glue Crawler
- **Propósito**: Escanea datos en fuentes como S3 para inferir su esquema
- **Funcionamiento**: Analiza la estructura de los datos y determina automáticamente tipos de datos, nombres de columnas, etc.
- **Programación**: Puede ejecutarse periódicamente o bajo demanda
- **Utilidad**: Permite descubrir y mantener actualizados los esquemas a medida que cambian los datos

#### 18.2.2. Glue Data Catalog
- **Propósito**: Actúa como repositorio centralizado de metadatos
- **Contenido**: Almacena definiciones de tablas, esquemas y ubicaciones de datos
- **Integración**: Se integra con servicios como Amazon Athena, Redshift, EMR y otras herramientas SQL
- **Característica clave**: Los datos originales permanecen en su ubicación (como S3) mientras que el catálogo solo almacena metadatos

#### 18.2.3. Glue ETL (Jobs)
- **Propósito**: Transformar datos según necesidades específicas
- **Tecnología subyacente**: Utiliza Apache Spark sin necesidad de gestionar el clúster
- **Activación**: Puede ejecutarse por eventos (llegada de nuevos datos), según un horario programado o bajo demanda
- **Ventaja**: Totalmente gestionado, sin infraestructura que mantener

### 18.3. Particionamiento de Datos en S3 para Glue

La organización de los datos en S3 afecta significativamente la eficiencia de AWS Glue y de las consultas posteriores.

#### 18.3.1. Importancia del particionamiento
- El particionamiento adecuado permite consultas más eficientes
- Glue Crawler extrae particiones basadas en la estructura de directorios en S3
- La organización debe planificarse considerando los patrones de consulta más comunes

#### 18.3.2. Estrategias de particionamiento
- **Particionamiento por tiempo**: Estructura como `/año/mes/día/`
  - Ideal para consultas basadas principalmente en rangos temporales
  - Ejemplo: Datos de sensores consultados por periodos específicos

- **Particionamiento por entidad**: Estructura como `/dispositivo/año/mes/día/`
  - Ideal para consultas basadas principalmente en entidades específicas
  - Ejemplo: Buscar todos los datos de un dispositivo particular independientemente del tiempo

#### 18.3.3. Ejemplo práctico
Para datos de sensores que envían información cada hora:
- Si las consultas son principalmente por tiempo: `/año/mes/día/dispositivo/`
- Si las consultas son principalmente por dispositivo: `/dispositivo/año/mes/día/`

La elección correcta del esquema de particionamiento puede marcar una diferencia significativa en el rendimiento de las consultas.

### 18.4. AWS Glue Studio

Glue Studio es una interfaz visual para la creación y gestión de trabajos ETL, simplificando el proceso para usuarios menos técnicos.

#### 18.4.1. Características principales
- **Interfaz visual**: Crea flujos de trabajo ETL sin necesidad de escribir código
- **Grafos dirigidos acíclicos (DAGs)**: Permite definir flujos de trabajo complejos con procesamiento en paralelo
- **Fuentes de datos compatibles**: S3, Kinesis, Kafka, y fuentes JDBC
- **Transformaciones visuales**: Permite transformar datos, crear muestras, unir tablas, etc.
- **Destinos**: Puede enviar datos procesados a S3 o al Glue Data Catalog
- **Soporte para particionamiento**: Aprovecha automáticamente los datos particionados

#### 18.4.2. Dashboard de monitoreo
- Permite visualizar el estado de trabajos ETL
- Muestra trabajos en ejecución, completados y fallados
- Proporciona métricas de rendimiento y uso de recursos
- Facilita la solución de problemas y la optimización

#### 18.4.3. Flujo de trabajo típico
1. Seleccionar fuente de datos (S3, RDS, Redshift, Kinesis, Kafka, etc.)
2. Configurar transformaciones mediante la interfaz visual
3. Especificar destino para los datos procesados
4. Programar o ejecutar el trabajo
5. Monitorear la ejecución a través del dashboard

### 18.5. AWS Glue Data Quality

Glue Data Quality es una característica que permite evaluar automáticamente la calidad de los datos durante el procesamiento ETL.

#### 18.5.1. Propósito y funcionalidad
- Evalúa la calidad de los datos según reglas predefinidas
- Puede integrarse como un paso en los trabajos de Glue
- Permite detectar problemas de calidad antes de que afecten análisis posteriores

#### 18.5.2. Creación de reglas
- **Creación manual**: Definir reglas específicas según necesidades
- **Creación automática**: Inferir reglas a partir de datos de referencia considerados "buenos"
- **DQDL (Data Quality Definition Language)**: Lenguaje específico para definir reglas de calidad

#### 18.5.3. Tipos de reglas comunes
- Validación de recuentos de filas
- Verificación de completitud de columnas
- Comprobación de rangos de valores
- Validación de desviaciones estándar

#### 18.5.4. Acciones ante violaciones
- **Fallo del trabajo**: Detener el proceso ETL si no se cumplen criterios de calidad
- **Notificación en CloudWatch**: Registrar problemas sin detener el proceso para revisión posterior

### 18.6. AWS Glue DataBrew

DataBrew es una herramienta visual específicamente diseñada para la preparación y transformación de datos sin necesidad de programación.

#### 18.6.1. Propósito y características
- **Interfaz visual**: Para preprocesamiento de grandes conjuntos de datos
- **Fuentes de datos**: S3, data warehouses o cualquier base de datos
- **Destino**: Los datos transformados se almacenan en S3
- **Transformaciones predefinidas**: Más de 250 transformaciones listas para usar

#### 18.6.2. Organización de trabajo
- **Proyectos**: Agrupan conjuntos de transformaciones relacionadas
- **Recetas**: Secuencias de transformaciones que pueden reutilizarse
- **Acciones de receta**: Transformaciones individuales que componen una receta

#### 18.6.3. Funcionalidades adicionales
- Definición de reglas de calidad de datos
- Creación de conjuntos de datos con SQL personalizado desde Redshift y Snowflake
- Integración con KMS, IAM, CloudWatch y CloudTrail

### 18.7. Manejo de Información Personal Identificable (PII) en DataBrew

DataBrew ofrece varias técnicas para manejar adecuadamente datos sensibles.

#### 18.7.1. Técnicas de gestión de PII
- **Sustitución**: Reemplazar PII con valores aleatorios
- **Shuffling (mezcla)**: Reorganizar valores de PII entre diferentes registros
- **Deterministic Encryption**: Cifrado donde un valor siempre produce el mismo resultado cifrado
- **Probabilistic Encryption**: Cifrado donde un valor puede producir diferentes resultados cifrados
- **Decrypt**: Proceso para revertir la encriptación
- **Delete**: Eliminación completa de la información
- **Masking (enmascaramiento)**: Ocultar parcialmente información (ej: mostrar solo últimos 4 dígitos)
- **Hashing**: Aplicar funciones hash para anonimizar datos

#### 18.7.2. Consideraciones para el examen
La transcripción menciona específicamente que el manejo de PII en DataBrew es un tema mencionado en la guía del examen, lo que sugiere su relevancia para la certificación.

---

## Resumen para el Examen

### AWS Glue - Conceptos Clave
- **Definición**: Servicio serverless de ETL que descubre y define esquemas automáticamente
- **Propósito principal**: Actuar como repositorio central de metadatos para lagos de datos
- **Componentes principales**: Crawler, Data Catalog y ETL Jobs
- **Tecnología subyacente**: Apache Spark (sin necesidad de gestionar el clúster)

### Componentes y Características

| Componente | Propósito | Características principales |
|------------|-----------|----------------------------|
| **Glue Crawler** | Inferir esquemas de datos | Escanea datos, identifica estructura, puede programarse |
| **Glue Data Catalog** | Almacenar metadatos | Repositorio central, integración con servicios SQL |
| **Glue ETL** | Transformar datos | Basado en Spark, serverless, activación flexible |
| **Glue Studio** | Crear visualmente flujos ETL | Interfaz gráfica, DAGs, monitoreo de trabajos |
| **Glue Data Quality** | Evaluar calidad de datos | Reglas automáticas o manuales, DQDL |
| **Glue DataBrew** | Preparar datos visualmente | +250 transformaciones, recetas, manejo de PII |

### Particionamiento en S3
- **Importancia**: Afecta directamente la eficiencia de consultas
- **Estrategias**: 
  - Por tiempo (`/año/mes/día/dispositivo/`) - para consultas temporales
  - Por entidad (`/dispositivo/año/mes/día/`) - para consultas por entidad

### Gestión de PII en DataBrew
- **Técnicas**: Sustitución, Shuffling, Encriptación, Eliminación, Enmascaramiento, Hashing
- **Consideración**: Mencionado específicamente en la guía del examen

**Consejo para el examen**: Según la transcripción, AWS Glue es un tema importante en el examen actual. Asegúrate de comprender bien cómo Glue conecta diferentes servicios y cómo sus componentes (Crawler, Data Catalog, ETL) trabajan juntos. Presta especial atención al particionamiento en S3 y cómo afecta al rendimiento de las consultas, así como a las capacidades de Glue Studio, Data Quality y DataBrew.


## 19. Amazon Athena

### 19.1. Introducción a Amazon Athena

Amazon Athena es un servicio de consultas interactivas que permite analizar datos almacenados en Amazon S3 utilizando SQL estándar, sin necesidad de mover los datos a un sistema de bases de datos tradicional.

#### 19.1.1. Definición y características fundamentales
- **Servicio serverless**: No requiere infraestructura para gestionar o escalar
- **Motor de consultas interactivo**: Permite ejecutar consultas SQL ad-hoc sobre datos en S3
- **Sin carga de datos**: Opera directamente sobre los datos en su ubicación original en S3
- **Tecnología subyacente**: Basado originalmente en Presto, aunque ha evolucionado considerablemente

#### 19.1.2. Formatos de datos compatibles
Athena funciona con múltiples formatos de datos, cada uno con características particulares:

| Formato | Tipo | Características | Optimizado para Athena |
|---------|------|-----------------|------------------------|
| **CSV, TSV** | Human-readable | Legibles por humanos, no splittable | No |
| **JSON** | Human-readable | Legibles por humanos, no splittable | No |
| **Avro** | Splittable | Se puede procesar en paralelo | Parcial |
| **ORC** | Columnar, Splittable | Almacenamiento por columnas, procesamiento paralelo | Sí |
| **Parquet** | Columnar, Splittable | Almacenamiento por columnas, procesamiento paralelo | Sí |

Athena también soporta diversos formatos de compresión:
- Snappy
- Zlib
- AE (Amazon Encryption)
- GZIP

**Punto clave para el examen**: Los formatos columnares (ORC, Parquet) ofrecen mejor rendimiento y menores costos por permitir lecturas selectivas de columnas.

#### 19.1.3. Tipos de datos compatibles
- **Datos no estructurados**: Archivos de texto sin formato definido
- **Datos semi-estructurados**: Como archivos JSON con cierta organización
- **Datos estructurados**: Con esquema bien definido

### 19.2. Casos de Uso Comunes

Athena es especialmente útil para varios escenarios habituales en el análisis de datos:

#### 19.2.1. Análisis de logs
- Consultas ad-hoc sobre logs web almacenados en S3
- Análisis de logs de servicios AWS (CloudTrail, CloudFront, VPC, ELB)
- Alternativa preferida por AWS frente a Elasticsearch para este propósito

#### 19.2.2. Exploración de datos previo a ETL
- Examen de datos antes de cargarlos en Redshift
- Verificación y validación de datos en staging

#### 19.2.3. Integración con herramientas de análisis
- Compatible con notebooks (Jupyter, Zeppelin, RStudio)
- Interfaces ODBC y JDBC para integraciones diversas
- Visualización con Amazon QuickSight

### 19.3. Integración con AWS Glue

La combinación de AWS Glue y Amazon Athena representa un poderoso tándem para trabajar con datos en S3.

#### 19.3.1. Flujo de trabajo con Glue
1. **Glue Crawler** escanea datos en S3 y descubre su esquema
2. **Glue Data Catalog** almacena los metadatos y definiciones de tablas
3. **Athena** automáticamente reconoce las tablas del Data Catalog
4. **Consultas SQL** se ejecutan directamente sobre los datos en S3

#### 19.3.2. Beneficios de la integración
- **Repositorio unificado de metadatos**: Permite consistencia entre diferentes servicios
- **Mantenimiento automático de esquemas**: Actualización de definiciones ante cambios
- **Versionado de esquemas**: Seguimiento de evolución de estructuras de datos
- **Amplia compatibilidad**: Además de Athena, el catálogo es utilizable por RDS, Redshift, EMR y cualquier aplicación compatible con Apache Hive Metastore

### 19.4. Workgroups (Grupos de Trabajo)

Los workgroups en Athena permiten organizar usuarios, equipos y cargas de trabajo para un mejor control y gestión.

#### 19.4.1. Propósito y funcionalidades
- **Organización**: Agrupar usuarios, equipos o aplicaciones
- **Control de acceso**: Gestionar permisos por grupo
- **Seguimiento de costos**: Monitorear gastos por equipo o proyecto
- **Límites de consulta**: Establecer restricciones sobre volumen de datos escaneados

#### 19.4.2. Características de gestión
- **Integración con IAM**: Políticas de permisos específicas por workgroup
- **Monitoreo con CloudWatch**: Métricas de uso y rendimiento
- **Notificaciones con SNS**: Alertas cuando se alcanzan límites
- **Historial de consultas separado**: Cada workgroup mantiene su propio historial
- **Configuraciones de cifrado independientes**: Diferentes niveles de seguridad por grupo

### 19.5. Modelo de Costos

Athena utiliza un modelo de precios simple basado en la cantidad de datos escaneados durante las consultas.

#### 19.5.1. Estructura de precios
- **$5 por TB de datos escaneados** (precio actual, sujeto a cambios)
- Se cobra por consultas **exitosas o canceladas**
- No se cobra por consultas fallidas
- Operaciones DDL (CREATE, ALTER, DROP) son gratuitas

#### 19.5.2. Optimización de costos
- **Uso de formatos columnares** (ORC, Parquet): Puede reducir costos entre 30-90%
- **Particionamiento de datos**: Limitar el escaneo a particiones relevantes
- **Compresión de datos**: Reducir el volumen total a escanear

**Nota importante**: Además de los costos de Athena, se aplican cargos separados por almacenamiento en S3 y uso de AWS Glue.

### 19.6. CTAS (Create Table As Select)

La instrucción CTAS es una funcionalidad poderosa de Athena que permite crear nuevas tablas a partir de resultados de consultas.

#### 19.6.1. Sintaxis y funcionamiento
```sql
CREATE TABLE new_table
WITH (
  format = 'PARQUET',
  compression = 'SNAPPY'
) 
AS SELECT * FROM old_table;
```

#### 19.6.2. Casos de uso principales
- **Conversión de formato**: Transformar datos de CSV/JSON a formatos más eficientes (ORC/Parquet)
- **Creación de subconjuntos**: Extraer porciones específicas de datos para análisis
- **Aplicación de transformaciones**: Modificar datos durante la creación de la nueva tabla
- **Redireccionamiento de ubicación**: Almacenar resultados en ubicaciones específicas de S3

#### 19.6.3. Ejemplo práctico
```sql
CREATE TABLE my_orc_task_table
WITH (
  format = 'ORC',
  external_location = 's3://my-athena-results/my-orc-data/'
) 
AS SELECT * FROM old_table;
```
Este ejemplo crea una nueva tabla en formato ORC en una ubicación específica de S3.

### 19.7. Optimización de Rendimiento

Existen varias estrategias para mejorar el rendimiento de las consultas en Athena:

#### 19.7.1. Uso de formatos columnares
- ORC y Parquet ofrecen mejor rendimiento que CSV, JSON o texto plano
- Permiten lecturas selectivas de columnas relevantes
- Reducen el volumen de datos a escanear

#### 19.7.2. Estructura óptima de archivos
- **Preferir pocos archivos grandes** sobre muchos archivos pequeños
- El tamaño ideal de archivo suele estar entre 128MB y 1GB
- Evitar fragmentación excesiva de datos

#### 19.7.3. Particionamiento efectivo
- Organizar datos según patrones de consulta más comunes
- Alinear particiones con predicados de filtrado frecuentes
- Usar `MSCK REPAIR TABLE` para añadir particiones a tablas existentes

### 19.8. Soporte para Transacciones ACID

Una característica reciente de Athena es el soporte para transacciones ACID (Atomicidad, Consistencia, Aislamiento, Durabilidad).

#### 19.8.1. Implementación y habilitación
- Basado en Apache Iceberg
- Se activa con la cláusula `TABLE_TYPE = 'ICEBERG'` al crear tablas
```sql
CREATE TABLE my_table (
  id int,
  data string
)
WITH (
  TABLE_TYPE = 'ICEBERG'
);
```

#### 19.8.2. Ventajas principales
- **Concurrencia segura**: Múltiples usuarios pueden modificar los mismos datos simultáneamente
- **Modificaciones a nivel de fila**: Soporte para operaciones INSERT, UPDATE, DELETE
- **Time travel**: Capacidad para consultar versiones anteriores de los datos
- **Elimina necesidad de bloqueos personalizados**: Gestión automática de concurrencia

#### 19.8.3. Mantenimiento y optimización
- Requiere compactación periódica para mantener rendimiento
- Usar comando de optimización para compactación:
```sql
OPTIMIZE table_name REWRITE DATA USING BIN_PACK;
```

#### 19.8.4. Relación con Lake Formation
- Alternativa a las tablas gobernadas (governed tables) de Lake Formation
- Ambas ofrecen soporte ACID pero con diferentes implementaciones y administración

### 19.9. Seguridad en Athena

Athena proporciona múltiples capas de seguridad para proteger tanto los datos como las consultas.

#### 19.9.1. Control de acceso
- **IAM**: Políticas de permisos granulares
- **ACLs de S3**: Control de acceso a nivel de bucket y objeto
- **Políticas de bucket S3**: Permisos para acciones específicas

#### 19.9.2. Cifrado de resultados
Athena permite cifrar los resultados de consultas en S3 mediante varios métodos:
- **SSE-S3**: Cifrado del lado del servidor con claves administradas por S3
- **SSE-KMS**: Cifrado del lado del servidor con claves administradas por KMS
- **CSE-KMS**: Cifrado del lado del cliente con claves administradas por KMS

#### 19.9.3. Acceso entre cuentas
- Posibilidad de acceder a buckets S3 en otras cuentas AWS
- Requiere configuración de políticas de bucket apropiadas

#### 19.9.4. Seguridad en tránsito
- TLS para toda comunicación entre Athena y S3
- Cifrado de comunicación entre componentes del servicio

### 19.10. Control de Acceso Detallado al Catálogo de Glue

Athena permite establecer permisos granulares sobre operaciones en bases de datos y tablas del Catálogo de Glue.

#### 19.10.1. Alcance y limitaciones
- Control a nivel de operación sobre bases de datos y tablas
- No es seguridad a nivel de fila, columna o celda (como en Lake Formation)
- No permite restricciones a versiones específicas de tablas

#### 19.10.2. Configuración de permisos
- Requiere políticas IAM específicas para Athena y Glue
- Permite restringir operaciones como:
  - ALTER o CREATE DATABASE
  - CREATE TABLE
  - DROP DATABASE o DROP TABLE
  - MSCK REPAIR TABLE
  - SHOW DATABASES o SHOW TABLES

#### 19.10.3. Ejemplo de política
```json
{
  "Effect": "Allow",
  "Action": [
    "glue:GetPartition*",
    "glue:GetTable",
    "glue:GetDatabase",
    "glue:DeleteTable",
    "glue:DeletePartition*"
  ],
  "Resource": [
    "arn:aws:glue:region:account:catalog",
    "arn:aws:glue:region:account:database/database_name",
    "arn:aws:glue:region:account:table/database_name/table_name"
  ]
}
```
Este ejemplo permite la operación DROP TABLE sobre una tabla específica.

### 19.11. Antipatrones

Existen casos donde Athena no es la mejor opción, según las propias recomendaciones de AWS:

#### 19.11.1. Reportes altamente formateados
- Athena es un motor de consultas, no una herramienta de reportería
- Para visualizaciones sofisticadas, mejor usar QuickSight

#### 19.11.2. Operaciones ETL complejas
- Aunque puede realizar algunas transformaciones, no está optimizado para ETL extenso
- Mejores alternativas: AWS Glue ETL, Apache Spark en EMR

---

## Resumen para el Examen

### Amazon Athena - Conceptos Clave
- **Definición**: Servicio serverless de consultas SQL interactivas sobre datos almacenados en S3
- **Características**: No requiere carga de datos, opera directamente sobre S3, integración con Glue
- **Modelo de costos**: $5 por TB escaneado, solo se cobra por consultas exitosas o canceladas

### Optimización de Rendimiento y Costos

| Estrategia | Beneficio | Implementación |
|------------|-----------|----------------|
| **Formatos columnares** | 30-90% reducción en costos, mejor rendimiento | Convertir datos a ORC/Parquet (usar CTAS) |
| **Particionamiento** | Reduce datos escaneados | Organizar datos según patrones de consulta comunes |
| **Archivos grandes** | Mejor rendimiento | Preferir pocos archivos grandes vs. muchos pequeños |
| **Compresión** | Reduce volumen de datos | Usar Snappy, Zlib, etc. con formatos compatibles |

### Características Avanzadas
- **CTAS**: Permite transformar datos y cambiar formatos mediante SQL
- **Workgroups**: Control, organización y seguimiento de usuarios/consultas
- **Transacciones ACID**: Soporte mediante Apache Iceberg (`TABLE_TYPE = 'ICEBERG'`)
- **Control de acceso detallado**: Permisos granulares sobre operaciones en catálogo de Glue

**Consejo para el examen**: Recuerda que los formatos columnares (ORC, Parquet) son cruciales para optimizar tanto el rendimiento como los costos en Athena. Comprende la diferencia entre los diversos formatos (human-readable vs. columnar vs. splittable) y cómo el particionamiento adecuado de datos en S3 influye directamente en la eficiencia de las consultas. Familiarízate con CTAS como herramienta para convertir datos a formatos más eficientes, y con los métodos de cifrado disponibles (SSE-S3, SSE-KMS, CSE-KMS).