# Data Ingestion and Storage (Ingesta y Almacenamiento de Datos)

## 1. Tipos de Datos
---

### 1.1 Datos Estructurados  
**Definición**:  
- Datos organizados en un esquema predefinido, típicamente almacenados en bases de datos relacionales.  
- Características:  
  - Organizados en **filas y columnas** con tipos de datos definidos (ej: números, fechas, texto).  
  - **Fácilmente consultables** con lenguajes como SQL.  
  - Estructura **consistente y predecible**.  

**Ejemplos**:  
- Tablas en bases de datos relacionales:  
  - **Amazon Redshift**, MySQL, PostgreSQL, Oracle.  
- Archivos CSV bien estructurados (con columnas consistentes).  
- Hojas de cálculo de Excel (si están organizadas en filas/columnas definidas).  

---

### 1.2 Datos No Estructurados  
**Definición**:  
- Datos sin un esquema predefinido. Requieren **preprocesamiento** para extraer significado.  

**Características**:  
- No se pueden consultar directamente sin procesamiento previo.  
- Formatos variados: texto plano, imágenes, audio, video, etc.  

**Ejemplos**:  
- Archivos de texto sin formato (ej: libros, posts de Reddit).  
- Archivos multimedia: imágenes, videos, audios.  
  - Requieren extraer metadatos (ej: duración, autor, fecha de creación).  
- Documentos de Word o emails con contenido textual sin organizar.  

---

### 1.3 Datos Semi-Estructurados  
**Definición**:  
- Datos con **cierto nivel de estructura** (etiquetas, jerarquías o patrones), pero sin esquema fijo.  

**Características**:  
- Más flexible que los datos estructurados, pero menos caótico que los no estructurados.  
- Permite variaciones en el formato, pero incluye **marcas o etiquetas** para inferir la estructura.  

**Ejemplos**:  
- Archivos **JSON/XML**:  
  ```json
  {
    "nombre": "Ejemplo",
    "detalles": {
      "id": 123,
      "activo": true
    }
  }


## 2. Propiedades de los Datos (3 V's)  
**Definición**:  
- Las **3 V's** (Volume, Velocity, Variety) son características clave para entender los desafíos del manejo de datos en ingeniería.  
- **Veracidad** (4ta V) existe en otros contextos, pero el examen se enfoca en las tres principales.  

---

### 2.1 Volumen  
**Definición**:  
- Cantidad o tamaño de los datos a manejar (gigabytes, petabytes, etc.).  
- **Impacto**:  
  - Influencia en decisiones de almacenamiento y procesamiento.  
  - Ejemplo: ¿Subir datos por internet o usar **AWS Snowmobile** para transferencia física?  

**Ejemplos**:  
- Plataformas de redes sociales: Terabytes diarios de imágenes, videos y texto.  
- Minoristas grandes: Petabytes de datos transaccionales históricos.  

**Relevancia en AWS**:  
- Servicios como **S3** y **Redshift** escalan para manejar grandes volúmenes.  
- Sistemas distribuidos (ej: **EMR**) procesan datos masivos en paralelo.  

---

### 2.2 Velocidad  
**Definición**:  
- Velocidad a la que se generan, recolectan y procesan los datos.  
- **Impacto**:  
  - Decisión entre procesamiento **batch** (por lotes) o **streaming** (tiempo real).  
  - Ejemplo: Kinesis Data Streams vs. Kinesis Data Firehose.  

**Ejemplos**:  
- Sensores IoT: Lecturas cada milisegundo.  
- Sistemas de trading de alta frecuencia: Procesamiento en milisegundos para órdenes bursátiles.  

**Relevancia en AWS**:  
- **Kinesis** gestiona datos en tiempo real.  
- **Lambda** procesa eventos en respuesta a flujos de datos.  

---

### 2.3 Variedad  
**Definición**:  
- Tipos de datos (estructurados, semi-estructurados, no estructurados) y sus fuentes.  
- **Impacto**:  
  - Necesidad de soluciones unificadas para consultar datos heterogéneos.  

**Ejemplos**:  
- Empresa que combina:  
  - Bases de datos relacionales (estructurados).  
  - Emails (no estructurados).  
  - Logs en JSON (semi-estructurados).  
- Sistemas de salud: Registros médicos, datos de dispositivos y formularios de pacientes.  

**Relevancia en AWS**:  
- **S3** como repositorio central para datos de cualquier formato.  
- **Athena** consulta datos semi-estructurados directamente en S3.  

---

### 2.4 Veracidad (Opcional)  
**Definición**:  
- Grado de confianza en la calidad y exactitud de los datos.  
- **Nota**: No es enfatizado en el examen, pero útil conocer su existencia.  


## 3. Data Warehouse vs. Data Lake  
**Definición**:  
- **Data Warehouse**: Repositorio centralizado optimizado para análisis, con datos estructurados y procesados.  
- **Data Lake**: Almacén de datos en bruto (raw) en formatos nativos (estructurados, semi-estructurados, no estructurados).  

---

### 3.1 Data Warehouse  
**Características**:  
- **Esquema predefinido (schema on write)**: Los datos se transforman antes de almacenarse.  
- **Proceso ETL**: Extract, Transform, Load (ej: limpieza, integración).  
- **Optimizado para consultas complejas**: Usado en Business Intelligence (BI) y análisis estructurado.  
- **Ejemplos en AWS**:  
  - **Amazon Redshift** (data warehouse).  
  - Esquemas en estrella o copo de nieve.  

**Casos de uso**:  
- Análisis de ventas históricas.  
- Reportes financieros.  
- Integración de datos de múltiples fuentes (ej: catálogos, clickstream, compras).  

---

### 3.2 Data Lake  
**Características**:  
- **Datos en bruto (raw)**: Almacenados en su formato original (CSV, JSON, imágenes, logs, etc.).  
- **Esquema flexible (schema on read)**: Los datos se transforman al leerse, no al almacenarse.  
- **Proceso ELT**: Extract, Load, Transform (ej: AWS Glue + Athena).  
- **Costo eficiente**: Almacenamiento en **Amazon S3** a bajo costo.  

**Casos de uso**:  
- Almacenamiento masivo de logs.  
- Machine Learning con datos no estructurados.  
- Exploración de datos sin un propósito definido.  

---

### 3.3 Comparación Clave  
| **Criterio**       | **Data Warehouse**                     | **Data Lake**                          |
|---------------------|----------------------------------------|----------------------------------------|
| **Estructura**      | Datos estructurados (ETL)              | Datos en bruto (ELT)                   |
| **Esquema**         | Predefinido (schema on write)          | Flexible (schema on read)              |
| **Costo**           | Alto (procesamiento complejo)          | Bajo (almacenamiento en S3)            |
| **Agilidad**        | Menos flexible (cambios costosos)      | Más flexible (transformación dinámica) |
| **Uso principal**   | BI, reportes, análisis estructurado     | Machine Learning, análisis exploratorio |

---

### 3.4 Data Lake House (Híbrido)  
**Definición**:  
- Combina ventajas de ambos: flexibilidad del data lake + rendimiento del data warehouse.  
- **Ejemplo en AWS**:  
  - **AWS Lake Formation** + **S3** + **Redshift Spectrum**.  
  - Consulta datos en S3 como si estuvieran en un data warehouse.  

**Características**:  
- Soporta datos estructurados y no estructurados.  
- Transacciones ACID (ej: Delta Lake en AWS).  
- Ideal para análisis avanzado y Machine Learning.  


## 4. Data Mesh  
**Definición**:  
- Paradigma de gestión de datos **descentralizado**, centrado en la **organización y gobernanza** en lugar de tecnologías específicas.  
- Enfoque en **dominios de datos** (equipos responsables de sus propios datos) y **productos de datos** accesibles de forma autónoma.  

---

### 4.1 Conceptos Clave  
1. **Dominio de Datos**:  
   - Equipos o departamentos son dueños de sus datos (ej: equipo de ventas mantiene sus propios datos de clientes).  
   - Responsables de la calidad, seguridad y documentación de sus datos.  

2. **Producto de Datos**:  
   - Los datos se exponen como "productos" reutilizables para otros equipos.  
   - Ejemplo: Un API que entrega datos procesados de ventas para análisis transversal.  

3. **Gobernanza Federada**:  
   - Estándares centrales (ej: seguridad, acceso) aplicados por cada dominio.  
   - **AWS Lake Formation** puede gestionar permisos y políticas de acceso.  

4. **Infraestructura de Autoservicio**:  
   - Herramientas como **AWS Glue** (catálogo de datos) y **S3** (almacenamiento) permiten a los equipos crear y consumir productos de datos sin dependencias.  

---

### 4.2 Ejemplo en AWS  
- **Almacenamiento**: Datos en **S3** organizados por dominios.  
- **Gobernanza**: **Lake Formation** define quién accede a qué datos.  
- **Descubrimiento**: **AWS Glue Data Catalog** lista productos de datos disponibles.  
- **Consulta**: **Athena** o **Redshift Spectrum** analizan datos sin moverlos.  

---

### 4.3 Relevancia para el Examen  
- **Enfoque**:  
  - No se trata de tecnologías específicas, sino de **principios organizacionales**.  
  - Preguntas sobre diferencias entre **Data Mesh**, Data Warehouse y Data Lake.  
- **Palabras clave**:  
  - Dominio de datos, producto de datos, gobernanza federada, autoservicio.  



## 5. ETL Pipelines  
**Definición**:  
- **ETL** (Extract, Transform, Load): Proceso para extraer datos, transformarlos y cargarlos en un destino (ej: data warehouse).  
- **ELT** (Extract, Load, Transform): Variante usada en data lakes, donde la transformación ocurre después de la carga.  

---

### 5.1 Extract (Extracción)  
**Definición**:  
- Recuperar datos desde fuentes originales (bases de datos, APIs, archivos, sistemas como Salesforce).  

**Desafíos**:  
- Garantizar **integridad de datos** durante la extracción.  
- Manejar fallos (ej: reintentos en APIs).  
- Velocidad de extracción (batch vs. streaming).  

**Ejemplos**:  
- Logs de servidores.  
- Datos de CRM (Salesforce).  
- Archivos CSV/JSON desde sistemas locales.  

---

### 5.2 Transform (Transformación)  
**Definición**:  
- Convertir datos crudos en un formato adecuado para análisis.  

**Acciones comunes**:  
- **Limpieza**: Eliminar duplicados, corregir errores.  
- **Enriquecimiento**: Combinar datos de múltiples fuentes.  
- **Formato**: Convertir fechas, codificar texto, normalizar valores.  
- **Agregaciones**: Calcular totales, promedios, etc.  
- **Manejo de datos faltantes**: Imputar valores o eliminar registros.  

**Ejemplo**:  
- Convertir fechas en formato `YYYY-MM-DD` a tipo `datetime`.  

---

### 5.3 Load (Carga)  
**Definición**:  
- Almacenar datos transformados en el destino final (ej: **Amazon Redshift** para ETL, **S3** para ELT).  

**Desafíos**:  
- Garantizar **integridad durante la escritura**.  
- Manejar volúmenes masivos (batch vs. streaming).  

**Ejemplos**:  
- Carga incremental diaria en un data warehouse.  
- Almacenamiento en S3 para procesamiento futuro (ELT).  

---

### 5.4 Orquestación de Pipelines  
**Herramientas en AWS**:  
- **AWS Glue**: Automatiza ETL/ELT con jobs serverless.  
- **Step Functions**: Orquesta flujos de trabajo complejos.  
- **Lambda**: Ejecuta transformaciones en respuesta a eventos.  
- **Amazon MWAA**: Managed Workflows for Apache Airflow.  

**Funciones clave**:  
- Automatizar secuencias (ej: extraer → transformar → cargar).  
- Monitorear fallos y reintentos.  
- Escalar según volumen de datos.  

---

### 5.5 ETL vs. ELT  
| **Criterio**       | **ETL**                          | **ELT**                          |
|---------------------|----------------------------------|----------------------------------|
| **Uso**             | Data warehouses (datos estructurados) | Data lakes (datos raw)          |
| **Transformación**  | Antes de cargar                  | Después de cargar                |
| **Flexibilidad**    | Menor (esquema fijo)            | Mayor (esquema dinámico)         |
| **Costo**           | Alto (procesamiento complejo)   | Bajo (almacenamiento en S3)      |

---

### 5.6 Relevancia en AWS  
- **ETL**:  
  - **Redshift**: Carga datos transformados para análisis.  
  - **Glue**: Jobs ETL con PySpark o Scala.  
- **ELT**:  
  - **S3 + Athena**: Consulta datos raw con SQL.  
  - **Glue Data Catalog**: Cataloga metadatos para ELT.  

**Palabras clave**:  
- ETL, ELT, AWS Glue, Step Functions, integridad de datos, transformación, carga incremental.  


## 6. Fuentes de Datos y Formatos  
**Definición**:  
- **Fuentes de datos**: Sistemas o ubicaciones desde donde se extraen los datos.  
- **Formatos**: Estructura en la que se almacenan los datos (ej: CSV, JSON, Avro, Parquet).  

---

### 6.1 Fuentes de Datos Comunes  
#### **6.1.1 JDBC (Java Database Connectivity)**  
- **Definición**: Interfaz basada en Java para conectarse a bases de datos.  
- **Características**:  
  - Independiente de la plataforma (usa Java).  
  - Requiere código en Java para su uso directo.  
- **Ejemplos**:  
  - Conexión a bases de datos relacionales (MySQL, PostgreSQL).  

#### **6.1.2 ODBC (Open Database Connectivity)**  
- **Definición**: Estándar para conectarse a bases de datos sin depender de lenguajes específicos.  
- **Características**:  
  - Requiere controladores específicos para cada sistema.  
  - Compatible con múltiples lenguajes (Python, C++, etc.).  

#### **6.1.3 Archivos de Logs**  
- **Definición**: Registros de eventos o actividades almacenados en texto plano.  
- **Ejemplos**:  
  - Logs de servidores en **Amazon S3**.  
  - Logs de aplicaciones (Apache, Nginx).  

#### **6.1.4 APIs**  
- **Definición**: Interfaces para extraer datos desde servicios externos.  
- **Ejemplos**:  
  - APIs REST para integrar datos de CRM (Salesforce).  
  - APIs de redes sociales (Twitter, Facebook).  

#### **6.1.5 Streaming (Kafka/Kinesis)**  
- **Definición**: Flujo continuo de datos en tiempo real.  
- **AWS Services**:  
  - **Amazon Kinesis**: Procesamiento de streams.  
  - **Lambda**: Respuesta a eventos en tiempo real.  

---

### 6.2 Formatos de Datos  
#### **6.2.1 CSV (Comma-Separated Values)**  
- **Definición**: Formato de texto delimitado por comas (u otros caracteres).  
- **Características**:  
  - Legible por humanos.  
  - Adecuado para datos tabulares pequeños/medianos.  
- **Usos**:  
  - Intercambio de datos entre sistemas.  
  - Importación/exportación desde hojas de cálculo (Excel).  
- **Ejemplos**:  
  - Datos transaccionales, catálogos de productos.  

#### **6.2.2 JSON (JavaScript Object Notation)**  
- **Definición**: Formato basado en pares clave-valor para datos semi-estructurados.  
- **Características**:  
  - Soporta estructuras anidadas y esquemas flexibles.  
  - Legible por humanos y máquinas.  
- **Usos**:  
  - APIs RESTful (comunicación entre servicios).  
  - Almacenamiento en bases de datos NoSQL (MongoDB).  

#### **6.2.3 Avro**  
- **Definición**: Formato binario que incluye esquema y datos.  
- **Características**:  
  - Eficiente para sistemas distribuidos.  
  - Ideal si el esquema cambia con frecuencia.  
- **Usos**:  
  - Procesamiento en tiempo real (**Apache Kafka**, **Spark**).  
  - Serialización de datos entre sistemas.  

#### **6.2.4 Parquet**  
- **Definición**: Formato columnar optimizado para análisis.  
- **Características**:  
  - Almacena datos por columnas (no por filas).  
  - Compresión eficiente y lectura rápida para consultas específicas.  
- **Usos**:  
  - Análisis de big data (**AWS Redshift Spectrum**, **Apache Spark**).  
  - Almacenamiento en data lakes para consultas complejas.  

---

### 6.3 Comparación de Formatos  
| **Formato** | **Tipo**       | **Legible** | **Esquema**       | **Uso Principal**                |  
|-------------|----------------|-------------|-------------------|-----------------------------------|  
| CSV         | Texto          | Sí          | Fijo              | Intercambio de datos simples      |  
| JSON        | Texto          | Sí          | Flexible          | APIs y datos semi-estructurados   |  
| Avro        | Binario        | No          | Embebido          | Streaming y esquemas dinámicos    |  
| Parquet     | Columnar       | No          | Predefinido       | Análisis de big data              |  

---

### 6.4 Relevancia en AWS  
- **CSV/JSON**: Usados en **S3** para almacenamiento y **Athena** para consultas.  
- **Parquet**: Optimizado para **Redshift Spectrum** y **EMR**.  
- **Avro**: Común en pipelines con **Kinesis** y **Glue**.  


## 8. Seguridad en Amazon S3  
---

### 8.1 Seguridad Basada en Usuarios  
**Definición**:  
- Controla el acceso mediante **políticas IAM** asociadas a usuarios, grupos o roles.  

**Características**:  
- Define permisos para **llamadas API específicas** (ej: `s3:GetObject`, `s3:PutObject`).  
- Ejemplo: Un usuario IAM con permisos para listar objetos en un bucket.  

---

### 8.2 Seguridad Basada en Recursos  
**Definición**:  
- Reglas aplicadas directamente a los recursos (ej: buckets S3).  

**Componentes**:  
1. **Políticas de Bucket (S3 Bucket Policies)**:  
   - JSON que define permisos a nivel de bucket.  
   - Ejemplo: Permitir acceso público o acceso cruzado entre cuentas.  
2. **Listas de Control de Acceso (ACL)**:  
   - **Object ACL**: Control fino por objeto (puede deshabilitarse).  
   - **Bucket ACL**: Control a nivel de bucket (poco común).  

---

### 8.3 Políticas de Bucket (S3 Bucket Policies)  
**Estructura**:  
  ```json
  {
    "Version": "2012-10-17",
    "Statement": [
      {
        "Effect": "Allow",
        "Principal": "*",
        "Action": "s3:GetObject",
        "Resource": "arn:aws:s3:::mi-bucket/*"
      }
    ]
  }


## 9. Versionado de Objetos en S3  
---

### 9.1 ¿Qué es el Versionado?  
**Definición**:  
- Funcionalidad que permite **guardar múltiples versiones** de un objeto en un bucket.  
- Debe habilitarse **a nivel de bucket**.  

**Cómo funciona**:  
- Al subir un archivo con la misma clave (nombre), S3 crea una **nueva versión** en lugar de sobrescribir la existente.  
- Ejemplo:  
  - `archivo.txt` (versión 1) → Nueva subida → `archivo.txt` (versión 2).  

---

### 9.2 Beneficios del Versionado  
1. **Protección contra borrados accidentales**:  
   - Al eliminar un objeto, S3 añade un **marcador de eliminación** en lugar de borrarlo físicamente.  
   - Permite restaurar versiones anteriores.  

2. **Rollback sencillo**:  
   - Recuperar fácilmente una versión anterior (ej: revertir cambios no deseados).  

---

### 9.3 Consideraciones Importantes  
- **Archivos pre-versionado**:  
  - Los objetos creados **antes de habilitar el versionado** tienen `versionId: null`.  

- **Suspender versionado**:  
  - No elimina versiones existentes, solo detiene la creación de nuevas versiones.  

---

### 9.4 Ejemplo Práctico: Habilitar Versionado  
**Pasos en la consola AWS**:  
1. Ir al bucket en S3.  
2. En la pestaña **Properties**, buscar **Bucket Versioning**.  
3. Habilitar **Enable versioning**.  

**Resultado**:  
- Cada nueva subida/edición generará una versión identificada con un `versionId` único.  

---

### 9.5 Caso de Uso: Restaurar una Versión Anterior  
**Pasos**:  
1. En la consola S3, navegar al objeto versionado.  
2. Seleccionar **"Show versions"** para ver todas las versiones.  
3. Elegir la versión deseada y descargarla o restaurarla como versión actual.  

**Nota**:  
- Las versiones anteriores **no se eliminan** al subir una nueva, a menos que se use `DELETE` explícitamente.  