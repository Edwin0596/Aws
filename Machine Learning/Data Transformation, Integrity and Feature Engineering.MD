# Data Transformation, Integrity and Feature Engineering

## 1. Elastic MapReduce (EMR) y Hadoop Overview

### 1.1 Introducción a EMR

EMR (Elastic MapReduce) es un framework de Hadoop gestionado que se ejecuta en instancias EC2. El nombre puede resultar confuso porque MapReduce en sí es una parte algo obsoleta de Hadoop, y EMR es mucho más que simplemente un cluster de Hadoop.

EMR incluye Hadoop y MapReduce, pero son las tecnologías construidas sobre Hadoop las que se utilizan con mayor frecuencia, como:
- Spark
- HBase
- Presto
- Flink
- Hive
- Y muchas otras herramientas preinstaladas

También existe algo llamado EMR notebooks, similar a un Jupyter notebook que se ejecuta en tu cluster EMR, y tiene varios puntos de integración con servicios AWS.

### 1.2 EMR en Machine Learning

EMR es relevante para el mundo del machine learning cuando tienes un conjunto de datos masivo que necesitas preparar, normalizar, escalar o tratar de otra manera antes de introducirlo en tus algoritmos.

EMR proporciona una forma de distribuir la carga de procesamiento de datos a través de un cluster completo de computadoras. Para conjuntos de datos masivos, a menudo necesitas un cluster para procesar esos datos y prepararlos para tus trabajos de entrenamiento de machine learning en paralelo.

### 1.3 Estructura de un Cluster EMR

Un cluster en EMR es una colección de instancias EC2 donde cada instancia EC2 se denomina node (nodo). Cada nodo tiene un rol dentro del cluster, llamado node type (tipo de nodo):

#### 1.3.1 Master Node (Nodo Maestro)
- Administra el cluster ejecutando componentes de software para coordinar la distribución de datos y tareas entre los otros nodos para el procesamiento
- Realiza seguimiento del estado de las tareas y monitorea la salud del cluster
- Todo cluster tiene un nodo maestro
- Puedes crear un cluster de un solo nodo (solo master)
- También se conoce como "leader node"

#### 1.3.2 Core Nodes (Nodos Core)
- Nodos con componentes de software que ejecutan tareas y almacenan datos en el Hadoop Distributed File System (HDFS)
- Los clusters multi-nodo tienen al menos un core node

#### 1.3.3 Task Nodes (Nodos de Tarea)
- Nodos con componentes de software que solo ejecutan tareas y no almacenan datos en HDFS
- Son opcionales
- Son un buen uso de instancias spot porque se pueden introducir y sacar del cluster según sea necesario
- No impactan el almacenamiento del cluster ya que no se comunican con HDFS
- No hay archivos permanentes almacenados en los task nodes que el cluster necesite, solo se utilizan para cómputo

### 1.4 Tipos de Clusters EMR

Hay un par de formas de utilizar un cluster EMR:

#### 1.4.1 Cluster Transitorio (Transient)
- Configurado para terminar automáticamente una vez completados todos los pasos definidos
- Los pasos pueden incluir: cargar datos de entrada, procesar datos, almacenar resultados de salida y luego apagar el cluster
- Puedes ahorrar dinero terminando automáticamente el cluster tan pronto como se complete la tarea

#### 1.4.2 Cluster de Larga Duración (Long Running)
- Crear un cluster para interactuar directamente con las aplicaciones
- Terminar manualmente cuando ya no lo necesites
- Más apropiado para consultas ad hoc o experimentación con conjuntos de datos donde no sabes exactamente qué quieres hacer de antemano

Cuando lanzas un cluster, seleccionas los frameworks y aplicaciones a instalar para tus necesidades de procesamiento de datos. Una vez que tienes un cluster funcionando, puedes:
- Conectarte directamente al nodo maestro a través de EC2 y ejecutar trabajos desde la terminal
- Enviar pasos ordenados a través de la consola AWS

### 1.5 Integración de EMR con Servicios AWS

EMR se integra con varios servicios de AWS:
- **EC2**: Para las instancias subyacentes que componen los nodos del cluster
- **Amazon VPC**: Para tener tu cluster dentro de una red virtual
- **Amazon S3**: Almacenamiento de datos de entrada y salida como alternativa a HDFS
- **Amazon CloudWatch**: Monitoreo del rendimiento del cluster y configuración de alarmas
- **IAM**: Configuración de permisos para el cluster
- **CloudTrail**: Creación de registros de auditoría para las solicitudes realizadas
- **AWS Data Pipeline**: Programación e inicio de clusters con pasos predefinidos

### 1.6 Almacenamiento en EMR

#### 1.6.1 HDFS (Hadoop Distributed File System)
- Solución de almacenamiento predeterminada en Hadoop
- Sistema de archivos distribuido y escalable para Hadoop
- Distribuye los datos almacenados en todas las instancias del cluster
- Almacena múltiples copias de datos en diferentes instancias para evitar pérdidas si una instancia falla
- Cada archivo en HDFS se almacena como bloques y se distribuye a través del cluster Hadoop
- Por defecto, el tamaño de un bloque en HDFS es 128MB
- Es almacenamiento efímero: cuando terminas el cluster, ese almacenamiento se pierde
- Es más rápido porque no hay que ir a través de internet para acceder a los datos
- Hadoop tiene inteligencia para optimizar el procesamiento, intentando que el código que procesa un dato se ejecute en el mismo nodo donde ese dato está almacenado

#### 1.6.2 EMR FS
- Permite usar S3 como si fuera un sistema de archivos HDFS
- Sigue siendo bastante rápido
- Tiene una "vista consistente" opcional para la consistencia de S3
- Puede usar DynamoDB para rastrear esa consistencia

#### 1.6.3 Otras opciones
- Sistema de archivos local para elementos efímeros (no distribuido, útil solo en el nodo maestro)
- HDFS respaldado por Elastic Block Store (EBS)

### 1.7 Aspectos operativos de EMR

- EMR cobra por hora más los cargos de EC2 subyacentes
- Aprovisiona automáticamente nuevos nodos si un core node falla
- Permite añadir y eliminar task nodes sobre la marcha
- Permite usar instancias spot para añadir o eliminar capacidad sin impactar el almacenamiento subyacente
- Permite redimensionar los core nodes en un cluster en ejecución

## 2. EMR Serverless

### 2.1 Introducción a EMR Serverless

EMR Serverless funciona con la misma idea que EMR. La diferencia es que se elimina el paso intermedio de definir cuánta capacidad necesitamos, permitiendo que EMR decida por sí mismo automáticamente cuántos nodos necesita en el cluster para hacer lo que queremos.

Para usar EMR Serverless, comenzamos eligiendo nuestro EMR release y runtime (Spark, Hive o Presto), igual que haríamos normalmente. Luego, simplemente enviamos consultas y scripts a nuestra instancia EMR Serverless mediante job run requests.

En lugar de iniciar sesión directamente en tu nodo maestro, al ser serverless, interactúas con él indirectamente. Le proporcionas una ruta a algún archivo en S3 que indica dónde está tu script. Este script hará referencia a datos de S3, y tú simplemente envías ese script o consulta de Hive desde S3 a tu servidor serverless, y automáticamente, en teoría, calculará cuánta capacidad necesitas para ejecutar ese trabajo exitosamente.

### 2.2 Administración de capacidad

EMR administrará la capacidad subyacente por ti. Pero si quieres pensar en la capacidad subyacente, todavía puedes hacerlo, lo cual puede ser una buena idea en muchas situaciones:

- Si sabes que tu trabajo requiere pocos recursos, puedes especificarlo de antemano
- Puedes especificar los tamaños de worker predeterminados de antemano
- También puedes pre-inicializar tu capacidad

En lugar de comenzar desde cero, puedes decirle a EMR Serverless cuál crees que será la capacidad base que necesitarás y comenzar desde ahí. Esto te ayudará; de lo contrario, intentará calcularlo automáticamente para ti.

Incluso si comienzas con una configuración base de capacidad pre-inicializada, analizará los recursos que realmente necesita tu trabajo y ajustará la capacidad en consecuencia, programando workers según los necesites y reprogramándolos cuando no los necesites.

Actualmente, todo funciona dentro de la misma región, a través de múltiples zonas de disponibilidad, por lo que tu cluster serverless estará dentro de una región.

### 2.3 Ventajas de EMR Serverless

¿Por qué es esto tan importante? Ya no tienes que averiguar cuántos workers necesitas. Un punto realmente problemático en tecnologías como Apache Spark es que si calculas muy poco, si no tienes suficientes recursos para un trabajo, terminarás quedándote sin memoria. Tu trabajo simplemente fallará a la mitad, y puede ser un proceso muy largo y costoso determinar cuánta capacidad realmente necesitas, porque no hay una buena manera de averiguarlo de antemano.

EMR Serverless resuelve ese problema, lo cual es bastante impresionante. Aprovisionará automáticamente la capacidad que necesitas para tu trabajo, así que no tienes que preocuparte por ello, lo que es realmente importante. Puedes concentrarte en los scripts y en lo que estás tratando de hacer, en lugar de averiguar cuánta capacidad necesitas.

Y como mencioné, no es realmente serverless como otros servicios serverless donde realmente no piensas en los servidores en absoluto. Bajo el capó, sigues usando Spark, Hive o Presto, y esas tecnologías asumen que tienes algún conocimiento de lo que está sucediendo a nivel de nodo worker o nivel de driver. Así que si aún quieres configurar Spark a ese nivel, todavía tendrás que pensar en los nodos worker y sus capacidades. La diferencia es que no necesariamente sabes cuántos tienes, y no necesariamente sabes dónde están.

### 2.4 Uso de EMR Serverless

Para usar EMR Serverless:

1. Comienzas con un usuario IAM desde el cual interactuarás, que funcionará con AWS CLI
2. Tu usuario invocará el CLI para crear este cluster serverless
3. Actualmente solo se admite CLI (probablemente cambiarán esto pronto para incluir soporte de consola y SDK)
4. Necesitas configurar un rol de ejecución de trabajo en IAM para el trabajo en sí:
   - Asegura que tu trabajo tenga permiso para acceder a AWS EMR Serverless
   - Proporciona permisos para acceder a tus scripts en S3
   - Proporciona permisos para cualquier dato que necesite acceder en S3
   - Si estás usando Spark SQL, puede necesitar acceso a Glue para metadatos
   - Acceso a cualquier clave KMS que puedas necesitar para seguridad

Con eso en mano, puedes crear una aplicación EMR Serverless usando Spark, Hive o lo que quieras, y luego simplemente enviarle tu trabajo a través de una solicitud de trabajo EMR.

Ejemplo de un comando para invocar un trabajo EMR Serverless:
```
aws emr-serverless start-job-run \
  --application-id [ID de aplicación] \
  --execution-role-arn [path al rol] \
  --job-driver '{
    "sparkSubmit": {
      "entryPoint": "[path al script]",
      "entryPointArguments": ["[argumentos]"],
      "sparkSubmitParameters": "--conf spark.executor.cores=1"
    }
  }'
```

Aunque lo llamemos serverless, aún estamos pensando en el tamaño de nuestros cores de ejecutor, cuántos cores tiene el driver, etc. Todavía tienes ese nivel de control, incluso si estás dejando la capacidad real y la cantidad de nodos a EMR Serverless.

También puedes enviar anulaciones de configuración específicas para EMR Serverless. Por ejemplo, puedes especificar que quieres enviar tus logs de CloudWatch a una ruta específica en S3.

Cuando termines, obtienes tu salida y tus logs donde dijiste que los pusieras, y eso es todo. Simplemente lo apagas cuando terminas, al igual que con EMR.

### 2.5 Ciclo de vida de una aplicación EMR Serverless

El ciclo de vida de una aplicación EMR Serverless es el siguiente:

1. **Creación**: Creas explícitamente tu aplicación a través de un comando (actualmente desde CLI)
   - Si tiene éxito, has creado tu aplicación
   - Si no, terminará automáticamente

2. Una vez creada, tienes opciones:
   - Terminarla inmediatamente (aunque no está claro por qué lo harías)
   - Emitir un comando de inicio para iniciar tu aplicación

3. Una vez iniciada, puede:
   - Tener éxito y luego pasar a la fase de detención cuando termines
   - Fallar y pasar automáticamente a la fase de detención

4. Una vez detenida exitosamente, puedes:
   - Ejecutarla nuevamente (volver a iniciar)
   - Pasar a la fase de terminación y apagarla cuando hayas terminado

Es importante destacar que esto no sucede automáticamente. Necesitas llamar comandos a través de la API como create-application, start-application, stop-application y, cuando termines, delete-application para que todo esto suceda.

No deberías usar EMR Serverless solo para evitar tener que recordar apagar tu cluster. Aunque te ayuda a no usar más capacidad de la que necesitas, aún debes eliminar explícitamente tu aplicación cuando termines. Si olvidas hacerlo, seguirás siendo facturado por esa aplicación serverless, incluso si no está haciendo nada.

No pienses que EMR Serverless es una excusa para descuidar la gestión de tus recursos. Todavía necesitas pensar en iniciar, detener y terminar explícitamente ese cluster cuando hayas terminado. EMR Serverless solo significa que está pensando en la capacidad en lugar de ti, pero aún necesitas administrar esa capacidad hasta cierto punto.

### 2.6 Capacidad Pre-inicializada en EMR Serverless

Ejemplo de una configuración de capacidad pre-inicializada desde la línea de comando:

```
--initial-capacity '{
  "Driver": {
    "count": 5,
    "workerConfiguration": {
      "cpu": "2vCPU",
      "memory": "4GB"
    }
  },
  "Executor": {
    "count": 50,
    "workerConfiguration": {
      "cpu": "4vCPU",
      "memory": "8GB"
    }
  }
}'
--maximum-capacity '{
  "cpu": "400vCPU",
  "memory": "1TB"
}'
```

Esto te permite decir cuántos procesos quieres para alimentar tu aplicación. Sigue siendo trabajo de EMR Serverless averiguar dónde se ejecutan esos procesos y la capacidad necesaria para hacerlo, pero puedes darle una pista para decir: "Sé que este es un trabajo bastante exigente, quiero comenzar con 50 ejecutores, averigua cómo hacer que eso suceda".

También puedes establecer una capacidad máxima como salvaguarda en caso de que hagas algo que quiera usar recursos infinitos, lo que te proporcionará una pequeña red de seguridad.

**Nota importante de la documentación**: Spark agrega un 10% de overhead a cualquier memoria solicitada para drivers y executors. Asegúrate de que tu solicitud de capacidad inicial tenga esto en cuenta. Asegúrate de solicitar al menos un 10% más de memoria de la que tu trabajo está realmente solicitando.

### 2.7 Seguridad en EMR Serverless

La seguridad con EMR Serverless es muy similar a la de EMR para almacenamiento:

- **Para almacenamiento**: 
  - Si estás usando EMR EFS (que es S3), tienes encriptación del lado del servidor o del cliente en S3 en reposo
  - Para datos en tránsito, TLS estará disponible entre los nodos EMR y S3
  - Si escribes directamente en S3, toda la seguridad que viene con S3 está disponible (S3, SSD, KMS)
  - Cualquier almacenamiento efímero en los discos locales de tus nodos también está encriptado

- **Para comunicación**:
  - La comunicación entre los nodos driver y los executors está encriptada automáticamente
  - Si estás usando Hive, la comunicación entre el metastore de Glue y EMR también está encriptada en tránsito usando TLS

**Nota**: Si estás usando S3, puedes forzar HTTPS en el tráfico en tránsito a S3 usando la política AWS Secure Transport.

### 2.8 EMR en EKS (Elastic Kubernetes Service)

Otro enfoque serverless para EMR es EMR en EKS (Elastic Kubernetes Service). Si tu mundo está construido alrededor de Kubernetes, esta es una forma de usar EMR y Spark en particular dentro de Amazon EKS.

Esto te permite enviar un trabajo de Spark en Elastic Kubernetes Service sin tener que preocuparte por aprovisionar los clusters para ello. Te da todos los beneficios de Kubernetes en lugar de tener solo un cluster EMR independiente, y también te da los beneficios de todos los aspectos completamente gestionados de EMR.

No tienes que lidiar con la configuración de tu propia aplicación Spark dentro de EKS. Esto automatiza todo por ti, proporcionando una solución completamente gestionada para Elastic MapReduce y Spark en particular sobre tu mundo ECS.

La ventaja de hacer esto es que puedes compartir recursos entre Spark y otras aplicaciones que puedas tener ejecutándose en Kubernetes. Puedes tener aplicaciones de Spark como solo una de las muchas cosas que estás haciendo en ese cluster EKS. Puedes tener otras aplicaciones haciendo cosas totalmente diferentes, tal vez una aplicación de análisis, incluso puedes tener diferentes versiones de Spark ejecutándose junto con todo lo demás.

A través de la magia de Kubernetes, podemos compartir esos recursos de manera más eficiente, lo que puede ser una mejor manera de aprovechar el hardware que tienes y, en última instancia, ahorrar dinero.

También tiene todas estas integraciones con otros servicios de Amazon, distribuidos potencialmente entre múltiples zonas de disponibilidad. De nuevo, no tienes que preocuparte mucho por eso.

Con solo unos pocos clics en la consola, puedes elegir la versión de Apache Spark que deseas, implementar una carga de trabajo EMR en Amazon EKS. EMR empaquetará automáticamente esa carga de trabajo en un contenedor y proporcionará conectores preconstruidos para integrarse con estos otros servicios de AWS. EMR luego implementará el contenedor en el cluster EKS y administrará todo el escalado, registro y monitoreo de esa carga de trabajo por ti.

## 3. Apache Hadoop y Spark en EMR

### 3.1 Hadoop: Componentes principales

Hadoop consiste en varios módulos diferentes, y muchas de las herramientas en tu cluster EMR se basan en estos módulos. Cuando hablamos de Hadoop, generalmente nos referimos a:

- El sistema de archivos HDFS
- Yarn
- MapReduce

Subyacente a todos estos está algo llamado Hadoop Core o Hadoop Common, que son las bibliotecas y utilidades necesarias para que todos estos módulos funcionen. Proporciona el sistema de archivos y funcionalidades a nivel del sistema operativo que necesita para abstraerse del SO, así como todos los archivos Java y scripts necesarios para iniciar Hadoop.

#### 3.1.1 HDFS (Hadoop Distributed File System)
Es un sistema de archivos distribuido y escalable para Hadoop. Distribuye los datos que almacena a través de las instancias del cluster. Se almacenan múltiples copias de los datos en diferentes instancias para garantizar que no se pierdan datos si falla una instancia individual. Sin embargo, estos datos se pierden cuando se termina el cluster, pero es útil para almacenar en caché resultados intermedios durante el procesamiento MapReduce o para cargas de trabajo con E/S aleatoria significativa.

#### 3.1.2 Hadoop YARN
YARN significa "Yet Another Resource Negotiator". Es un componente introducido en Hadoop 2.0 para gestionar centralmente los recursos del cluster desde múltiples frameworks de procesamiento de datos. Esto nos permite utilizar otras herramientas además de MapReduce.

#### 3.1.3 MapReduce
MapReduce es un framework de software para escribir fácilmente aplicaciones que procesan grandes cantidades de datos en paralelo en clusters grandes de hardware commodity de manera confiable y tolerante a fallos. Se remonta a algunas ideas de Google en sus primeros días de procesamiento de big data.

MapReduce consta de funciones mapper y funciones reduce que escribes en código:
- Una función map mapea datos a conjuntos de pares clave-valor, llamados resultados intermedios. Las funciones map generalmente transforman tus datos, los reformatean o extraen los datos que necesitas.
- Las funciones reduce combinan esos resultados intermedios, aplican algoritmos adicionales y producen la salida final.

Generalmente, los mappers transforman y preparan tus datos, mientras que los reducers agregan esos datos y los destilan a una respuesta final.

### 3.2 Apache Spark en EMR

En la actualidad, Apache Spark ha reemplazado en gran medida a MapReduce. Gracias a la existencia de YARN, Spark puede ejecutarse sobre tu cluster Hadoop y utilizar el negociador de recursos subyacente y el sistema de archivos que ofrece Hadoop, pero proporcionando una alternativa más rápida a MapReduce.

Spark, que puede instalarse opcionalmente en tu cluster EMR, es un sistema de procesamiento distribuido de código abierto comúnmente utilizado para cargas de trabajo de big data. Utiliza caché en memoria y ejecución de consultas optimizada para consultas analíticas rápidas contra datos de cualquier tamaño.

Spark utiliza algo llamado Directed Acyclic Graph (DAG), que es su principal truco para lograr velocidad. En comparación con MapReduce, puede ser más inteligente sobre las dependencias y el procesamiento, y cómo programarlos de manera más efectiva.

Spark tiene APIs para Java, Scala, Python y R, y admite la reutilización de código en múltiples cargas de trabajo como:
- Procesamiento por lotes
- Consultas interactivas
- Análisis en tiempo real
- Machine learning
- Procesamiento de grafos

### 3.3 Funcionamiento interno de Spark

Las aplicaciones Spark se ejecutan como un conjunto independiente de procesos en un cluster, coordinados por el objeto SparkContext del programa principal. Esto se conoce como el programa driver, que es el código que escribes para hacer que tu trabajo Spark se ejecute.

El SparkContext se conecta a diferentes cluster managers que asignan recursos a través de las aplicaciones (por ejemplo, YARN, o Spark tiene su propio cluster manager incorporado que puedes usar si no estás en un cluster Hadoop).

Al conectarse, Spark adquirirá executors en nodos del cluster. Los executors son procesos que ejecutan cálculos y almacenan datos para tus aplicaciones. El código de la aplicación se envía a los executors y, en el paso final, el SparkContext enviará tareas a los executors para ejecutarlas.

### 3.4 Componentes de Spark

Spark tiene varios componentes diferentes, al igual que Hadoop:

#### 3.4.1 Spark Core
Es la base de la plataforma. Es responsable de:
- Gestión de memoria
- Recuperación ante fallos
- Programación, distribución y monitoreo de trabajos
- Interacción con sistemas de almacenamiento

Tiene APIs para Java, Scala, Python y R, y en el nivel más bajo utiliza algo llamado Resilient Distributed Dataset (RDD), que representa una colección lógica de datos particionados entre diferentes nodos de cómputo.

#### 3.4.2 Spark SQL
Es un motor de consulta distribuido que proporciona consultas interactivas de baja latencia, hasta 100 veces más rápidas que MapReduce. Incluye:
- Optimizador basado en costos
- Almacenamiento columnar
- Generación de código para consultas rápidas

Soporta varias fuentes de datos como JDBC, ODBC, JSON, HDFS, Hive, archivos ORC o Parquet, y admite la consulta de tablas Hive usando HiveQL.

Lo más importante de Spark SQL es que expone algo llamado DataFrame en Python o Dataset en Scala, que está reemplazando los RDDs de nivel inferior en Spark. El código Spark moderno tiende a interactuar con datos de manera similar a como lo harías con un DataFrame en pandas o una tabla de base de datos relacional.

#### 3.4.3 Spark Streaming
Es una solución en tiempo real que aprovecha las capacidades de programación rápida de Spark Core para realizar análisis de streaming. Los datos se ingieren en mini-lotes y se analizan dentro de la misma aplicación. El código escrito para análisis por lotes se puede aplicar a estos mini-lotes.

Spark Streaming admite ingesta desde Twitter, Kafka, Flume, HDFS y ZeroMQ, y también puede integrarse con AWS Kinesis.

#### 3.4.4 MLlib (Machine Learning Library)
Es la biblioteca de machine learning para Spark, relevante para el examen de machine learning. Ofrece varios algoritmos de machine learning implementados de manera distribuida y escalable.

No todos los algoritmos de machine learning se prestan bien al procesamiento paralelo; muchos necesitan ser reimaginados para distribuir la carga en un cluster de computadoras. MLlib ofrece:

- Para tareas de clasificación: regresión logística y Naive Bayes
- Regresión a través de un cluster
- Árboles de decisión
- Motor de recomendación basado en Alternating Least Squares
- Implementación de clustering K-means
- LDA para modelado de tópicos
- Utilidades de workflow para machine learning (pipelines, transformación de características, persistencia)
- Implementaciones distribuidas de SVD, PCA y funciones estadísticas

La característica especial es que puede ejecutarse en un cluster, permitiendo procesar conjuntos de datos masivos y entrenar modelos de machine learning a través de un cluster completo.

#### 3.4.5 GraphX
Es un framework de procesamiento de grafos distribuido construido sobre Spark. No se trata de gráficos y diagramas como QuickSight, sino de grafos en el sentido de estructuras de datos en ciencias de la computación (por ejemplo, un grafo de personas en una red social).

Proporciona ETL, análisis exploratorio y computación iterativa de grafos para permitir a los usuarios construir y transformar interactivamente una estructura de datos de grafo a escala.

### 3.5 Spark Streaming en detalle

En las aplicaciones Spark, generalmente se usa un Dataset en el código que hace referencia a tus datos, que se tratan como una tabla de base de datos. Con Spark Streaming, esa tabla sigue creciendo a medida que se reciben nuevos fragmentos de datos en tiempo real. Puedes consultar esos datos utilizando ventanas de tiempo (por ejemplo, mirar la última hora de datos en tu flujo de datos entrante).

El Structured Streaming en Spark modela los datos de streaming entrantes como una tabla de base de datos sin límites que puedes consultar cuando quieras. Spark Streaming se integra con Kinesis: puedes tener un productor Kinesis publicando datos en un Kinesis Data Stream, y hay una forma en el KCL de implementar un Dataset de Spark construido sobre ese flujo de datos.

### 3.6 Apache Zeppelin

Zeppelin es básicamente un notebook para Spark. Permite ejecutar código Spark de forma interactiva dentro de un entorno de notebook en un navegador. Puedes:
- Ejecutar consultas SQL contra tus datos Spark usando Spark SQL
- Consultar tus resultados y visualizarlos en gráficos usando herramientas como matplotlib y seaborn

Hace que Spark se sienta más como una herramienta de ciencia de datos y permite preprocesar datos en un formato familiar para los científicos de datos.

## 4. Feature Engineering and the Curse of Dimensionality

### 4.1 ¿Qué es Feature Engineering?

Feature Engineering es el proceso de aplicar lo que sabes sobre tus datos para seleccionar las features (características) que estás utilizando, crear nuevas features o transformar las features existentes. Las features son los atributos de tus datos de entrenamiento, los elementos con los que estás entrenando tu modelo.

Por ejemplo, si estamos tratando de predecir cuánto dinero ganan las personas basándonos en varios atributos, las features podrían incluir:
- La edad de una persona
- Su altura
- Su peso
- Su dirección
- Qué tipo de automóvil conducen
- Y muchas otras características

Algunas de estas features serán relevantes para lo que estás tratando de predecir, y otras no lo serán. El proceso de Feature Engineering consiste, en parte, en seleccionar qué features son importantes para lo que estás tratando de predecir y elegirlas sabiamente.

### 4.2 Componentes del Feature Engineering

#### 4.2.1 Selección de Features
Identificar y seleccionar las features que son más relevantes para tu problema de predicción.

#### 4.2.2 Transformación de Features
Muchas veces necesitas transformar las features de alguna manera:
- Los datos sin procesar pueden no ser útiles para el modelo específico que estás utilizando
- Puede ser necesario normalizar o escalar las features
- Puede ser necesario codificarlas de alguna manera específica

#### 4.2.3 Manejo de Datos Faltantes
En el mundo real, a menudo no se dispone de datos completos para cada punto de datos. La forma en que eliges manejar esto puede influir significativamente en la calidad del modelo resultante.

#### 4.2.4 Creación de Nuevas Features
A veces, quieres crear nuevas features a partir de las existentes:
- Las tendencias numéricas pueden representarse mejor tomando el logaritmo o el cuadrado de una feature
- Puede ser mejor combinar matemáticamente varias features en una para reducir la dimensionalidad

### 4.3 Importancia del Feature Engineering

No puedes simplemente tomar todos los datos que tienes, arrojarlos a un algoritmo de machine learning y esperar buenos resultados. El Feature Engineering es realmente el arte del machine learning. Es donde se aplica tu experiencia para obtener buenos resultados.

No es solo un proceso mecánico donde sigues unos pasos, tomas todos los datos, los introduces en un algoritmo y ves qué predicciones obtienes. Esto es lo que separa a los buenos practicantes de machine learning de los malos. Aquellos que pueden hacer Feature Engineering son los más exitosos y valiosos en el mercado laboral.

Este conocimiento generalmente no se enseña formalmente, sino que se aprende a través de la experiencia y la práctica real de machine learning. Por eso el examen de certificación de AWS Machine Learning se centra mucho en este tema - está tratando de identificar a las personas que tienen experiencia real en este campo.

### 4.4 La Maldición de la Dimensionalidad

La maldición de la dimensionalidad explica por qué el Feature Engineering es tan importante. No puedes simplemente utilizar todas las features disponibles y esperar buenos resultados. Tener demasiadas features puede ser problemático por varias razones:

#### 4.4.1 Datos Dispersos
Considerando el ejemplo anterior de entrenar un modelo con atributos de personas, podríamos generar cientos de atributos diferentes. Cada persona se puede visualizar como un vector en el espacio dimensional de todas estas features:
- Con una sola feature (por ejemplo, edad), representamos a una persona como un vector a lo largo de un solo eje
- Con dos features (por ejemplo, edad y altura), tenemos un vector bidimensional
- Con tres features (edad, altura, ingresos), tenemos un vector tridimensional

A medida que agregamos más dimensiones, el espacio disponible para trabajar explota exponencialmente. Esto es la maldición de la dimensionalidad.

Cuantas más features tengas, mayor será el espacio en el que debemos encontrar una solución, lo que dificulta encontrar la solución óptima. Cuantas más features tengas, más dispersos serán tus datos dentro de ese espacio de solución, y más difícil será encontrar la mejor solución.

#### 4.4.2 Problemas de Rendimiento
Desde una perspectiva de rendimiento, imagina tratar de crear una red neuronal con entradas para cada una de estas features. Esta red tendría que ser masiva, extremadamente ancha en la base y probablemente extremadamente profunda, para encontrar todas las relaciones entre estas numerosas features. Sería increíblemente difícil hacer que converja.

### 4.5 Técnicas para la Reducción de Dimensionalidad

No siempre tienes que adivinar qué features son importantes. Existen formas más sistemáticas de reducir la dimensionalidad:

#### 4.5.1 Principal Component Analysis (PCA)
PCA es una forma de tomar todas las dimensiones superiores (todas las diferentes features) y destilarlas en un número menor de features o dimensiones. Intenta hacer esto de una manera que preserva la información lo mejor posible.

Las features resultantes no son necesariamente etiquetables; son features creadas artificialmente que capturan la esencia de las features originales.

#### 4.5.2 K-means Clustering
K-means clustering es otra técnica para reducción de dimensionalidad.

Ambas técnicas (PCA y K-means) son métodos no supervisados, lo que significa que no necesitas entrenarlos con datos etiquetados. Simplemente puedes introducir los datos de features en estos algoritmos, y producirán un conjunto más pequeño de dimensiones que funcionarán casi tan bien como el conjunto original.

### 4.6 Conclusión

Más features no siempre es mejor debido a la maldición de la dimensionalidad. El Feature Engineering es crucial para seleccionar las features más relevantes, transformarlas adecuadamente y, en última instancia, construir modelos de machine learning más efectivos y eficientes.

## 5. Imputing Missing Data

### 5.1 El problema de los datos faltantes

Una parte importante del Feature Engineering es la imputación de datos faltantes. En el mundo real, es común que para cada observación haya algunos puntos de datos faltantes. Existen diferentes técnicas para manejar este problema.

### 5.2 Mean Replacement (Reemplazo por la media)

Una solución simple es el reemplazo por la media:
- Si falta un atributo o feature en una fila de tus datos, se reemplaza con la media de toda la columna
- Es importante tomar la media de todas las observaciones de esa misma feature (columna), no de la fila

**Ventajas**:
- Rápido y fácil de implementar
- No afecta la media ni el tamaño de la muestra de tu conjunto de datos

**Consideraciones**:
- Si hay muchos valores atípicos (outliers) en tu conjunto de datos, la mediana puede ser una mejor opción que la media
- Por ejemplo, en un conjunto de datos sobre ingresos, la media podría estar sesgada por millonarios o billonarios

### 5.3 Limitaciones del reemplazo por la media

A pesar de su simplicidad, el reemplazo por la media no es generalmente la mejor opción:

- Solo funciona a nivel de columna, ignorando correlaciones entre features
  - Por ejemplo, si existe una relación entre edad e ingresos, esta relación se perdería
  - Podrías terminar imputando que un niño de 10 años gana $50,000 al año

- No funciona con features categóricas
  - No tiene sentido calcular la media de datos categóricos
  - Para datos categóricos, podrías usar el valor más frecuente, pero es un enfoque diferente

- En general, no es un método muy preciso para la imputación

### 5.4 Eliminación de filas con datos faltantes

Otra opción simple es eliminar las filas que tienen datos faltantes:

- Puede ser razonable si tienes suficientes datos y eliminar algunas filas no afecta significativamente
- Sin embargo, debes asegurarte de que eliminar las filas con datos faltantes no sesgue tu conjunto de datos

**Ejemplo de posible sesgo**:
- Si estás analizando ingresos, es posible que las personas con ingresos muy altos o muy bajos sean más propensas a no reportarlos
- Al eliminar estas observaciones, podrías estar eliminando datos importantes para tu modelo

### 5.5 Técnicas avanzadas de imputación

#### 5.5.1 K-Nearest Neighbors (KNN)
- Encuentra las K filas más similares a la que tiene datos faltantes
- Promedia los valores de esas filas similares para imputar el valor faltante
- Aprovecha las relaciones entre otras features del conjunto de datos
- Funciona mejor con datos numéricos que con datos categóricos

#### 5.5.2 Deep Learning (Aprendizaje profundo)
- Especialmente útil para datos categóricos
- Desarrolla un modelo de machine learning específicamente para imputar datos
- Aunque es complejo y requiere mucho código y ajuste, puede proporcionar resultados excelentes

#### 5.5.3 Regresión múltiple
- Utiliza regresión sobre las otras features del conjunto de datos
- Puede encontrar relaciones lineales o no lineales entre la feature faltante y otras features

#### 5.5.4 MICE (Multiple Imputation by Chained Equations)
- Una técnica avanzada considerada el estado del arte para la imputación de datos faltantes
- Utiliza ecuaciones encadenadas para imputar valores múltiples

### 5.6 La mejor solución: obtener más datos

Finalmente, la mejor manera de lidiar con datos faltantes es simplemente obtener más datos:
- Esforzarse por conseguir datos más completos
- Asegurarse de que la calidad de los datos sea alta desde el principio
- Aunque las técnicas de imputación son útiles, es difícil superar la calidad de los datos reales

Cuanto mejor sea la calidad de los datos que ingresas en tu sistema, mejores serán los resultados que obtendrás. La imputación es una forma de compensar cuando no tienes suficientes datos y no puedes obtener más, pero siempre es preferible conseguir datos más completos y de mejor calidad cuando sea posible.

## 6. Dealing with Unbalanced Data

### 6.1 El problema de los datos desbalanceados

Datos desbalanceados se refiere a cuando existe una gran discrepancia entre los casos positivos y negativos en nuestros datos de entrenamiento. Un ejemplo común es en la detección de fraude, donde el fraude real es bastante raro, por lo que la mayoría de los datos de entrenamiento contendrán filas que no son fraudulentas.

Esto puede dificultar la construcción de un modelo que pueda identificar el fraude, ya que tiene muy pocos puntos de datos para aprender en comparación con todos los puntos de datos que no son fraude. Es muy fácil que un modelo simplemente diga: "dado que el fraude solo ocurre el 0.01% del tiempo, voy a predecir que no es fraude todo el tiempo", logrando una precisión aparentemente alta pero sin valor real.

**Aclaración importante**: En este contexto, "positivo" y "negativo" no se refieren a resultados buenos o malos. "Positivo" simplemente significa "la condición que estamos tratando de detectar". Si nuestro modelo está tratando de detectar fraude, entonces el fraude es el caso positivo, aunque el fraude sea algo negativo.

Este problema es principalmente relevante con redes neuronales.

### 6.2 Técnicas para manejar datos desbalanceados

#### 6.2.1 Oversampling (Sobremuestreo)
Una solución simple es el sobremuestreo. Consiste en tomar muestras de tu clase minoritaria (en el ejemplo del fraude, los casos de fraude) y copiarlas varias veces. Aunque parezca que esto no debería ayudar, en realidad funciona con redes neuronales.

#### 6.2.2 Undersampling (Submuestreo)
En lugar de crear más casos de tu clase minoritaria, puedes eliminar casos de la mayoría. En el caso del fraude, estaríamos hablando de eliminar algunos de esos casos no fraudulentos para equilibrar un poco más el conjunto de datos.

Sin embargo, desechar datos no suele ser la respuesta correcta, ya que estás descartando información. El submuestreo solo tiene sentido si estás tratando específicamente de evitar algún problema de escalado con tu entrenamiento, por ejemplo, si tienes más datos de los que puedes manejar con el hardware disponible.

#### 6.2.3 SMOTE (Synthetic Minority Oversampling Technique)
Una técnica mejor que el sobremuestreo o submuestreo es SMOTE, que genera artificialmente nuevas muestras de la clase minoritaria utilizando vecinos más cercanos.

Al igual que el uso de KNN para imputación, SMOTE ejecuta K-Nearest Neighbors en cada muestra de la clase minoritaria y luego crea nuevas muestras a partir de esos resultados KNN tomando la media de esos vecinos. En lugar de simplemente hacer copias ingenuas de otros casos de prueba para la clase minoritaria, estamos fabricando nuevos puntos basados en promedios de otras muestras.

SMOTE tanto genera nuevas muestras como realiza un submuestreo de la clase mayoritaria, lo que lo hace mejor que el simple sobremuestreo mediante copias, ya que está fabricando nuevos puntos de datos que todavía tienen alguna base en la realidad.

#### 6.2.4 Ajuste de umbrales
Un enfoque más simple es ajustar los umbrales al hacer inferencias y aplicar tu modelo a los datos. Para clasificaciones (como fraude o no fraude), tendrás algún tipo de umbral de probabilidad en el que decides que algo es probablemente fraude.

Si tienes demasiados falsos positivos, una forma de solucionarlo es aumentar ese umbral. Esto garantiza reducir tu tasa de falsos positivos, pero a costa de más falsos negativos.

Antes de hacer algo así, debes pensar en el impacto que tendrá ese umbral:
- Si aumentas el umbral, tendrás menos transacciones marcadas como fraude (menos falsos positivos, pero podrías perder casos reales de fraude)
- Si disminuyes el umbral, detectarás más casos de fraude (pero con más falsos positivos)

Debes considerar el costo de un falso positivo frente a un falso negativo y elegir tus umbrales en consecuencia. En el caso del fraude, podría ser mejor equivocarse al marcar algo como fraudulento cuando no lo es, que no detectar un caso real de fraude.

## 7. Handling Outliers

### 7.1 Fundamentos matemáticos para identificar outliers

Antes de hablar sobre outliers (valores atípicos), necesitamos entender algunos conceptos matemáticos básicos.

#### 7.1.1 Varianza
La varianza, generalmente representada como sigma al cuadrado (σ²), es el promedio de las diferencias al cuadrado desde la media. Para calcular la varianza de un conjunto de datos:

1. Calcula la media del conjunto de datos
2. Encuentra las diferencias desde la media para cada punto de datos
3. Eleva al cuadrado estas diferencias
4. Calcula el promedio de estos valores al cuadrado

Por ejemplo, si tenemos los datos [1, 4, 5, 4, 8]:
- La media es 4.4
- Las diferencias desde la media son [-3.4, -0.4, 0.6, -0.4, 3.6]
- Las diferencias al cuadrado son [11.56, 0.16, 0.36, 0.16, 12.96]
- La varianza es el promedio de estos valores: 5.04

Elevamos al cuadrado las diferencias por dos razones:
- Para asegurar que las varianzas negativas cuenten tanto como las positivas
- Para dar más peso a los outliers

#### 7.1.2 Desviación Estándar
La desviación estándar (σ) es simplemente la raíz cuadrada de la varianza. En nuestro ejemplo, la desviación estándar es √5.04 = 2.24.

La desviación estándar se utiliza comúnmente para identificar outliers en un conjunto de datos. En una distribución normal, los valores dentro de una desviación estándar de la media son considerados típicos. En nuestro ejemplo, 1 y 8 caen fuera de este rango (4.4 ± 2.24), por lo que podemos considerarlos matemáticamente como outliers.

### 7.2 Identificación de outliers

Existen varios métodos para identificar outliers:

1. **Desviación estándar**: Definir outliers como puntos que están a una cierta cantidad de desviaciones estándar de la media (comúnmente 1, 2 o 3 desviaciones estándar)

2. **Diagramas de caja y bigotes (Box and Whisker)**: Estos diagramas tienen una forma incorporada de detectar y visualizar outliers, definiéndolos como puntos que están fuera de 1.5 veces el rango intercuartil

3. **Random Cut Forest**: Algoritmo propio de AWS para detección de outliers, disponible en varios servicios:
   - Amazon QuickSight
   - Kinesis Analytics
   - SageMaker
   - Y otros servicios de AWS

No existe una regla absoluta sobre qué es un outlier. Debes examinar tus datos, observar la distribución, mirar el histograma y comprender qué representan los valores atípicos antes de decidir eliminarlos.

### 7.3 Manejo de outliers

Una vez identificados los outliers, debes decidir cómo manejarlos. A veces es apropiado eliminarlos, y a veces no. Debes tomar esta decisión responsablemente.

#### 7.3.1 Ejemplos donde podría ser apropiado eliminar outliers:

- En filtrado colaborativo para recomendaciones de películas, donde algunos usuarios "power users" han calificado casi todas las películas y podrían tener una influencia desproporcionada
- Al analizar datos de registros web donde los outliers podrían indicar tráfico malicioso o bots
- Cuando los outliers representan errores en la recopilación de datos

#### 7.3.2 Ejemplos donde no debería eliminar outliers:

- Cuando calculas el ingreso promedio y los millonarios/billonarios son outliers legítimos que deberían influir en el promedio
- Cuando los outliers representan casos reales y relevantes para lo que estás tratando de modelar

### 7.4 Ejemplo práctico: Desigualdad de ingresos

Si estamos modelando distribuciones de ingresos y tenemos un conjunto de datos con ingresos centrados alrededor de $27,000, pero con un solo billonario, este outlier distorsionaría significativamente nuestra distribución.

Un enfoque simple para la detección de outliers sería:
1. Calcular la mediana del conjunto de datos completo
2. Calcular la desviación estándar
3. Eliminar cualquier punto que esté fuera de dos desviaciones estándar

Al aplicar esta función y volver a graficar el histograma, obtendríamos datos más significativos al rechazar ese outlier.

Lo importante es considerar si realmente es apropiado eliminar ese billonario o no, basado en el efecto que tiene en el resultado empresarial que estás tratando de lograr.

## 8. Binning, Transforming, Encoding, Scaling and Shuffling

### 8.1 Binning

Binning es una técnica que consiste en tomar datos numéricos y transformarlos en datos categóricos, agrupando los valores en rangos.

**Ejemplo**: 
- Para edades de personas, podríamos agrupar a todos los que están en sus 20s en un grupo, los de 30s en otro, y así sucesivamente.
- En lugar de usar la edad exacta (22.3 años), utilizamos la categoría "veinteañeros".

**Razones para usar binning**:
1. **Manejar incertidumbre en las mediciones**: Si no hay precisión exacta en los datos (por ejemplo, alguien podría recordar mal su cumpleaños), el binning puede ayudar a cubrir esta imprecisión.
2. **Usar modelos que trabajan con datos categóricos**: Aunque esto es cuestionable porque estás descartando información, podría ser necesario para ciertos algoritmos.

#### 8.1.1 Quantile Binning
Una variante especial es el quantile binning, que categoriza los datos por su lugar en la distribución, asegurando que cada bin contenga un número igual de muestras. La ventaja es que garantiza tamaños uniformes en cada bin.

### 8.2 Transformación de datos

Consiste en aplicar alguna función a nuestras features para hacerlas más adecuadas para los algoritmos que vamos a utilizar.

**Ejemplo**: 
- Si tienes datos con una tendencia exponencial, podría beneficiarse de una transformación logarítmica para hacerlos más lineales.
- Esto puede ayudar a muchos modelos que tienen dificultad con datos no lineales.

Un caso real es YouTube, que en su sistema de recomendaciones, para cada feature numérica x, también incluye x² (cuadrado) y √x (raíz cuadrada). Esto les permite aprender funciones superlineales y sublineales en los datos, mejorando significativamente sus resultados.

Es importante notar que a veces no se reemplaza el dato original con la transformación, sino que se crean nuevas features a partir de la transformación de las existentes.

### 8.3 Encoding (Codificación)

La codificación es muy común en deep learning, donde los modelos requieren un tipo específico de entrada, y debes transformar tus datos al formato requerido.

#### 8.3.1 One-Hot Encoding
Es una técnica muy común donde se crea un "bucket" (contenedor) para cada categoría posible. Se representa con un "1" la categoría que existe y con "0" las que no.

**Ejemplo**: Para reconocimiento de dígitos manuscritos (0-9):
- Si tenemos una imagen del número 8, la codificación one-hot sería: [0,0,0,0,0,0,0,0,1,0]
- El "1" en la novena posición (índice 8) indica que es el número 8
- Todos los demás valores son 0, indicando que no es ninguno de esos dígitos

Esto es necesario en deep learning porque las neuronas generalmente están activadas (1) o desactivadas (0), y no podemos simplemente ingresar un número como 8 directamente y esperar que funcione.

### 8.4 Scaling y Normalización

La mayoría de los modelos requieren que los datos de features estén normalizados o escalados:

- Muchos modelos prefieren que sus datos tengan una distribución normal alrededor de cero
- Como mínimo, la mayoría de los modelos requieren que los datos estén escalados a valores comparables

Si no normalizas los datos, las features con magnitudes más grandes tendrán más peso en tu modelo del que deberían.

**Ejemplo**: Si intentas entrenar un modelo con ingresos (números grandes como 50,000) y edades (números pequeños como 30-40), sin normalización el ingreso tendría un impacto mucho mayor en el modelo.

Es fácil implementar esto con bibliotecas como scikit-learn en Python, que incluye módulos como `MinMaxScaler`. Solo recuerda que si estás prediciendo valores numéricos (no categorías), deberás revertir el escalado para obtener resultados significativos.

### 8.5 Shuffling (Barajado)

Muchos algoritmos se benefician de mezclar aleatoriamente los datos de entrenamiento. Esto es importante porque:

- Elimina cualquier señal residual en los datos que provenga del orden en que fueron recolectados
- Puede mejorar significativamente la calidad del modelo

Hay muchos casos donde un modelo con mal rendimiento mejoró drásticamente simplemente al barajar los datos de entrada, así que es una técnica que no debes olvidar implementar.

## 9. SageMaker Overview

### 9.1 Introducción a Amazon SageMaker

SageMaker es el servicio principal de Amazon para machine learning. Aunque puede utilizarse para IA generativa, existen otros servicios específicamente diseñados para eso. SageMaker existe desde antes de que la IA generativa fuera tan relevante y está diseñado para ser más amplio, abarcando algoritmos de machine learning en general, incluyendo deep learning tradicional.

La característica clave de SageMaker es que está construido para manejar el flujo de trabajo completo en torno al machine learning:
- Obtención, limpieza y preparación de datos
- Entrenamiento y evaluación de modelos
- Despliegue de modelos y evaluación de resultados en producción

De esta manera, SageMaker cubre todo el ciclo de vida del machine learning a través de sus diversas funcionalidades.

### 9.2 Flujo de trabajo de entrenamiento y despliegue

Conceptualmente, el proceso de entrenamiento y despliegue de un modelo en SageMaker funciona de la siguiente manera:

#### 9.2.1 Proceso de despliegue (de arriba hacia abajo)
1. Una aplicación cliente quiere obtener predicciones de un modelo de machine learning creado con SageMaker
2. La aplicación se comunica con un endpoint (que pueden ser múltiples endpoints si se está escalando)
3. Los endpoints se comunican con el modelo desplegado y alojado
4. El modelo utiliza:
   - Artefactos alojados en S3
   - Código de modelo para realizar inferencias (almacenado posiblemente en ECR - Elastic Container Registry)

#### 9.2.2 Proceso de entrenamiento (de abajo hacia arriba)
1. El código de entrenamiento se aloja en ECR
2. Este código consume datos de entrenamiento desde S3
3. Una vez entrenado, el modelo produce artefactos que se almacenan en S3
4. El modelo se despliega utilizando código de inferencia alojado en ECR
5. El modelo desplegado es accesible a través de endpoints para inferencia
6. La aplicación cliente se comunica con estos endpoints

### 9.3 Formas de configurar SageMaker

Existen diferentes maneras de configurar y utilizar SageMaker:

#### 9.3.1 Usando código (SageMaker Notebooks)
Una forma es utilizando código Python para orquestar todo el proceso desde un SageMaker notebook:

- Son notebooks Jupyter ejecutados en instancias EC2
- Vienen con acceso incorporado a S3
- Incluyen bibliotecas útiles preinstaladas como scikit-learn, Spark, TensorFlow o PySpark
- Ofrecen una amplia variedad de modelos incorporados

Desde estos notebooks puedes:
- Iniciar instancias de entrenamiento usando los tipos de instancia que necesites
- Iniciar el entrenamiento
- Evaluar y ajustar el modelo
- Desplegar el modelo para hacer predicciones a escala

Esto permite orquestar todo el ciclo de vida desde un solo lugar: procesamiento de datos, entrenamiento, ajuste y despliegue del modelo.

#### 9.3.2 Usando la consola de AWS
Si prefieres no escribir código, gran parte de este proceso se puede realizar directamente desde la interfaz de usuario en la consola de SageMaker:

- Configurar trabajos de entrenamiento
- Seleccionar entre muchos algoritmos incorporados
- Especificar la fuente de datos de entrenamiento
- Gestionar el despliegue

Esto significa que no es necesario escribir código Python para usar SageMaker y construir modelos de machine learning que puedan realizar predicciones en producción.


## 10. SageMaker AI Domains

### 10.1 Introducción a SageMaker AI Domains

Antes de poder hacer cualquier cosa en SageMaker AI (su nombre actual), necesitas crear lo que se llama un "domain" (dominio). Todo lo que haces dentro de SageMaker AI está bajo el paraguas de este dominio que creas.

### 10.2 Componentes de un SageMaker Domain

Un dominio de SageMaker consta de varios componentes:

1. **Volumen EFS compartido**: 
   - Un volumen de Amazon Elastic File System (EFS) que se comparte en todo el dominio
   - SageMaker divide este volumen EFS entre los diferentes usuarios y recursos compartidos
   - Bajo el capó, hay un volumen EFS que está asociado con ese dominio

2. **Lista de usuarios autorizados**:
   - Usuarios que tienen permiso para acceder al dominio y los recursos dentro de él

3. **Configuraciones del dominio**:
   - Configuraciones de políticas varias
   - Configuraciones de VPC (Virtual Private Cloud)

### 10.3 User Profiles (Perfiles de Usuario)

Dentro de un dominio puedes tener perfiles de usuario:

- Un perfil de usuario es básicamente el concepto de una persona que utiliza un dominio de SageMaker
- Todo lo que está centrado en el usuario en la interfaz estará vinculado a un perfil de usuario dentro de ese dominio
- Los perfiles de usuario poseen aplicaciones personales
- Como usuario dentro de un dominio de SageMaker, puedes crear una instancia de SageMaker Studio para realizar tareas (entrenar modelos, desplegar modelos, etc.)
- Cada usuario tiene su propio directorio EFS privado como parte de su perfil, lo que proporciona un espacio de trabajo privado dentro del volumen EFS
- Los usuarios también pueden acceder a recursos compartidos que se pueden utilizar entre otros usuarios dentro del dominio

### 10.4 Recursos Compartidos

Dentro del dominio de SageMaker existen espacios compartidos:

- Acceso a un directorio EFS compartido donde puedes intercambiar información entre usuarios
- Una aplicación IDE comunal (SageMaker Studio) que puede ser utilizada por todos los usuarios dentro del dominio
- Posibilidad de compartir datos de entrenamiento, notebooks y otros recursos

### 10.5 Configuración de VPC en SageMaker Domains

Un aspecto importante al usar un dominio de SageMaker AI es cómo se asocian las VPC con él:

#### 10.5.1 Configuración predeterminada (dos VPCs)

Cuando creas un dominio de SageMaker, por defecto tendrá dos VPCs asociadas:

1. **VPC para Internet externo**:
   - Para acceder a datos en internet o desplegar datos en internet
   - Esta VPC es gestionada por SageMaker AI (no tienes que configurarla)

2. **Tu propia VPC**:
   - Se utiliza para cualquier tráfico cifrado a tu propio volumen EFS gestionado por SageMaker
   - Debes especificar esta VPC
   - Necesitas especificar también las subredes dentro de ella (generalmente todas)
   - Debes especificar grupos de seguridad asociados con esa VPC

#### 10.5.2 Configuración VPC-only

No tienes que usar la configuración predeterminada. Puedes cambiarla para usar solo tu propia VPC:

- Selecciona "VPC only" al crear tu dominio
- Todo el tráfico pasará a través de tu propia VPC
- Tienes control más detallado sobre lo que esa VPC puede acceder

### 10.6 Resumen

El dominio de SageMaker AI es, en resumen, una unidad organizativa para todo lo que haces dentro de SageMaker. Abarca:
- Todos tus usuarios
- Todas tus aplicaciones
- Tus espacios de trabajo compartidos
- La arquitectura de red subyacente

## 11. Data Processing, Training, and Deployment with SageMaker AI

### 11.1 Procesamiento de datos

Si no deseas crear tu propio código y contenedor para el procesamiento de datos, puedes utilizar los algoritmos incorporados en SageMaker:

- Existen contenedores de procesamiento incorporados que puedes elegir directamente desde SageMaker
- Una vez que termina de procesar tus datos, la salida se almacena en otro bucket S3 que especifiques
- Este bucket S3 con datos procesados se utilizará posteriormente para el entrenamiento

### 11.2 Entrenamiento de modelos

Para entrenar un modelo en SageMaker, necesitas crear un trabajo de entrenamiento:

- El trabajo de entrenamiento tomará como entrada la URL del bucket S3 que contiene los datos de entrenamiento procesados
- Debes asignar varios recursos de cómputo para el entrenamiento
  - El entrenamiento de machine learning puede ser costoso y requerir hardware potente
  - Puedes elegir qué tipo de hardware usar y cuánto
  - Diferentes algoritmos tienen diferentes capacidades en términos de escalabilidad
    - Algunos funcionan mejor con una sola computadora
    - Otros funcionan mejor con múltiples tipos de instancias
    - Cada algoritmo tiene sus propias recomendaciones sobre qué tipo de instancia usar

- Necesitarás un bucket S3 para la salida (artefactos del modelo)
  - Aquí es donde se almacena el modelo entrenado

- También necesitarás proporcionar una ruta a ECR (Elastic Container Registry) donde vive tu código de entrenamiento
  - Todo está basado en contenedores: contenedor para procesamiento de datos y contenedor para el código de entrenamiento

### 11.3 Opciones de algoritmos de entrenamiento

Tienes múltiples opciones para el entrenamiento:

1. **Algoritmos incorporados de SageMaker**
2. **Spark Machine Learning Library**
3. **Tu propio código en:**
   - Python
   - TensorFlow
   - PyTorch
   - MXNet
4. **Para modelos no basados en deep learning:**
   - scikit-learn
   - RL Estimator (para aprendizaje por refuerzo)
   - XGBoost
5. **Hugging Face**
   - Repositorio grande de modelos, incluyendo modelos de IA generativa y LLMs
6. **Chainer**
7. **Tu propia imagen Docker personalizada**
8. **Algoritmos disponibles para compra en AWS Marketplace**

### 11.4 Despliegue de modelos

Una vez entrenado el modelo, necesitas desplegarlo y ponerlo en producción:
- El modelo se guardará en un bucket S3
- Existen diferentes opciones para el despliegue:

#### 11.4.1 Endpoints en tiempo real
- Crear un endpoint persistente para hacer predicciones en tiempo real bajo demanda
- Escalar según sea necesario

#### 11.4.2 Transformación por lotes (Batch Transform)
- Para cuando no necesitas predicciones en tiempo real
- Procesa grandes grupos de datos de una vez
- Obtiene predicciones para todo un conjunto de datos sin necesidad de un endpoint en tiempo real

#### 11.4.3 Opciones adicionales de despliegue

- **Inference Pipelines**: para orquestar procesamientos más complejos
- **SageMaker Neo**: para desplegar modelos entrenados en dispositivos edge
  - Útil cuando no hay conectividad confiable a internet/nube
  - Cuando no puedes tolerar la latencia de ir a la nube

- **Elastic Inference**: para acelerar tus modelos desplegados
- **Escalado automático**: aumenta automáticamente el número de endpoints según sea necesario cuando aumenta el tráfico
- **Shadow Testing**: evalúa nuevos modelos contra los modelos actualmente desplegados para detectar errores

SageMaker ofrece muchas características que permiten desplegar modelos recién entrenados de manera controlada y revertir cambios si no funcionan como se esperaba, lo cual es una parte importante de las operaciones.

## 12. Amazon SageMaker Ground Truth and Label Generation

### 12.1 Introducción a SageMaker Ground Truth

SageMaker Ground Truth es un servicio relativamente nuevo de Amazon diseñado para utilizar humanos en el etiquetado de datos. Fundamentalmente, está relacionado con el manejo de datos faltantes, especialmente etiquetas y características (features).

La idea básica es que si tienes datos faltantes que pueden ser inferidos fácilmente por un humano, Ground Truth te permite externalizar estas tareas a personas reales para completar esa información faltante.

### 12.2 Casos de uso comunes

El caso de uso más común es la clasificación de imágenes:
- Si estás entrenando un nuevo modelo de clasificación de imágenes, necesitas etiquetar todas las imágenes de entrenamiento con lo que contienen
- Esta es una tarea que generalmente no es fácil para una computadora
- Por ejemplo, identificar si una imagen muestra un partido de baloncesto o fútbol, o qué tipo de ave aparece en una imagen

Ground Truth administra un grupo de personas que etiquetarán tus datos para propósitos de entrenamiento. Cuando tienes un conjunto de datos grande, como muchas imágenes que necesitan etiquetas, a veces los seres humanos son la mejor manera de obtener esas etiquetas.

### 12.3 Aprendizaje automático en Ground Truth

Lo que diferencia a Ground Truth es que crea su propio modelo a medida que se van agregando etiquetas:

- El modelo aprende a medida que recibe más y más datos etiquetados de los humanos
- Con el tiempo, solo enviará a los humanos las etiquetas sobre las que no está seguro
- A medida que recibe más etiquetas de los etiquetadores humanos, el modelo mejora
- Solo los casos ambiguos se envían a los humanos
- Esto reduce el costo de los trabajos de etiquetado hasta en un 70%

### 12.4 Opciones de personal para etiquetado

Ground Truth ofrece diferentes opciones para quién puede realizar el etiquetado:

1. **Amazon Mechanical Turk**: Una gran fuerza laboral de personas de todo el mundo que etiquetarán tus datos por una pequeña cantidad de dinero
2. **Equipo interno**: Puedes elegir usar tu propio equipo interno, lo que tiene sentido para datos sensibles
3. **Empresas de etiquetado profesionales**: Compañías especializadas que tienen personal dedicado al etiquetado de datos de entrenamiento

### 12.5 Alternativas al etiquetado humano

Existen otras técnicas para generar etiquetas de entrenamiento sin usar humanos:

1. **AWS Rekognition**: 
   - Servicio de AWS para reconocimiento de imágenes
   - Tiene un modelo pre-entrenado para la mayoría de los tipos comunes de objetos
   - Útil si necesitas clasificar imágenes con objetos comunes

2. **AWS Comprehend**:
   - Servicio de AWS para análisis de texto y modelado de temas
   - Puede generar automáticamente temas o sentimientos para un documento
   - Útil tanto para crear etiquetas como para generar características adicionales de entrenamiento

3. **Cualquier modelo pre-entrenado o técnica de aprendizaje no supervisado** puede ser útil para generar nuevas etiquetas de entrenamiento o incluso nuevas características para tu conjunto de datos

### 12.6 SageMaker Ground Truth Plus

Si Ground Truth todavía te parece complicado, existe Ground Truth Plus:

- Una solución llave en mano donde contratas a alguien a través de AWS para configurar y gestionar todo el proyecto
- Un equipo de "expertos de AWS" gestiona el flujo de trabajo y configura todo por ti
- Administran tu equipo de etiquetadores
- Solo necesitas completar un formulario indicando quién eres y qué intentas hacer
- Te contactarán para discutir los detalles y el precio (que no se divulga públicamente)
- Puedes seguir el progreso a través del portal de proyectos de Ground Truth Plus
- Cuando terminan, obtienes tus datos etiquetados desde S3

En resumen, Ground Truth Plus es una forma de delegar todo el problema del etiquetado de datos a alguien más, si estás dispuesto a pagar por ello.

## 13. Amazon Mechanical Turk

### 13.1 Origen del nombre

Amazon Mechanical Turk toma su nombre del "Turco Mecánico" original de 1770, una máquina de ajedrez construida por un inventor que parecía ser un autómata capaz de jugar al ajedrez, pero que en realidad era una ilusión. Dentro de la máquina se escondía una persona que controlaba los movimientos del "robot". Los espectadores creían estar jugando contra una máquina, cuando en realidad estaban enfrentándose a un humano oculto.

### 13.2 ¿Qué es Amazon Mechanical Turk?

Amazon Mechanical Turk (MTurk) es un mercado de crowdsourcing diseñado para realizar tareas humanas simples. Proporciona:

- Acceso a una fuerza laboral virtual distribuida
- Humanos reales que realizan las tareas en segundo plano
- Capacidad para ejecutar tareas simples y económicas a gran escala

### 13.3 Casos de uso comunes

MTurk es útil para diversas tareas que requieren inteligencia humana:

- **Clasificación de imágenes**: Etiquetar grandes cantidades de imágenes
- **Recopilación de datos**: Obtener información de diversas fuentes
- **Procesamiento empresarial**: Tareas repetitivas que requieren criterio humano
- **Cualquier tarea simple** que pueda distribuirse fácilmente a muchas personas

### 13.4 Relevancia para IA y Machine Learning

MTurk es especialmente valioso en el contexto de IA y machine learning para:

- Etiquetar imágenes para conjuntos de datos de entrenamiento
- Revisar recomendaciones generadas por algoritmos
- Validar resultados de modelos
- Crear datos de entrenamiento de alta calidad

Existe una profunda integración entre Amazon Mechanical Turk y otros servicios como Amazon SageMaker Ground Truth, lo que facilita su uso en flujos de trabajo de aprendizaje automático.

### 13.5 Funcionamiento del servicio

Cuando se utiliza MTurk como solicitante:

1. Defines una tarea simple con instrucciones claras
2. Estableces una recompensa por cada tarea completada (por ejemplo, 10 centavos por imagen etiquetada)
3. Publicas el trabajo en la plataforma

Los trabajadores (personas reales de todo el mundo):
- Ven los trabajos disponibles en su interfaz
- Pueden ver la recompensa por cada tarea
- Eligen en qué tareas quieren trabajar
- Completan las tareas y reciben el pago

### 13.6 Consideraciones de costos

El costo total depende del volumen y la recompensa que establezcas:
- Por ejemplo, si pagas 10 centavos por imagen y tienes 10 millones de imágenes, el costo total sería aproximadamente un millón de dólares
- Tú controlas el precio que pagas por tarea
- Cuanto más atractiva sea la recompensa, más rápido se completará tu trabajo

En resumen, Amazon Mechanical Turk proporciona acceso a una gran fuerza laboral dispuesta a realizar tareas pequeñas y repetitivas, lo que lo hace valioso para proyectos de machine learning que requieren etiquetado de datos u otras tareas que necesitan intervención humana.

## 14. SageMaker Data Wrangler

### 14.1 Introducción a Data Wrangler

SageMaker Data Wrangler es probablemente la tecnología más directamente aplicable en SageMaker para la ingeniería de datos. Es básicamente un pipeline ETL (Extract, Transform, Load) integrado en SageMaker Studio. Como podrías imaginar, tiene mucho en común con AWS Glue Data Studio, pero está personalizado específicamente para pipelines de machine learning.

### 14.2 Funcionalidades principales

Data Wrangler ofrece varias funcionalidades clave:

1. **Importación de datos**: Permite importar datos desde diversas fuentes (archivos CSV en S3, datos de streaming, etc.)

2. **Visualización de datos**: Te permite visualizar tus datos y asegurarte de que estás conforme con su distribución, así como identificar cualquier outlier antes de alimentar tu modelo de machine learning

3. **Transformación de datos**: Ofrece más de 300 transformaciones predefinidas para elegir, o puedes integrar tus propias transformaciones usando código pandas, PySpark o PySpark SQL si deseas hacer algo especializado

4. **Quick Model**: Una característica que permite entrenar rápidamente tu modelo con un subconjunto de datos y medir sus resultados, lo que te ayuda a experimentar con diferentes transformaciones y determinar cuáles producen los mejores resultados

### 14.3 Arquitectura y flujo de datos

Data Wrangler actúa como intermediario entre las fuentes de datos y sus destinos:

#### 14.3.1 Fuentes de datos compatibles:
- Amazon S3
- AWS Lake Formation
- Amazon Athena
- SageMaker Feature Store
- Amazon Redshift
- Cualquier fuente compatible con JDBC (como Salesforce u otras aplicaciones SaaS)
- Sistemas externos como Databricks

#### 14.3.2 Destinos posibles:
- Jupyter Notebooks (exporta código para procesamiento adicional)
- Amazon SageMaker Processing (para entrenar modelos)
- SageMaker Pipelines (como parte de un proceso más amplio)
- SageMaker Feature Store (para hacer las características transformadas disponibles para otros modelos)

Es importante entender que Data Wrangler no realiza las transformaciones en tu pipeline directamente, sino que genera código para realizar esas transformaciones. Es más un generador de código que una herramienta de procesamiento en sí misma.

### 14.4 Ejemplo práctico de uso

El flujo de trabajo típico en Data Wrangler incluye:

#### 14.4.1 Importación de datos
- Desde la pestaña "Import" en SageMaker Studio
- Se selecciona un archivo específico (por ejemplo, un CSV de S3)
- El sistema previsualiza automáticamente el contenido del archivo
- En el ejemplo, se utilizan datos de pasajeros del Titanic (quién sobrevivió, su clase, nombre, etc.)

#### 14.4.2 Exploración y validación de datos
- Se puede expandir la previsualización para ver más detalles
- Verificar y modificar los tipos de datos inferidos si es necesario
- Cambiar nombres de columnas para hacerlos más legibles
- Visualizar la distribución de los datos (por ejemplo, la distribución de edades de los pasajeros)
- Realizar comprobaciones de coherencia antes de procesar los datos

#### 14.4.3 Transformación de datos
- Aplicar transformaciones a los datos, como "encode categorical"
- El ejemplo muestra la transformación de la columna "class" mediante one-hot encoding
- Esto convierte una característica categórica (clase 1, 2 o 3) en tres características binarias

#### 14.4.4 Uso de Quick Model
- Entrenar rápidamente un modelo con los datos transformados
- Evaluar cómo las transformaciones afectan el rendimiento del modelo
- Iterar y optimizar las transformaciones en función de los resultados

#### 14.4.5 Exportación del flujo de datos
- Generar un notebook de Jupyter con el código Python necesario
- Este código encapsula todos los pasos ETL definidos en Data Wrangler
- Permite integrar el proceso en pipelines más amplios

### 14.5 Solución de problemas comunes

Si encuentras dificultades con Data Wrangler, considera estos consejos:

1. **Permisos IAM**: Asegúrate de que tu usuario de SageMaker Studio tenga los roles IAM apropiados para Data Wrangler

2. **Acceso a fuentes de datos**: Verifica que tus fuentes de datos permitan el acceso a Data Wrangler
   - Cualquier fuente de datos debe tener adjunta la política "Amazon SageMaker Full Access"

3. **Límites de recursos**: Si ves un error del tipo "the following instance type is not available"
   - Esto generalmente significa que necesitas solicitar un aumento de cuota
   - Ve a Service Quotas > Amazon SageMaker > Studio Kernel Gateway Apps
   - Solicita más instancias ml.m5.4xlarge
   - Este proceso requiere aprobación administrativa

## 15. SageMaker Model Monitor and SageMaker Clarify

### 15.1 Introducción a SageMaker Model Monitor

Cuando se despliega un sistema de machine learning en producción, no se puede garantizar que funcionará de la misma manera para siempre. La naturaleza de los datos que ingresan al sistema puede cambiar con el tiempo de formas imprevistas, lo que podría afectar el rendimiento del modelo.

SageMaker Model Monitor aborda este problema permitiéndote:
- Recibir alertas a través de CloudWatch sobre cualquier desviación de calidad en tus modelos desplegados
- Visualizar el data drift (cambios en los datos a lo largo del tiempo)
- Detectar automáticamente anomalías y outliers en los datos
- Identificar nuevas características en tus datos que no estaban presentes anteriormente

Todo esto se puede configurar visualmente dentro de SageMaker Studio, sin necesidad de escribir código.

### 15.2 Casos de uso de Model Monitor

Algunos ejemplos de situaciones donde Model Monitor es útil:

- **Predicciones crediticias**: La distribución de atributos de los solicitantes de préstamos puede cambiar con el tiempo
- **Problemas de calidad de datos**: Puedes comenzar a tener características de entrada faltantes o datos corruptos
- **Nuevas oportunidades**: Detectar nuevas características en los datos que podrían aprovecharse para mejorar el modelo

### 15.3 SageMaker Clarify y su integración con Model Monitor

SageMaker Clarify se integra con Model Monitor para proporcionar capacidades adicionales:

- **Detección de sesgos**: Identifica posibles sesgos en tus modelos, como desequilibrios entre diferentes grupos, edades o niveles de ingreso
- **Explicabilidad del modelo**: Ayuda a explicar el comportamiento del modelo, indicando qué características contribuyen más a las predicciones
- **Monitoreo de sesgos**: Permite configurar alertas cuando se detectan sesgos en los datos o predicciones

#### 15.3.1 Métricas de sesgo en Clarify

Clarify ofrece varias métricas para medir el sesgo en tus datos de entrenamiento:
- Divergencia de Kullback-Leibler (muy popular)
- Otras métricas específicas para diferentes tipos de sesgos

Para un examen de nivel practitioner, no es necesario conocer todos los detalles de estas métricas, pero es importante saber que existen múltiples opciones disponibles.

### 15.4 Funcionamiento de SageMaker Model Monitor

#### 15.4.1 Almacenamiento y seguridad
- Los datos de Model Monitor se almacenan en S3 con las medidas de seguridad correspondientes
- Toda la información de monitoreo se mantiene segura

#### 15.4.2 Programación y alertas
- Los trabajos de monitoreo se programan con lo que se llama "monitoring schedule"
- Pueden ejecutarse con la frecuencia que desees
- Las métricas se envían a CloudWatch
- Desde CloudWatch puedes configurar notificaciones para activar alarmas

Es importante destacar que Model Monitor solo envía los datos a los logs de CloudWatch; tú debes configurar las alarmas adecuadas.

#### 15.4.3 Acciones correctivas
Una vez que recibes una alarma, puedes tomar acciones correctivas como:
- Reentrenar el modelo
- Auditar los datos para identificar el problema

#### 15.4.4 Visualización
SageMaker Model Monitor puede integrarse con:
- TensorBoard
- Amazon QuickSight
- Tableau

Estas herramientas permiten visualizar los datos fuera de SageMaker Studio, aunque también puedes usar las visualizaciones integradas en la plataforma.

### 15.5 Tipos de monitoreo disponibles

Model Monitor ofrece varios tipos específicos de monitoreo:

1. **Drift en calidad de datos**: 
   - Monitorea si las propiedades estadísticas de tus características están cambiando con el tiempo
   - Se compara con una línea base que tú defines
   - La "calidad" es subjetiva y la defines tú según tus necesidades

2. **Drift en calidad del modelo**:
   - Mide la precisión de tu modelo a lo largo del tiempo
   - Funciona de manera similar al monitoreo de calidad de datos, con una línea base definida
   - Puede integrarse con "ground truth" (datos etiquetados por humanos) para comparar las predicciones del modelo con los resultados correctos

3. **Drift de sesgo**:
   - Utiliza Clarify para detectar si los sesgos en tu modelo están cambiando con el tiempo

4. **Drift de atribución de características**:
   - También una característica de Clarify
   - Mide si el impacto de cada característica en tus resultados está cambiando con el tiempo

## 16. Partial Dependence Plots (PDPs), Shapley values, y SHAP

### 16.1 Explicabilidad en SageMaker Clarify

El examen AWS Machine Learning - Specialty espera que tengas un buen nivel de conocimiento sobre cómo funciona SageMaker Clarify. Vamos a explorar más a fondo las herramientas de explicabilidad que proporciona.

### 16.2 Partial Dependence Plots (PDPs)

Los Partial Dependence Plots (PDPs) o Gráficos de Dependencia Parcial son una herramienta que proporciona SageMaker Clarify para:

- Mostrar la dependencia de la respuesta objetivo predicha en un conjunto de características de entrada
- Visualizar cómo afecta una característica específica a las predicciones del modelo

#### 16.2.1 Interpretación de un PDP

En un PDP:
- El eje X representa los valores de una característica (agrupados en rangos, similar a un histograma)
- El eje Y muestra los valores de predicción del modelo
- La curva muestra el efecto que tiene la característica en las predicciones del modelo

Por ejemplo, en un PDP que muestra la dependencia en la edad:
- Podríamos observar que después de los 50 años, las predicciones del modelo permanecen constantes
- Esto podría indicar que la edad es un factor importante hasta cierto punto (50 años)
- También podría revelar un problema potencial, como una falta de datos suficientes para edades superiores a 50 años
- Esta información podría señalar un desequilibrio en los datos de entrenamiento

Además de los gráficos, SageMaker Clarify también proporciona distribuciones de datos para cada rango de valores, lo que te permite verificar si existe algún desequilibrio de clases en tus datos.

### 16.3 Shapley Values

Los Shapley values son la técnica que SageMaker Clarify utiliza internamente para determinar el impacto que cada característica tiene en la salida del modelo.

#### 16.3.1 Origen y concepto

- Proviene de la teoría de juegos y se ha adaptado al machine learning
- La idea básica es eliminar características individuales una por una y medir el impacto que esto tiene en el modelo
- Esto permite cuantificar la contribución de cada característica a la predicción final

#### 16.3.2 Desafíos computacionales

El cálculo directo de Shapley values se vuelve computacionalmente intensivo cuando hay muchas características, ya que requiere probar todas las posibles combinaciones de características.

### 16.4 SHAP (SHapley Additive exPlanations)

Debido a los desafíos computacionales de los Shapley values tradicionales, SageMaker Clarify utiliza SHAP:

- SHAP significa "Shapley Additive exPlanations" (Explicaciones Aditivas de Shapley)
- Es una técnica de aproximación que permite calcular los Shapley values de manera más eficiente
- Proporciona información útil sobre el impacto de las características individuales sin tener que hacer un cálculo exhaustivo

SHAP ayuda a identificar:
- Qué características tienen mayor impacto en las predicciones del modelo
- Propiedades no intencionadas que podrían indicar problemas en los datos
- Posibles sesgos en el modelo

### 16.5 Asymmetric Shapley Values para Series Temporales

Si estás trabajando con datos de series temporales, SageMaker Clarify utiliza una variante llamada Asymmetric Shapley values:

- Diseñada específicamente para datos de series temporales
- Determina la contribución de las características de entrada en cada paso temporal hacia las predicciones o pronósticos
- Además de medir el impacto de cada característica individualmente, también mide el impacto a lo largo del tiempo
- Es más complejo que el SHAP tradicional porque considera la dimensión temporal adicional

Estos conceptos (PDPs, Shapley values, SHAP, y Asymmetric Shapley values) son términos clave que necesitas conocer para entender cómo funciona la explicabilidad en Amazon SageMaker Clarify.

## 17. SageMaker Feature Store

### 17.1 Introducción a Feature Store

En este contexto, una "feature" (característica) es una propiedad que se utiliza para entrenar un modelo de machine learning. Por ejemplo, si estás intentando predecir el partido político de alguien basándote en sus características, estas podrían ser su dirección, ingresos, edad, etc.

Las features son esencialmente columnas o campos en una fila donde estás utilizando esas piezas de información para intentar predecir una etiqueta (label) para esa fila. En el ejemplo anterior:
- Las features serían las propiedades de la persona (dirección, ingresos, edad)
- La etiqueta que intentamos predecir sería su partido político

### 17.2 Necesidad de Feature Store

Los modelos de machine learning requieren:
- Acceso rápido a los datos de características
- Acceso seguro a información que podría ser sensible o personal
- Organización eficiente de los datos
- Capacidad para compartir características entre diferentes modelos

Feature Store ayuda a evitar situaciones donde se almacenan las mismas características múltiples veces, permitiendo compartirlas entre diferentes proyectos de machine learning.

### 17.3 Fuentes de datos para Feature Store

SageMaker Feature Store se sitúa en el centro de un ecosistema donde los datos pueden provenir de diversas fuentes:

- SageMaker Studio
- Pipelines (Step Functions, SageMaker Pipelines, Apache Airflow)
- EMR
- AWS Glue
- SageMaker Processing
- Streaming (Kinesis, Kafka)
- SageMaker Data Wrangler
- AWS Glue DataBrew

La principal ventaja de Feature Store es la organización de estos datos, independientemente de su origen.

### 17.4 Estructura de Feature Store

Feature Store organiza los datos en lo que se denomina "feature groups" (grupos de características):

- Cada feature group contiene:
  - Identificadores de registro
  - Nombres de características
  - Tiempos de eventos asociados con esa característica

### 17.5 Funcionamiento de Feature Store

SageMaker Feature Store opera en dos modalidades principales:

#### 17.5.1 Mundo de streaming
- Datos provenientes de Kinesis, MSK, Apache Spark, SageMaker Data Wrangler, etc.
- Se utiliza la API `PutRecord` para alimentar datos en streaming a Feature Store
- Feature Store procesa estos datos en tiempo real

#### 17.5.2 Mundo de procesamiento por lotes (batch)
- Almacenamiento offline en S3
- Feature Store crea automáticamente un catálogo de datos en AWS Glue para los datos almacenados en S3
- Se pueden usar herramientas como Amazon Athena o Data Wrangler para trabajar con los datos

### 17.6 Repositorios de Feature Store

Feature Store mantiene dos tipos de repositorios:

1. **Repositorio online**:
   - Almacenamiento en tiempo real de características
   - Se accede mediante la API `GetRecord`
   - Ideal para modelos que necesitan acceso rápido a características

2. **Repositorio offline**:
   - Almacenamiento en S3 para acceso por lotes
   - Permite análisis y procesamiento más complejo
   - Se puede alimentar directamente si no se necesita streaming

### 17.7 Seguridad en Feature Store

Feature Store proporciona varias capas de seguridad:

- Cifrado automático de datos en reposo y en tránsito
- Compatibilidad con claves maestras de cliente de AWS KMS
- Control de acceso detallado a través de IAM
- Posibilidad de asegurar el acceso mediante AWS PrivateLink

## 18. SageMaker Canvas

### 18.1 Visión general

SageMaker Canvas es un entorno de machine learning sin código diseñado principalmente para analistas de negocio. Este tema aparecerá seguramente una o dos veces en el examen AWS Machine Learning - Specialty.

La idea principal es que permite crear modelos de machine learning sin necesidad de escribir código o tener conocimientos profundos en la materia. Es suficiente con proporcionar datos en formato CSV, indicar la columna para la que quieres hacer predicciones, y el sistema se encarga del resto utilizando técnicas de AutoML.

### 18.2 Capacidades principales

SageMaker Canvas ofrece diversas funcionalidades:

- **Unión de conjuntos de datos**: Permite combinar diferentes fuentes de datos
- **Predicciones**: Puede realizar clasificación o regresión automáticamente
- **Limpieza automática de datos**: Detecta y maneja valores faltantes, outliers y filas duplicadas sin intervención manual
- **Machine learning para no expertos**: Diseñado para ser utilizado por personas sin formación técnica en machine learning

### 18.3 Integración con SageMaker Studio

Una característica importante es que se integra con SageMaker Studio:

- Permite compartir modelos y conjuntos de datos con SageMaker Studio
- Posibilita exportar modelos y datos de entrenamiento desde Canvas a SageMaker Studio
- Facilita la colaboración entre analistas de negocio e ingenieros de machine learning

### 18.4 Soporte para modelos de IA generativa

SageMaker Canvas ha evolucionado más allá del machine learning tradicional:

- Incorpora soporte para modelos de IA generativa
- Compatible con los modelos foundation disponibles en Amazon Bedrock y a través de Amazon SageMaker JumpStart
- Permite el fine-tuning de estos modelos directamente desde la interfaz de Canvas
- No requiere ser un experto en IA o machine learning para crear aplicaciones de IA generativa
- Facilita la personalización de modelos foundation con datos propios

En resumen, SageMaker Canvas representa "IA y machine learning para todos", permitiendo que personas sin experiencia técnica aprovechen estas tecnologías de manera intuitiva y efectiva.

## 19. AWS Glue

### 19.1 Introducción a AWS Glue

AWS Glue es un sistema para construir definiciones de tablas y realizar ETL (Extract, Transform, Load - Extracción, Transformación y Carga) de tus datos automáticamente. Es un tema muy importante en el examen AWS Machine Learning - Specialty, ya que ocupa un lugar destacado desde que se trasladó el contenido de machine learning del examen AWS Big Data a su propio examen.

No es necesario conocer todos los detalles de Glue, pero sí es fundamental entender cómo conecta diferentes servicios y cómo se utiliza como parte de aplicaciones más grandes.

### 19.2 Características principales

AWS Glue es un sistema serverless con varias características clave:

- No requiere mantenimiento de infraestructura
- Maneja automáticamente el descubrimiento y definición de tablas y esquemas
- Sirve como repositorio central de metadatos para tu data lake
- Descubre esquemas a partir de datos no estructurados en S3
- Publica definiciones de tablas para usar con herramientas de análisis como:
  - Amazon Athena
  - Amazon Redshift
  - Amazon EMR
  - Otras bases de datos SQL

### 19.3 Funcionalidades de AWS Glue

#### 19.3.1 Descubrimiento de esquemas
El propósito principal de Glue es extraer estructura de tus datos no estructurados. Si tienes datos en un data lake en S3, Glue puede proporcionar un esquema para que puedas consultarlos usando SQL o herramientas similares.

#### 19.3.2 Trabajos ETL personalizados
Además de descubrir esquemas, Glue puede procesar tus datos mediante trabajos ETL:
- Pueden ejecutarse en respuesta a eventos (cuando se reciben datos)
- Pueden programarse para ejecutarse periódicamente
- Pueden ejecutarse bajo demanda
- Utilizan Apache Spark internamente
- Son totalmente gestionados (no necesitas mantener un cluster de Spark)

### 19.4 Glue Crawler y Data Catalog

#### 19.4.1 Glue Crawler
- Su propósito es escanear datos en S3
- Infiere esquemas automáticamente basados en los datos
- A veces necesita algunas pistas para funcionar correctamente
- Puede programarse para ejecutarse periódicamente

#### 19.4.2 Glue Data Catalog
- Se alimenta con la información que recoge el Crawler
- Contiene definiciones de tablas (qué columnas existen, tipos de datos, nombres de columnas)
- Incluye información sobre la ubicación de los datos
- Permite que servicios como Redshift, Athena o Hive (en EMR) consulten datos no estructurados en S3 como si fueran datos estructurados

Es importante destacar que los datos originales permanecen donde estaban (generalmente en S3). No se duplican ni se copian a una tabla de base de datos real. Glue simplemente proporciona la "conexión" (el "pegamento" o "glue" en inglés) entre las interfaces de bases de datos relacionales y el data lake no estructurado en S3.

Una vez catalogados, los datos están inmediatamente disponibles para análisis, y pueden visualizarse con herramientas como Amazon QuickSight.

### 19.5 Particiones en S3 y Glue

El trabajo con Glue requiere cierta planificación en la organización de tus datos no estructurados:

- Glue Crawler extraerá particiones basadas en cómo están organizados tus datos en S3
- Debes pensar anticipadamente sobre cómo consultarás tu data lake
- La estructura de organización afecta el rendimiento de las consultas

#### 19.5.1 Ejemplo de particionamiento

Imagina que tienes dispositivos que envían datos de sensores a tu sistema cada hora:

**Si consultas principalmente por rangos de tiempo:**
- Organiza tus buckets S3 como: año/mes/día/dispositivo
- Esto permite acceder eficientemente a datos de una fecha específica sin tener que procesar información de otras fechas

**Si consultas principalmente por dispositivo:**
- Organiza tus buckets S3 como: dispositivo/año/mes/día
- Esto es más eficiente cuando necesitas consultar datos de dispositivos específicos constantemente

En el examen, podrías encontrar preguntas sobre la forma óptima de organizar tus datos en S3 para que Glue pueda extraerlos y presentarlos como particiones adecuadas para tu caso de uso específico.

## 20. AWS Glue Studio

### 20.1 Introducción a AWS Glue Studio

AWS Glue Studio es una característica relativamente nueva en AWS Glue. Aunque es posible que aún no aparezca en el examen AWS Machine Learning - Specialty, es probable que se incluya pronto debido a la gran inversión que AWS está realizando en esta tecnología.

Lo principal que necesitas saber para el examen es qué es AWS Glue Studio y para qué se utiliza.

### 20.2 Propósito y funcionalidades

AWS Glue Studio es una interfaz visual para definir y crear flujos de trabajo ETL, permitiendo:

- Crear flujos de trabajo complejos sin escribir código
- Configurar procesos paralelos desde la misma fuente
- Procesar diferentes fuentes en paralelo de distintas maneras
- Establecer trabajos complejos de forma visual

### 20.3 Componentes principales

#### 20.3.1 Editor visual de trabajos
- Permite crear "directed acyclic graphs" (DAGs) para flujos de trabajo complejos
- Soporta diversas fuentes de datos:
  - Amazon S3
  - Amazon Kinesis
  - Apache Kafka
  - Cualquier fuente JDBC

#### 20.3.2 Transformaciones gráficas
Desde la interfaz de usuario de Glue Studio, puedes configurar gráficamente:
- Transformaciones de datos
- Muestreo de datos
- Uniones de tablas
- Filtrado de datos
- Mapeo y renombrado de campos
- División de datos

Los resultados de estas transformaciones pueden enviarse a S3 o al Glue Data Catalog.

#### 20.3.3 Particionamiento automático
AWS Glue Studio soporta particionamiento de forma nativa:
- Detecta automáticamente datos particionados
- Acelera el procesamiento en función de las particiones

#### 20.3.4 Panel de control visual
Proporciona un dashboard para:
- Visualizar el estado de tus trabajos ETL
- Monitorear qué trabajos están en ejecución
- Ver cuánto tiempo llevan ejecutándose
- Facilitar la solución de problemas
- Asegurar que los trabajos se ejecutan según lo esperado

### 20.4 Funcionamiento de AWS Glue Studio

AWS Glue Studio ofrece una experiencia de usuario intuitiva:

1. **Creación de trabajos**:
   - Se accede desde la consola de AWS Glue
   - Se selecciona "Create and Manage Jobs"
   - Se elige la fuente de datos (S3, RDS, Redshift, Kinesis, Kafka)
   - Se selecciona el destino de los resultados (S3 o Glue Data Catalog)

2. **Configuración del flujo de trabajo**:
   - Se muestra una representación gráfica del flujo de datos
   - Se pueden configurar transformaciones intermedias
   - Es posible crear flujos complejos en forma de árbol (DAG)
   - Se pueden aplicar múltiples transformaciones en paralelo

3. **Tipos de transformaciones disponibles**:
   - Mapeo
   - Eliminar campos
   - Renombrar campos
   - Unir tablas
   - Dividir datos
   - Filtrar registros
   - Transformaciones personalizadas con código

4. **Monitoreo de trabajos**:
   - Dashboard de resumen que muestra:
     - Cantidad de trabajos en ejecución
     - Trabajos exitosos y fallidos
     - Tasas de éxito
     - Uso de recursos
     - Tipos de trabajos en ejecución
     - Línea de tiempo de ejecución

Este panel facilita el mantenimiento, la supervisión y la solución de problemas de los trabajos ETL.

### 20.5 Relación con Apache Spark

Es importante notar que la estructura de DAG (Directed Acyclic Graph) en Glue Studio no es coincidencia:
- AWS Glue ETL utiliza Apache Spark internamente
- Los DAGs son una forma natural de representar flujos de trabajo en Spark
- Esta estructura permite el procesamiento paralelo y eficiente de datos

## 21. AWS Glue Data Quality

### 21.1 Introducción a AWS Glue Data Quality

AWS Glue Data Quality es una característica reciente de AWS Glue Studio que permite evaluar automáticamente la calidad de los datos entrantes dentro de un trabajo de Glue. Si los datos violan ciertos parámetros o reglas que configures, puede automáticamente fallar el trabajo o simplemente registrar un evento en CloudWatch.

A partir de 2024, es probable que este tema forme parte del examen AWS Machine Learning - Specialty.

### 21.2 Creación de reglas de calidad de datos

Existen dos formas principales de crear reglas de calidad de datos:

#### 21.2.1 Creación manual de reglas
- Defines tus propias reglas basadas en el conocimiento de tus datos
- Utilizas el lenguaje DQDL (Data Quality Definition Language)

#### 21.2.2 Creación automática de reglas
- Puedes indicar a Glue Data Quality que examine tus datos fuente que sabes que son "buenos"
- El sistema infiere automáticamente reglas basadas en:
  - Rangos esperados de los datos
  - Desviación estándar de valores típicos
  - Patrones de datos normales
- Puedes editar las reglas inferidas si no estás de acuerdo con lo que el sistema generó

### 21.3 Integración con trabajos de Glue

Una vez configuradas las reglas de calidad de datos, puedes integrarlas directamente en tus trabajos de Glue:
- Funcionan como otra etapa de transformación dentro del flujo de trabajo
- Pueden colocarse en cualquier punto del proceso ETL
- Se pueden aplicar a datos intermedios o finales

### 21.4 Acciones de respuesta a problemas de calidad

Cuando se detectan problemas de calidad de datos, puedes configurar dos tipos de respuestas:

#### 21.4.1 Fallo del trabajo
- Si una regla de calidad no se cumple, el trabajo completo puede fallar
- Esto puede generar falsos positivos si los umbrales están demasiado ajustados

#### 21.4.2 Registro en CloudWatch
- Alternativa menos disruptiva
- Reporta los resultados a CloudWatch
- Te permite interpretar los problemas y actuar según consideres apropiado

### 21.5 Lenguaje DQDL y ejemplos

El Data Quality Definition Language (DQDL) se utiliza para definir las reglas de calidad:

- **Verificación de recuento de filas**: Asegurar que el número de filas está dentro de un rango esperado
  ```
  RowCount between min_value and max_value
  ```

- **Completitud de columnas**: Verificar que ciertas columnas contienen datos completos
  ```
  Completeness "column_name" > threshold_value
  ```

- **Longitud de columnas**: Comprobar que la longitud de ciertos campos está dentro de rangos esperados
  ```
  ColumnLength "column_name" between min_length and max_length
  ```

- **Desviación estándar**: Validar que los valores no se desvían demasiado de lo normal
  ```
  StandardDeviation "column_name" between min_std and max_std
  ```

### 21.6 Visualización de resultados

Después de ejecutar un trabajo con reglas de calidad de datos:
- Puedes ver los resultados directamente en la interfaz de Glue Studio
- Se muestra qué reglas han pasado o fallado
- Se proporcionan detalles sobre las violaciones específicas

Por ejemplo, una regla podría fallar porque:
- La desviación estándar en un campo de fecha está fuera de lo esperado
- Los valores de columna vistos no coinciden con patrones históricos

Estos resultados pueden indicar:
- Umbrales demasiado restrictivos (falsos positivos)
- Problemas reales con los datos
- Outliers que podrían afectar análisis posteriores

AWS Glue Data Quality es una herramienta valiosa para garantizar que los datos que fluyen a través de tus pipelines ETL mantienen la calidad necesaria para análisis y machine learning efectivos.


## 22. AWS Glue DataBrew

### 22.1 Introducción a AWS Glue DataBrew

AWS Glue DataBrew es una herramienta visual de preparación de datos que definitivamente necesitas conocer en el campo de la ingeniería de datos y para el examen AWS Machine Learning - Specialty. Es una interfaz de usuario específicamente diseñada para transformar datos, representando la "T" (Transform) de tu proceso ETL.

### 22.2 Características principales

DataBrew es una interfaz de usuario para el preprocesamiento de grandes conjuntos de datos que ofrece:

- **Fuentes de datos versátiles**: Los datos pueden provenir de:
  - Amazon S3
  - Data warehouses
  - Cualquier base de datos que desees

- **Destino de salida**: Tras ser transformados por DataBrew, los datos se envían a S3

- **Transformaciones predefinidas**: Más de 250 transformaciones listas para usar que puedes aplicar visualmente a tus datos
  - Puedes simplemente arrastrar y soltar estas transformaciones en DataBrew
  - Prácticamente cualquier transformación que puedas imaginar probablemente esté incluida

### 22.3 Recetas y trabajos

DataBrew permite crear "recetas" de transformaciones que pueden guardarse como trabajos dentro de un proyecto más grande:

- **Recetas**: Conjuntos de transformaciones que quieres aplicar a tus datos
  - Pueden guardarse y reutilizarse
  - Pueden aplicarse a diferentes proyectos y conjuntos de datos

- **Acciones de receta**: Las recetas están compuestas por acciones individuales
  - Cada acción representa una transformación específica
  - La documentación de AWS detalla todas estas acciones de receta

### 22.4 Ejemplo de acción de receta: Nest to Map

Un ejemplo concreto de acción de receta es "Nest to Map":

- **Propósito**: Toma columnas seleccionadas y las convierte en pares clave-valor
  - En lugar de columnas individuales, convierte los datos en un documento JSON con mapeos clave-valor
  
- **Parámetros**:
  - **Source columns**: Especifica qué columnas quieres anidar
    - Ejemplo: "age", "weight_kilogram", "height_centimeter"
  - **Column name**: El nombre de la nueva columna que contendrá el mapa
  - **Remove source columns**: Opción para eliminar las columnas originales después de la transformación

- **Resultado**: 
  - Una nueva columna con un mapa JSON que contiene todos los valores
  - Por ejemplo: `{"age": 53, "weight_kilogram": 100, "height_centimeter": 180}`

### 22.5 Características adicionales

DataBrew ofrece otras funcionalidades importantes:

- **Reglas de calidad de datos**: Define reglas para asegurar que tus datos tienen sentido durante el proceso
- **Conjuntos de datos personalizados**: Crea conjuntos de datos utilizando SQL personalizado desde:
  - Amazon Redshift
  - Snowflake

### 22.6 Seguridad en DataBrew

AWS Glue DataBrew incorpora varias medidas de seguridad:

- **Integración con KMS**: Utiliza exclusivamente claves maestras del cliente
- **SSL en tránsito**: Para proteger los datos durante la transmisión
- **IAM**: Para restringir quién puede hacer qué acciones
- **Integración con servicios de monitoreo**:
  - CloudWatch: Para alarmas y monitoreo
  - CloudTrail: Para auditoría y gobernanza

## 23. Handling PII in DataBrew Transformations

### 23.1 Técnicas para manejar PII

La guía del examen destaca el manejo de información de identificación personal (PII) en transformaciones DataBrew. Estas son las técnicas disponibles:

#### 23.1.1 Substitution
- Reemplazar PII con números aleatorios u otra información no identificable

#### 23.1.2 Shuffle
- Mezclar todos los valores PII entre diferentes filas
- Por ejemplo, asignar el número de tarjeta de crédito de la Persona A a la Persona B

#### 23.1.3 Deterministic Encryption
- Cifrado donde un valor dado siempre resulta en el mismo valor cifrado

#### 23.1.4 Probabilistic Encryption
- Sistema donde puede haber más de un resultado del cifrado para un campo dado

#### 23.1.5 Decrypt
- Permite descifrar información previamente cifrada

#### 23.1.6 Delete
- Posiblemente la mejor manera de manejar PII es eliminarla completamente
- Usar la transformación "Delete" para descartar esa información

#### 23.1.7 Masking
- Mostrar solo parte de la información (como los últimos cuatro dígitos de una tarjeta de crédito)
- El resto se enmascara o codifica

#### 23.1.8 Hashing
- Aplicar una función hash criptográfica al valor
- Múltiples valores pueden tener el mismo resultado hash
- Proporciona cierto anonimato

Estas son las transformaciones específicas en DataBrew para manejar información personalmente identificable.

## 24. Amazon Athena

### 24.1 Introducción a Amazon Athena

Amazon Athena es un motor de consulta interactivo serverless para datos almacenados en S3. Te permite consultar grandes data lakes en S3 como si fueran bases de datos SQL, sin necesidad de cargar los datos en un sistema intermedio.

### 24.2 Características principales

- **Motor de consulta interactivo para S3**: Proporciona una interfaz SQL para tus datos en S3
- **Sin carga de datos**: Los datos permanecen en S3; no es necesario cargarlos en una base de datos intermedia
- **Basado en Presto**: Aunque ha evolucionado significativamente desde sus orígenes
- **Totalmente serverless**: No necesitas gestionar la infraestructura subyacente
- **Facilidad de uso**: Simplemente accedes a la consola de Athena, apuntas a tus datos en S3 y empiezas a escribir consultas

### 24.3 Formatos de datos compatibles

Athena soporta múltiples formatos de datos:

#### 24.3.1 Formatos legibles por humanos
- CSV (valores separados por comas)
- TSV (valores separados por tabulaciones)
- JSON

#### 24.3.2 Formatos columnares y divisibles
- ORC (formato columnar optimizado)
- Parquet (formato columnar)

#### 24.3.3 Otros formatos divisibles
- Avro (divisible pero no columnar)

#### 24.3.4 Compresión
- Snappy
- Zlib
- LZ4
- GZIP

### 24.4 Puntos clave para el examen

- ORC y Parquet son formatos columnares y divisibles
- Avro es solo divisible (no columnar)
- CSV, TSV y JSON son formatos legibles por humanos
- Los formatos divisibles permiten procesamiento en paralelo
- Los formatos columnares aceleran consultas que solo necesitan columnas específicas

### 24.5 Tipos de datos compatibles

Athena puede trabajar con datos:
- No estructurados
- Semi-estructurados
- Estructurados

Puede inferir un esquema examinando los datos o utilizar un esquema que tú proporciones.

### 24.6 Casos de uso

- **Consultas ad hoc de logs web**: Alternativa preferida a Elasticsearch para consultar logs web en S3
- **Consulta de datos de staging**: Antes de cargarlos en Redshift
- **Análisis de logs diversos**: CloudTrail, CloudFront, VPC, Elastic Load Balancer, etc.
- **Integración con herramientas de análisis**: 
  - Notebooks (Jupyter, Zeppelin, RStudio)
  - Interfaces ODBC y JDBC
  - Amazon QuickSight para visualización

Athena actúa como conector entre los datos no estructurados en S3 y herramientas de visualización o análisis estructurados como QuickSight.

## 25. Athena y AWS Glue

### 25.1 Integración entre Athena y AWS Glue

Para impartir estructura a datos no estructurados en S3 y permitir que Athena los consulte como una base de datos, se utiliza AWS Glue:

- El **Glue Crawler** explora los datos en S3 y popula el **Glue Data Catalog**
- El Crawler extrae columnas y definiciones de tablas de los datos
- Se puede usar la consola de Glue para refinar esta definición según sea necesario

Una vez que se publica un Glue Data Catalog para los datos en S3:
- Athena lo ve automáticamente y construye una tabla a partir de él
- Esto permite consultar los datos con SQL, como cualquier otra base de datos

El Glue Data Catalog no es exclusivo para Athena, también puede ser utilizado por:
- Amazon RDS
- Amazon Redshift y Redshift Spectrum
- Amazon EMR
- Cualquier aplicación compatible con Apache Hive Metastore

Esta integración permite:
- Crear un repositorio unificado de metadatos entre varios servicios
- Descubrir esquemas mediante rastreo de datos
- Mantener versiones de esquemas
- Proporcionar una interfaz SQL a través de Athena

### 25.2 Athena Workgroups

Los Workgroups (grupos de trabajo) en Athena permiten:

- Organizar usuarios, equipos, aplicaciones y cargas de trabajo en diferentes grupos
- Controlar el acceso a consultas para cada grupo de trabajo
- Realizar seguimiento de costos por grupo de trabajo

Características principales:
- **Integración con IAM, CloudWatch y SNS**: Permite notificaciones cuando se alcanzan límites
- **Límites de datos**: Se puede limitar cuántos datos pueden escanear las consultas por workgroup
- **Historial de consultas individual**: Cada workgroup tiene su propio historial
- **Políticas IAM separadas**: Cada workgroup puede tener sus propias políticas de permisos
- **Configuración de cifrado individual**: Cada workgroup puede tener su propia configuración

Los workgroups son útiles para:
- Limitar el acceso de grupos específicos
- Medir costos de diferentes equipos
- Establecer límites sobre la cantidad de datos que pueden escanear las consultas

### 25.3 Optimización del rendimiento de Athena

Para optimizar el rendimiento de Athena, se recomiendan tres enfoques principales:

#### 25.3.1 Usar formatos de datos columnares
- **ORC o Parquet** ofrecen mejor rendimiento
- Se pueden usar herramientas como Glue para transformar datos a estos formatos
- Permiten a Athena leer selectivamente solo las columnas requeridas

#### 25.3.2 Tamaño y número de archivos
- Un pequeño número de archivos grandes funciona mejor que muchos archivos pequeños
- Los archivos columnares grandes ofrecen el mejor rendimiento

#### 25.3.3 Utilizar particiones
- Organizar datos en S3 en formato de partición que se alinee con los patrones de consulta
- Por ejemplo, particionar por fecha si las consultas suelen filtrar por rangos de fechas
- Para añadir particiones a bases de datos existentes, se puede usar el comando `MSCK REPAIR TABLE`

### 25.4 Soporte para transacciones ACID en Athena

Athena ofrece soporte para transacciones ACID (Atomicidad, Consistencia, Aislamiento, Durabilidad):

- Permite a usuarios concurrentes modificar las mismas filas simultáneamente de manera segura
- Utiliza **Apache Iceberg** bajo el capó
- Para activarlo, añadir `TABLE_TYPE = 'ICEBERG'` al crear una tabla en Athena
- Compatible con Elastic MapReduce, Apache Spark y cualquier sistema que soporte Apache Iceberg
- Elimina la necesidad de bloqueo personalizado de registros
- Ofrece operaciones de "time travel" para recuperar datos recientemente eliminados

Este es otro enfoque para lograr transacciones ACID, similar a las tablas gobernadas en Lake Formation:
- Se puede configurar una tabla gobernada en Lake Formation y acceder con Athena 
- O crear directamente una tabla en Athena con tipo Iceberg

Para mantener el rendimiento con tablas ACID:
- Es necesario compactar periódicamente los datos
- Se usa el comando: `OPTIMIZE table_name REWRITE DATA USING BIN_PACK WHERE catalog = 'catalog_name'`

### 25.5 Control de acceso detallado al Catálogo de Datos de Glue

Athena permite control de acceso basado en IAM a nivel de base de datos y tabla para operaciones específicas:

- Más amplio que los filtros de datos en Lake Formation (no es seguridad a nivel de celda, fila o columna)
- Se aplica a todas las versiones de tabla, no se puede restringir a versiones específicas

Requisitos mínimos:
- Política IAM para Athena que otorgue acceso a la base de datos
- Acceso al Glue Data Catalog en cada región

Se pueden restringir operaciones como:
- ALTER o CREATE DATABASE
- CREATE TABLE
- DROP DATABASE o DROP TABLE
- REPAIR TABLE
- SHOW DATABASES o SHOW TABLES

Para implementar estos controles, es necesario mapear operaciones a acciones IAM específicas. Por ejemplo, para restringir DROP TABLE se necesitan permisos para:
- Obtener la partición y particiones de la tabla
- Obtener la tabla y la base de datos
- Eliminar la tabla y la partición

### 25.6 Modelo de costos y seguridad

#### 25.6.1 Costos
- Pago por uso: $5 por terabyte escaneado
- Las consultas exitosas o canceladas cuentan para el volumen escaneado
- Las consultas fallidas no generan cargos
- Las operaciones DDL (CREATE, ALTER, DROP) son gratuitas
- Los formatos columnares pueden ahorrar entre 30-90% en costos
- La partición de datos también reduce costos al escanear menos datos

#### 25.6.2 Seguridad
Athena ofrece varias opciones de seguridad:
- **Control de acceso**: IAM, ACLs y políticas de bucket S3
- **Políticas IAM relevantes**: Amazon Athena Full Access y AWS QuickSight Athena Access
- **Cifrado de resultados** en S3:
  - SSE-S3: Cifrado del lado del servidor con clave administrada por S3
  - SSE-KMS: Cifrado del lado del servidor con clave KMS
  - CSE-KMS: Cifrado del lado del cliente con clave KMS
- **Acceso entre cuentas**: Definido en la política de bucket S3
- **Seguridad en tránsito**: TLS para todo el tráfico entre Athena y S3

### 25.7 Anti-patrones

Casos en los que no se recomienda usar Athena:
- Para informes altamente formateados o visualizaciones (usar QuickSight en su lugar)
- Para operaciones ETL (Extract, Transform, Load) - usar Glue ETL o Apache Spark en su lugar