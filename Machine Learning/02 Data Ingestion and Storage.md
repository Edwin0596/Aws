# Data Ingestion and Storage

## 1. Common Data Sources and Data Formats

En el mundo del Machine Learning, la forma en que adquirimos y almacenamos datos es fundamental para el éxito de nuestros modelos. Esta sección cubre las principales fuentes de datos y formatos que encontrarás en entornos AWS y que son importantes para el examen AWS Certified Machine Learning - Associate.

### 1.1. Fuentes de Datos Comunes

Existen diversas formas de conectarse a fuentes de datos y obtener información para nuestros proyectos de ML:

#### 1.1.1. Conexiones JDBC (Java Database Connectivity)
JDBC es una interfaz estándar que permite conectarse y consultar bases de datos externas desde aplicaciones Java. 

**¿Cómo funciona?** JDBC proporciona un conjunto de clases e interfaces que permiten enviar comandos SQL a bases de datos relacionales.

**Ventajas y limitaciones:**
- ✅ **Platform-independent**: Funciona en cualquier dispositivo que soporte Java
- ❌ **Language-dependent**: Limitado al uso del lenguaje Java

**¿Cuándo usarlo?** Es ideal cuando estás desarrollando herramientas de extracción de datos en Java y necesitas conectarte a diferentes bases de datos.

#### 1.1.2. Conexiones ODBC (Open Database Connectivity)
ODBC es una alternativa a JDBC que permite acceder a bases de datos desde cualquier lenguaje de programación.

**¿Cómo funciona?** ODBC proporciona una capa de abstracción que permite a aplicaciones conectarse a diferentes bases de datos usando drivers específicos.

**Ventajas y limitaciones:**
- ✅ **Language-independent**: Funciona con múltiples lenguajes de programación
- ❌ **Platform-dependent**: Requiere drivers específicos para cada plataforma

**Nota importante:** En la práctica, muchas herramientas de extracción de datos soportan tanto JDBC como ODBC, pero operan en capas internas ocultas para el usuario.

#### 1.1.3. Archivos de Registro (Log Files)
Los logs son registros que capturan eventos y actividades generados por sistemas y aplicaciones.

**¿Cómo funcionan?** Estos archivos se generan continuamente y pueden almacenarse en servicios como Amazon S3 para su posterior procesamiento.

**¿Cuándo usarlos?** Son útiles cuando necesitas analizar comportamientos, detectar errores o monitorear actividades de sistemas.

#### 1.1.4. APIs Externas (Application Programming Interfaces)
Las APIs son interfaces programáticas que permiten a diferentes aplicaciones comunicarse entre sí.

**¿Cómo funcionan?** Ofrecen puntos de acceso predefinidos mediante los cuales se pueden solicitar y recibir datos estructurados.

**¿Cuándo usarlas?** Cuando necesitas acceder a datos de servicios externos que no están disponibles a través de JDBC/ODBC.

#### 1.1.5. Streams de Datos
Los streams proporcionan flujos continuos de datos en tiempo real a medida que se generan.

**¿Cómo funcionan?** En lugar de procesar datos en lotes, los streams permiten procesar cada registro de datos tan pronto como está disponible.

**Ejemplos en AWS:**
- **Amazon Kinesis**: Servicio para recolectar, procesar y analizar datos en tiempo real
- **Apache Kafka**: Plataforma de streaming que puede integrarse con servicios AWS

**¿Cuándo usarlos?** Cuando necesitas procesar datos en tiempo real para análisis instantáneo, detección de anomalías o actualizaciones continuas de modelos.

### 1.2. Formatos de Datos Comunes

Los datos pueden almacenarse en diferentes formatos, cada uno con ventajas específicas según el caso de uso:

#### 1.2.1. CSV (Comma-Separated Values)
El formato CSV es uno de los más simples y ampliamente utilizados para datos tabulares.

**¿Qué es?** Un archivo de texto plano donde cada línea representa una fila de datos y los valores se separan por delimitadores (típicamente comas).

**Características clave:**
- **Human-readable**: Puedes abrirlo y leerlo directamente en un editor de texto
- **Simple**: Estructura sencilla de filas y columnas
- **Flexible en delimitadores**: Aunque se llama CSV, el delimitador puede ser cualquier carácter (coma, pipe |, tab, etc.)

**Ejemplo de CSV:**
```
nombre,edad,ciudad
Juan,25,Madrid
María,30,Barcelona
```

**Ventajas:**
- Fácil de leer y modificar manualmente
- Compatible con prácticamente cualquier sistema
- Ideal para intercambiar datos entre diferentes plataformas

**Limitaciones:**
- No eficiente para grandes volúmenes de datos
- No soporta estructuras de datos anidadas
- Puede ser complicado si los datos contienen el carácter delimitador

**¿Cuándo usarlo?**
- Para conjuntos de datos pequeños a medianos
- Cuando necesitas importar/exportar datos de/a hojas de cálculo
- Cuando la legibilidad humana es importante

**Tecnologías que trabajan bien con CSV:**
- Microsoft Excel
- Pandas (Python)
- Herramientas ETL
- Bases de datos SQL

#### 1.2.2. JSON (JavaScript Object Notation)
JSON es un formato más flexible que permite representar estructuras de datos más complejas.

**¿Qué es?** Un formato ligero basado en texto que representa datos como pares clave-valor y soporta estructuras anidadas.

**Características clave:**
- **Human-readable**: Legible por humanos
- **Semi-estructurado**: Permite diferentes campos en diferentes registros
- **Anidado**: Soporta estructuras jerárquicas

**Ejemplo de JSON:**
```json
{
  "nombre": "Juan",
  "edad": 25,
  "direccion": {
    "ciudad": "Madrid",
    "codigo_postal": "28001"
  },
  "telefonos": ["+34612345678", "+34687654321"]
}
```

**Ventajas:**
- Más flexible que CSV
- Soporta datos anidados y arrays
- Ampliamente utilizado en desarrollo web

**Limitaciones:**
- Menos eficiente en almacenamiento que formatos binarios
- Puede ser más complejo de procesar que CSV para grandes volúmenes

**¿Cuándo usarlo?**
- Para datos con estructura variable (diferentes campos por registro)
- En aplicaciones web para comunicación cliente-servidor
- Para configuraciones y ajustes de aplicaciones

**Tecnologías que trabajan bien con JSON:**
- Navegadores web (JavaScript)
- APIs RESTful
- Bases de datos NoSQL (MongoDB)
- Prácticamente todos los lenguajes de programación modernos

#### 1.2.3. Avro
Avro es un formato binario más avanzado que ofrece características adicionales para sistemas de big data.

**¿Qué es?** Un formato binario que almacena tanto los datos como su esquema, permitiendo que los datos se procesen correctamente incluso si el esquema cambia con el tiempo.

**Características clave:**
- **Binario**: No es legible por humanos, pero es más eficiente
- **Esquema integrado**: Almacena la estructura junto con los datos
- **Evolución de esquema**: Permite que el esquema cambie sin afectar la compatibilidad

**Ventajas:**
- Muy eficiente en términos de almacenamiento
- El esquema integrado facilita la compatibilidad entre versiones
- Bueno para serialización de datos entre sistemas

**Limitaciones:**
- No es human-readable (requiere herramientas para visualizar)
- Mayor complejidad de implementación que CSV/JSON

**¿Cuándo usarlo?**
- En ecosistemas de big data
- Cuando el esquema de datos puede evolucionar con el tiempo
- Para transportar datos eficientemente entre sistemas

**Tecnologías que trabajan bien con Avro:**
- Apache Kafka (para mensajería)
- Apache Spark (para procesamiento)
- Apache Hadoop (para almacenamiento)
- Apache Flink (para streaming)

#### 1.2.4. Parquet
Parquet es un formato de almacenamiento columnar especialmente diseñado para análisis de datos.

**¿Qué es?** Un formato de almacenamiento columnar (en lugar de por filas) que optimiza las consultas analíticas y compresión de datos.

**Características clave:**
- **Almacenamiento columnar**: Guarda datos por columnas en lugar de por filas
- **Altamente comprimible**: Optimizado para reducir espacio
- **Eficiente para consultas analíticas**: Permite leer solo las columnas necesarias

**¿Cómo funciona?** A diferencia de los formatos basados en filas (como CSV), Parquet agrupa los datos por columnas:

**Datos en formato por filas (como CSV):**
```
ID,Nombre,Edad
1,Juan,25
2,María,30
```

**La misma información conceptualmente en Parquet (columnar):**
```
Columna ID: [1,2]
Columna Nombre: [Juan,María]
Columna Edad: [25,30]
```

**Ventajas:**
- Reduce significativamente el volumen de datos leídos en consultas
- Mejor compresión (datos similares agrupados)
- Ideal para sistemas distribuidos

**¿Cuándo usarlo?**
- Para grandes conjuntos de datos analíticos
- Cuando tus consultas suelen seleccionar solo algunas columnas
- En pipelines de big data y data warehousing

**Ejemplo práctico:** Si tienes una tabla con 100 columnas pero tu consulta solo necesita 5, Parquet te permite leer únicamente esas 5 columnas, reduciendo drásticamente la cantidad de datos transferidos.

**Tecnologías que trabajan bien con Parquet:**
- Amazon Redshift Spectrum
- Apache Spark
- Apache Hive
- Presto
- Apache Impala

---

## Resumen para el examen

### Fuentes de Datos
- **JDBC**: Conexión a bases de datos desde Java - platform-independent pero language-dependent (Java)
- **ODBC**: Conexión a bases de datos desde cualquier lenguaje - language-independent pero platform-dependent (requiere drivers específicos)
- **Log Files**: Archivos de registro que pueden almacenarse en S3 para análisis posterior
- **APIs**: Interfaces programáticas para obtener datos de sistemas externos
- **Streams**: Flujos continuos de datos en tiempo real (Kinesis en AWS, Kafka)

### Formatos de Datos
- **CSV**: 
  - Formato simple de texto separado por delimitadores
  - Ventajas: human-readable, compatible con todo, fácil exportación/importación
  - Usar para: conjuntos pequeños/medianos, intercambio de datos, Excel, Pandas

- **JSON**: 
  - Formato de pares clave-valor que permite estructuras anidadas
  - Ventajas: flexible, soporta datos anidados y arrays
  - Usar para: aplicaciones web, configuraciones, datos semi-estructurados, APIs RESTful

- **Avro**: 
  - Formato binario que incluye el esquema con los datos
  - Ventajas: eficiente, evolución de esquema, buena serialización
  - Usar para: big data, esquemas cambiantes, Kafka, Spark, Hadoop

- **Parquet**: 
  - Formato columnar optimizado para analítica
  - Ventajas: lectura selectiva de columnas, alta compresión, eficiente para consultas
  - Usar para: data warehousing, grandes conjuntos de datos analíticos, Redshift Spectrum, Spark

**Consejo para el examen:** Presta especial atención a Parquet, ya que es fundamental en entornos de análisis de datos en AWS, particularmente con servicios como Redshift Spectrum, Athena y EMR.

## 2. Amazon EBS (Elastic Block Store)

Amazon Elastic Block Store (EBS) es uno de los principales servicios de almacenamiento para instancias EC2. Es fundamental entender sus características y capacidades para el examen AWS Certified Machine Learning - Associate.

### 2.1. Conceptos Fundamentales de EBS

#### 2.1.1. ¿Qué es Amazon EBS?
- **Definición**: EBS significa "Elastic Block Store" y funciona como un dispositivo de almacenamiento en bloque que se puede conectar a instancias EC2.
- **Naturaleza**: Es un dispositivo de almacenamiento en red, no físico, que se comunica con las instancias a través de la red.
- **Analogía**: Puede pensarse como un "USB en red" que puedes conectar y desconectar entre diferentes computadoras (instancias).

#### 2.1.2. Características Principales
- **Persistencia de datos**: Los datos en volúmenes EBS persisten incluso después de que una instancia EC2 se termina (a menos que se configure lo contrario).
- **Flexibilidad de conexión**: Pueden desconectarse de una instancia y conectarse a otra rápidamente, facilitando la recuperación ante fallos.
- **Independencia de instancias**: Los volúmenes EBS pueden existir de forma independiente sin estar conectados a una instancia EC2.

#### 2.1.3. Limitaciones y Restricciones
- **Restricción de conexión**: A nivel básico, un volumen EBS solo puede conectarse a una instancia EC2 a la vez.
- **Restricción de zona**: Los volúmenes EBS están limitados a una Availability Zone (AZ) específica. Si un volumen se crea en us-east-1a, no puede conectarse a una instancia en us-east-1b.
- **Aprovisionamiento previo**: Se debe especificar la capacidad (GB) y rendimiento (IOPS) por adelantado.

### 2.2. Consideraciones Operativas

#### 2.2.1. Facturación
- Se factura por la capacidad aprovisionada, no por la utilizada.
- AWS ofrece 30 GB de almacenamiento EBS gratuito por mes (tipos GP2, SSD o Magnético).
- La capacidad y rendimiento pueden aumentarse con el tiempo según sea necesario.

#### 2.2.2. Atributo "Delete on Termination"
- **Propósito**: Controla si el volumen EBS se elimina automáticamente cuando se termina la instancia EC2 asociada.
- **Comportamiento predeterminado**:
  - **Volumen raíz**: Habilitado (se elimina al terminar la instancia)
  - **Volúmenes adicionales**: Deshabilitado (persisten después de terminar la instancia)
- **Caso de uso**: Si necesitas preservar datos del volumen raíz después de terminar una instancia, puedes deshabilitar esta opción.

### 2.3. EBS Elastic Volumes

Una característica importante de EBS es la capacidad de modificar volúmenes sin interrupciones, conocida como "EBS Elastic Volumes".

#### 2.3.1. Ventajas
- **Sin tiempo de inactividad**: No es necesario desconectar el volumen o reiniciar la instancia para hacer cambios.
- **Modificaciones en caliente**: Permite cambiar el tipo de volumen, tamaño y características de rendimiento mientras está en uso.

#### 2.3.2. Tipos de Modificaciones Posibles
- **Aumento de tamaño**: 
  - Puedes aumentar el tamaño del volumen sin interrupciones.
  - **Limitación**: No es posible reducir el tamaño de un volumen EBS existente.

- **Cambio de tipo de volumen**: 
  - Ejemplo: Cambiar de GP2 a GP3 sin desconexión.
  - Se recomienda especificar explícitamente los IOPS o el rendimiento deseados al cambiar el tipo.

- **Ajuste de rendimiento**: 
  - Puedes aumentar o disminuir los atributos de rendimiento (IOPS, rendimiento) según tus necesidades.

#### 2.3.3. Procedimiento de Modificación
1. Acceder a la consola de AWS
2. Seleccionar el volumen EBS
3. Ir a "Actions" → "Modify Volume"
4. Especificar los cambios deseados
5. Confirmar los cambios

### 2.4. Configuraciones Comunes

#### 2.4.1. Un Volumen para Una Instancia
- Configuración básica: Una instancia EC2 con un volumen EBS conectado.
- El volumen se puede utilizar como almacenamiento principal o adicional.

#### 2.4.2. Múltiples Volúmenes por Instancia
- Una instancia EC2 puede tener varios volúmenes EBS conectados simultáneamente.
- Útil para separar sistemas operativos, datos de aplicaciones y logs.

#### 2.4.3. Volúmenes en Diferentes Zonas
- Los volúmenes EBS están vinculados a una AZ específica.
- Para tener almacenamiento en varias zonas, es necesario crear volúmenes separados en cada zona.

#### 2.4.4. Volúmenes No Conectados
- Los volúmenes EBS pueden existir sin estar conectados a una instancia.
- Permite una conexión bajo demanda cuando sea necesario.

---

## Resumen para el examen

### Conceptos Fundamentales de EBS
- **Definición**: Almacenamiento en bloques en red para instancias EC2
- **Persistencia**: Los datos se mantienen incluso si la instancia se termina
- **Conexión**: Un volumen EBS solo puede conectarse a una instancia a la vez
- **Limitación de zona**: Los volúmenes están vinculados a una AZ específica
- **Aprovisionamiento**: Se debe especificar capacidad y rendimiento por adelantado

### Atributo "Delete on Termination"
- **Volumen raíz**: Habilitado por defecto (se elimina con la instancia)
- **Otros volúmenes**: Deshabilitado por defecto (persisten tras terminar la instancia)
- Si quieres preservar el volumen raíz, desactiva esta opción

### EBS Elastic Volumes
- Permite modificar volúmenes sin desconectarlos o reiniciar la instancia
- **Modificaciones posibles**:
  - Aumentar tamaño (nunca reducir)
  - Cambiar tipo de volumen (GP2 a GP3, etc.)
  - Ajustar parámetros de rendimiento (IOPS, throughput)

### Configuraciones Prácticas
- Una instancia puede tener múltiples volúmenes EBS
- Los volúmenes pueden existir sin estar conectados a instancias
- Para usar volúmenes en diferentes AZs, debes crearlos específicamente en cada zona

**Consejo para el examen**: Presta especial atención al comportamiento de "Delete on Termination" y las capacidades de EBS Elastic Volumes, ya que son temas recurrentes en las preguntas del examen.



## 3. Amazon EFS (Elastic File System)

Amazon Elastic File System (EFS) es un servicio de almacenamiento de archivos completamente administrado que ofrece una solución escalable y compartida para instancias EC2. A diferencia de EBS, EFS permite compartir datos entre múltiples instancias simultáneamente.

### 3.1. Conceptos Fundamentales de EFS

#### 3.1.1. ¿Qué es Amazon EFS?
- **Definición**: EFS es un sistema de archivos en red (NFS) administrado por AWS.
- **Naturaleza**: Funciona como un sistema de archivos compartido al que pueden acceder múltiples instancias EC2 simultáneamente.
- **Diferencia con EBS**: Mientras EBS es un almacenamiento en bloques que solo se puede conectar a una instancia a la vez, EFS permite conexiones simultáneas desde múltiples instancias.

#### 3.1.2. Características Principales
- **Multi-AZ**: Puede ser montado en instancias EC2 ubicadas en diferentes Availability Zones.
- **Alta disponibilidad**: Al extenderse a través de múltiples AZs, ofrece mayor resistencia a fallos.
- **Escalabilidad automática**: Crece o se reduce automáticamente según las necesidades, sin necesidad de aprovisionamiento previo.
- **Modelo de pago**: Se paga solo por el almacenamiento utilizado, no por la capacidad aprovisionada.

#### 3.1.3. Limitaciones y Requisitos
- **Compatibilidad**: Solo funciona con instancias basadas en Linux, no es compatible con Windows.
- **Seguridad**: Requiere la configuración de Security Groups para controlar el acceso.
- **Protocolo**: Utiliza el protocolo NFS (Network File System) estándar.
- **Sistema de archivos**: Utiliza el sistema POSIX con API de archivos estándar en Linux.

### 3.2. Rendimiento y Capacidad

#### 3.2.1. Escalabilidad
- Soporta miles de clientes NFS concurrentes.
- Ofrece más de 10 GB/s de throughput.
- Capacidad de crecimiento automático hasta escala de petabytes.

#### 3.2.2. Modos de Rendimiento
- **General Purpose**: 
  - Es el modo predeterminado.
  - Optimizado para casos de uso sensibles a la latencia.
  - Ideal para servidores web, CMS y aplicaciones interactivas.

- **Max I/O**: 
  - Ofrece mayor throughput a costa de mayor latencia.
  - Diseñado para procesamiento altamente paralelo.
  - Ideal para aplicaciones de big data y procesamiento multimedia.

#### 3.2.3. Modos de Throughput
- **Bursting**: 
  - El throughput escala con el tamaño de almacenamiento.
  - Ejemplo: 1 TB = 50 MB/s base con ráfagas de hasta 100 MB/s.

- **Provisioned**: 
  - Permite establecer el throughput independientemente del tamaño de almacenamiento.
  - Útil cuando necesitas un rendimiento específico sin relación con la cantidad de datos almacenados.
  - Ejemplo: Puedes configurar 1 GB/s para solo 1 TB de almacenamiento.

- **Elastic**: 
  - Escala automáticamente el throughput según la carga de trabajo.
  - Puede alcanzar hasta 3 GB/s para lecturas y 1 GB/s para escrituras.
  - Ideal para cargas de trabajo impredecibles que varían en intensidad.

### 3.3. Clases de Almacenamiento y Gestión del Ciclo de Vida

#### 3.3.1. Tipos de Almacenamiento (Storage Tiers)
- **EFS Standard**: 
  - Para archivos de acceso frecuente.
  - Mayor costo pero sin latencia adicional para acceder a los datos.

- **EFS-IA (Infrequent Access)**: 
  - Para archivos de acceso poco frecuente.
  - Menor costo de almacenamiento pero con cargo adicional por recuperación.
  - Equilibra rendimiento y costo para datos que se acceden ocasionalmente.

- **EFS Archive**: 
  - Para datos raramente accedidos (pocas veces al año).
  - Costo significativamente menor para almacenamiento.
  - Mayor tiempo y costo de recuperación.

#### 3.3.2. Políticas de Ciclo de Vida
- Permiten mover automáticamente archivos entre diferentes clases de almacenamiento.
- Se configuran basadas en el tiempo desde el último acceso a los archivos.
- Ejemplo: Mover archivos no accedidos durante 60 días de Standard a IA.
- Ayudan a optimizar costos manteniendo archivos frecuentemente accedidos en Standard y los demás en clases más económicas.

### 3.4. Disponibilidad y Durabilidad

#### 3.4.1. Configuración Multi-AZ (Standard)
- Distribuye datos a través de múltiples zonas de disponibilidad.
- Proporciona alta disponibilidad y resistencia ante fallos de AZ.
- Recomendado para cargas de trabajo de producción.

#### 3.4.2. Configuración One Zone
- Almacena datos en una única zona de disponibilidad.
- Menor costo (aproximadamente 47% más económico que Multi-AZ).
- Incluye respaldos automáticos.
- Compatible con la clase de almacenamiento IA (EFS One Zone-IA).
- Adecuado para entornos de desarrollo o datos no críticos.

### 3.5. Casos de Uso

#### 3.5.1. Casos de Uso Ideales
- Sistemas de gestión de contenido (CMS)
- Servidores web
- Compartición de datos entre aplicaciones
- Implementaciones de WordPress
- Aplicaciones que requieren acceso compartido a archivos

#### 3.5.2. Consideraciones de Costo
- Aproximadamente tres veces más costoso que un volumen EBS GP2.
- Utilizando las clases de almacenamiento adecuadas se pueden lograr ahorros de hasta 90%.
- La elección entre Multi-AZ y One Zone puede impactar significativamente en el costo.

---

## Resumen para el examen

### Conceptos Clave de EFS
- **Definición**: Sistema de archivos en red (NFS) administrado por AWS
- **Característica principal**: Permite montarse en múltiples instancias EC2 en diferentes AZs simultáneamente
- **Compatibilidad**: Solo con Linux, no compatible con Windows
- **Escalabilidad**: Automática hasta escala de petabytes, sin necesidad de aprovisionamiento previo
- **Pago**: Solo por el almacenamiento utilizado, no por capacidad aprovisionada

### Rendimiento
- **Modos de rendimiento**: 
  - General Purpose: Para aplicaciones sensibles a la latencia
  - Max I/O: Para alto throughput en aplicaciones paralelas

- **Modos de throughput**:
  - Bursting: Escala con el tamaño de almacenamiento
  - Provisioned: Throughput independiente del almacenamiento
  - Elastic: Adapta automáticamente el throughput a la carga de trabajo

### Clases de Almacenamiento
- **Standard**: Acceso frecuente, sin cargos adicionales por acceso
- **EFS-IA**: Acceso infrecuente, menor costo pero con cargo por recuperación
- **Archive**: Datos raramente accedidos, costo muy reducido

### Disponibilidad
- **Multi-AZ**: Alta disponibilidad para entornos de producción
- **One Zone**: Menor costo, adecuado para desarrollo o datos no críticos

**Consejo para el examen**: Recuerda las diferencias clave entre EBS y EFS - EBS es almacenamiento en bloques para una sola instancia, mientras que EFS es un sistema de archivos en red que permite acceso simultáneo desde múltiples instancias en diferentes zonas de disponibilidad.


## 4. Amazon EFS vs. Amazon EBS

En esta sección analizaremos las diferencias fundamentales entre los dos principales servicios de almacenamiento para instancias EC2: Amazon EBS (Elastic Block Store) y Amazon EFS (Elastic File System). Comprender estas diferencias es crucial para determinar qué servicio es el más adecuado según los requisitos específicos de almacenamiento.

### 4.1. Comparación General

| Característica | Amazon EBS | Amazon EFS | Instance Store |
|----------------|------------|------------|----------------|
| **Tipo de almacenamiento** | Almacenamiento en bloques | Sistema de archivos en red (NFS) | Almacenamiento físico local |
| **Conexión** | Una instancia a la vez (con excepciones) | Múltiples instancias simultáneamente | Solo la instancia a la que está físicamente conectado |
| **Alcance de disponibilidad** | Limitado a una AZ | A través de múltiples AZs | Limitado a la instancia |
| **Persistencia** | Persiste independientemente de la instancia | Persiste independientemente de las instancias | Se pierde si la instancia se termina |
| **Precio** | Menor costo | Aproximadamente 3x más costoso que EBS GP2 | Incluido en el costo de la instancia |
| **Compatibilidad** | Linux y Windows | Solo Linux (sistema POSIX) | Linux y Windows |

### 4.2. Características Específicas de Amazon EBS

#### 4.2.1. Limitaciones de Conexión
- **Regla general**: Un volumen EBS solo puede conectarse a una instancia EC2 a la vez.
- **Excepción**: Los volúmenes io1 e io2 tienen una característica de "multi-attach" para casos de uso muy específicos.

#### 4.2.2. Restricciones de Zona
- Los volúmenes EBS están vinculados a una única Availability Zone.
- Ejemplo: Un volumen EBS en AZ-1 no puede conectarse a una instancia en AZ-2.

#### 4.2.3. Escalado de Rendimiento
- **Volúmenes gp2**: El rendimiento (IOPS) aumenta automáticamente con el tamaño del disco.
- **Volúmenes gp3 e io1**: Permiten aumentar el rendimiento independientemente del tamaño del disco.

#### 4.2.4. Migración Entre AZs
- **Proceso**: Para mover un volumen EBS entre zonas de disponibilidad, se debe:
  1. Crear un snapshot del volumen
  2. Restaurar el snapshot en la nueva AZ

#### 4.2.5. Consideraciones de Backup
- Los backups de EBS utilizan operaciones de I/O
- No se recomienda ejecutar backups durante períodos de alto tráfico para evitar impactos en el rendimiento

#### 4.2.6. Comportamiento de Terminación
- Por defecto, el volumen raíz de EBS se elimina cuando se termina la instancia EC2
- Este comportamiento puede deshabilitarse si se necesita conservar los datos

### 4.3. Características Específicas de Amazon EFS

#### 4.3.1. Capacidad de Conexión
- Diseñado para conectarse a cientos de instancias simultáneamente
- Permite compartir datos entre múltiples instancias

#### 4.3.2. Arquitectura Multi-AZ
- Un único sistema de archivos EFS puede tener múltiples "mount targets" en diferentes AZs
- Las instancias en diferentes AZs pueden acceder al mismo sistema de archivos EFS

#### 4.3.3. Compatibilidad
- Solo compatible con instancias Linux
- Utiliza el sistema de archivos POSIX

#### 4.3.4. Consideraciones de Costo
- Precio más elevado que EBS
- Ofrece diferentes niveles de almacenamiento (storage tiers) para optimizar costos:
  - Standard: para acceso frecuente
  - IA (Infrequent Access): para datos menos utilizados
  - Archive: para datos raramente accedidos

### 4.4. Instance Store

#### 4.4.1. Características Principales
- Almacenamiento físicamente conectado a la instancia EC2
- No persiste si la instancia se termina o se detiene
- Si se pierde la instancia, se pierden los datos almacenados

#### 4.4.2. Casos de Uso
- Datos temporales o cache
- Información que puede recrearse
- Procesamiento de alta velocidad que requiere almacenamiento local

### 4.5. Escenarios de Uso y Selección

#### 4.5.1. Cuándo Usar Amazon EBS
- Almacenamiento de sistema operativo (volumen raíz)
- Bases de datos que requieren baja latencia
- Aplicaciones que necesitan almacenamiento dedicado
- Datos que no necesitan ser compartidos entre instancias

#### 4.5.2. Cuándo Usar Amazon EFS
- Compartición de archivos entre múltiples instancias
- Sistemas de gestión de contenido (CMS)
- Servidores web con contenido compartido
- Implementaciones de WordPress
- Aplicaciones que requieren acceso a los mismos archivos desde diferentes instancias

#### 4.5.3. Cuándo Usar Instance Store
- Almacenamiento temporal de alta velocidad
- Datos que pueden reconstruirse
- Cache o procesamiento intermedio de datos

---

## Resumen para el examen

### Diferencias clave entre EBS y EFS
- **EBS**: 
  - Conectado a una sola instancia a la vez (excepto multi-attach en io1/io2)
  - Limitado a una única AZ (requiere snapshots para migrar)
  - Compatible con Linux y Windows
  - Menor costo
  - Rendimiento ajustable según el tipo de volumen (gp2, gp3, io1)

- **EFS**: 
  - Puede conectarse a cientos de instancias simultáneamente
  - Funciona a través de múltiples AZs
  - Solo compatible con Linux (sistema POSIX)
  - Mayor costo pero con opciones de optimización (storage tiers)
  - Ideal para compartir datos entre aplicaciones/instancias

- **Instance Store**:
  - Almacenamiento físico directamente conectado a la instancia
  - Datos efímeros que se pierden cuando la instancia se detiene/termina
  - No requiere configuración adicional

**Consejo para el examen**: Presta atención a los escenarios que involucran compartir datos entre instancias o trabajar a través de múltiples AZs. En estos casos, EFS suele ser la respuesta correcta, mientras que para necesidades de almacenamiento dedicado y de baja latencia, EBS es la opción preferida.


## 5. Amazon FSx

Amazon FSx es un servicio que permite lanzar sistemas de archivos de alto rendimiento de terceros en AWS como un servicio completamente administrado. Similar a cómo RDS ofrece bases de datos administradas, FSx ofrece sistemas de archivos administrados.

### 5.1. Conceptos Fundamentales de Amazon FSx

#### 5.1.1. ¿Qué es Amazon FSx?
- **Definición**: Servicio que proporciona sistemas de archivos de terceros completamente administrados en AWS.
- **Concepto clave**: Similar a RDS pero para sistemas de archivos en lugar de bases de datos.
- **Variantes principales**: FSx ofrece diferentes tipos de sistemas de archivos para diferentes necesidades:
  - FSx for Windows File Server
  - FSx for Lustre
  - FSx for NetApp ONTAP
  - FSx for OpenZFS

### 5.2. FSx for Windows File Server

#### 5.2.1. Características Principales
- **Tipo de sistema**: Sistema de archivos Windows completamente administrado.
- **Protocolos compatibles**: 
  - SMB (Server Message Block)
  - Windows NTFS (NT File System)
- **Integración con Active Directory**: Compatible con Microsoft Active Directory para gestión de seguridad.
- **Controles de acceso**: Soporta ACLs (Access Control Lists) y cuotas de usuario.

#### 5.2.2. Compatibilidad
- Principalmente diseñado para Windows, pero también puede montarse en instancias Linux EC2.
- Se puede integrar con servidores Windows on-premises utilizando Microsoft Distributed File System (DFS).

#### 5.2.3. Rendimiento y Almacenamiento
- **Escalabilidad**: Hasta decenas de GB/s, millones de IOPS y cientos de petabytes.
- **Opciones de almacenamiento**:
  - **SSD**: Para cargas de trabajo sensibles a la latencia (bases de datos, procesamiento multimedia, análisis de datos).
  - **HDD**: Más económico, para uso general (directorios personales, CMS).

#### 5.2.4. Disponibilidad y Recuperación
- **Configuración Multi-AZ** disponible para alta disponibilidad.
- **Backups diarios** automáticos a Amazon S3 para recuperación ante desastres.
- **Acceso desde on-premises** mediante conexiones privadas (VPN o Direct Connect).

### 5.3. FSx for Lustre

#### 5.3.1. Características Principales
- **Definición**: Sistema de archivos distribuido para computación a gran escala.
- **Origen del nombre**: "Lustre" viene de "Linux" + "cluster".
- **Casos de uso principales**: 
  - Machine Learning
  - High Performance Computing (HPC)
  - Procesamiento de video
  - Modelado financiero
  - Automatización de diseño electrónico

#### 5.3.2. Rendimiento
- **Escalabilidad**: Cientos de GB/s, millones de IOPS, latencia de sub-milisegundos.
- **Opciones de almacenamiento**:
  - **SSD**: Para cargas de trabajo intensivas en IOPS, operaciones con archivos pequeños y aleatorios.
  - **HDD**: Para cargas de trabajo intensivas en throughput, operaciones con archivos grandes y secuenciales.

#### 5.3.3. Integración con S3
- **Característica clave**: Integración nativa con Amazon S3.
- Permite leer S3 como un sistema de archivos a través de FSx.
- Permite escribir los resultados de los cálculos desde FSx de vuelta a S3.

#### 5.3.4. Opciones de Implementación
- **Scratch File System**:
  - Almacenamiento temporal sin replicación.
  - Mayor rendimiento (6 veces superior al sistema persistente).
  - Hasta 200 MB/s por TB de throughput.
  - Uso: Procesamiento a corto plazo, optimización de costos.
  - Solo mantiene una copia de los datos.
  - Opcional: Puede tener un bucket S3 subyacente como repositorio de datos.

- **Persistent File System**:
  - Para almacenamiento a largo plazo.
  - Datos replicados dentro de la misma Availability Zone.
  - Reemplazo transparente de archivos en minutos si falla un servidor.
  - Uso: Procesamiento a largo plazo y almacenamiento de datos sensibles.
  - Mantiene dos copias de los datos para redundancia.

### 5.4. FSx for NetApp ONTAP

#### 5.4.1. Características Principales
- **Definición**: Sistema de archivos NetApp ONTAP administrado en AWS.
- **Protocolos compatibles**: NFS, SMB e iSCSI.
- **Caso de uso principal**: Migrar cargas de trabajo que ya se ejecutan en ONTAP o en un NAS on-premises a AWS.

#### 5.4.2. Compatibilidad
- **Sistemas operativos**: Linux, Windows y macOS.
- **Servicios AWS**: Compatible con VMware Cloud on AWS, WorkSpaces, AppStream, EC2, ECS y EKS.

#### 5.4.3. Características Avanzadas
- **Auto-escalado**: El almacenamiento crece o se reduce automáticamente.
- **Gestión de datos**: 
  - Replicación y snapshots.
  - Compresión de datos.
  - Deduplicación de datos (eliminación de duplicados).
- **Clonación instantánea**: Permite crear copias instantáneas point-in-time para probar nuevas cargas de trabajo.

### 5.5. FSx for OpenZFS

#### 5.5.1. Características Principales
- **Definición**: Sistema de archivos OpenZFS administrado en AWS.
- **Protocolos compatibles**: Solo NFS (Network File System) en múltiples versiones.
- **Caso de uso principal**: Migrar cargas de trabajo que ya se ejecutan en ZFS internamente hacia AWS.

#### 5.5.2. Compatibilidad
- **Sistemas operativos**: Linux, Mac, Windows, etc.

#### 5.5.3. Rendimiento y Características
- **Rendimiento**: Hasta 1 millón de IOPS con menos de 0.5 ms de latencia.
- **Funcionalidades**:
  - Soporta snapshots.
  - Compresión.
  - Bajo costo.
  - No soporta deduplicación de datos.
- **Clonación instantánea**: Al igual que NetApp ONTAP, permite clonación point-in-time para probar cargas de trabajo.

### 5.6. Selección del Sistema de Archivos Adecuado

Para elegir el sistema de archivos FSx más apropiado, considera los siguientes factores:

#### 5.6.1. FSx for Windows File Server
- **Usar cuando**: 
  - Necesites compatibilidad con protocolos Windows (SMB).
  - Requieras integración con Active Directory.
  - Tengas aplicaciones Windows que necesiten un sistema de archivos compartido.

#### 5.6.2. FSx for Lustre
- **Usar cuando**: 
  - Necesites procesamiento de alto rendimiento (HPC).
  - Trabajes con Machine Learning.
  - Requieras integración con S3 como sistema de archivos.
  - Necesites throughput muy alto para procesamiento de datos.

#### 5.6.3. FSx for NetApp ONTAP
- **Usar cuando**: 
  - Estés migrando desde sistemas NetApp ONTAP on-premises.
  - Necesites compatibilidad con múltiples protocolos (NFS, SMB, iSCSI).
  - Requieras funcionalidades avanzadas como deduplicación y compresión.
  - Necesites clonación instantánea para entornos de prueba.

#### 5.6.4. FSx for OpenZFS
- **Usar cuando**: 
  - Estés migrando desde sistemas ZFS existentes.
  - Necesites un rendimiento extremadamente alto (millones de IOPS).
  - Requieras latencias muy bajas.
  - Necesites compresión de datos y snapshots.

---

## Resumen para el examen

### Tipos de Amazon FSx
- **FSx for Windows File Server**:
  - Sistema de archivos Windows (SMB, NTFS)
  - Integración con Active Directory
  - Opciones: SSD (baja latencia) o HDD (más económico)
  - Compatible con instancias Linux y Windows
  - Soporta Multi-AZ para alta disponibilidad

- **FSx for Lustre**:
  - Para computación de alto rendimiento (HPC) y Machine Learning
  - Integración nativa con S3 (lectura/escritura)
  - Dos modos: Scratch (temporal, sin replicación) o Persistent (replicado en la misma AZ)
  - Muy alto rendimiento (cientos de GB/s)

- **FSx for NetApp ONTAP**:
  - Protocolos: NFS, SMB, iSCSI
  - Para migrar cargas de NetApp on-premises
  - Funciones avanzadas: auto-escalado, compresión, deduplicación, clonación
  - Compatible con múltiples OS y servicios AWS

- **FSx for OpenZFS**:
  - Solo protocolo NFS
  - Para migrar cargas ZFS existentes
  - Alto rendimiento: hasta 1 millón de IOPS, latencia <0.5ms
  - Sin deduplicación, pero con compresión y snapshots

**Consejo para el examen**: Presta atención a las palabras clave en las preguntas. Por ejemplo, si mencionan "High Performance Computing" o "Machine Learning" con alto throughput, piensa en FSx for Lustre. Si hablan de Windows, Active Directory o SMB, la respuesta probablemente sea FSx for Windows File Server.


## 6. Amazon Kinesis Data Streams

Amazon Kinesis Data Streams es un servicio que permite recolectar y almacenar datos de streaming en tiempo real, siendo una pieza fundamental en arquitecturas de procesamiento de datos que requieren análisis inmediato.

### 6.1. Conceptos Fundamentales de Kinesis Data Streams

#### 6.1.1. ¿Qué es Amazon Kinesis Data Streams?
- **Definición**: Servicio administrado para la recolección y almacenamiento de datos de streaming en tiempo real.
- **Palabra clave para el examen**: "Tiempo real" (real-time) es el término clave para identificar cuándo usar Kinesis Data Streams.
- **Concepto básico**: Permite capturar, procesar y analizar flujos de datos a medida que se generan, sin necesidad de esperar a que se acumulen.

#### 6.1.2. Tipos de Datos en Tiempo Real
- **Click streams**: Datos generados cuando los usuarios interactúan con un sitio web.
- **Dispositivos conectados**: Datos provenientes de IoT (Internet of Things), como bicicletas conectadas.
- **Métricas y logs**: Información generada por servidores que requiere análisis inmediato.

### 6.2. Arquitectura de Kinesis Data Streams

#### 6.2.1. Componentes Principales

##### 6.2.1.1. Productores (Producers)
- **Aplicaciones**: Código personalizado que envía datos desde websites o dispositivos hacia Kinesis Data Streams.
- **Kinesis Agent**: Software que se instala en servidores para enviar métricas y logs automáticamente.
- **Kinesis Producer Library (KPL)**: Biblioteca para crear aplicaciones productoras optimizadas con alto throughput.

##### 6.2.1.2. Consumidores (Consumers)
- **Aplicaciones personalizadas**: Código que lee datos de Kinesis Data Streams.
- **Funciones Lambda**: Pueden procesar registros de Kinesis automáticamente.
- **Amazon Data Firehose**: Servicio para cargar datos de streaming a servicios de almacenamiento.
- **Servicios de análisis**: Como Amazon Managed Service for Apache Flink.
- **Kinesis Client Library (KCL)**: Biblioteca para crear aplicaciones consumidoras optimizadas.

#### 6.2.2. Flujo de Datos
1. Los productores envían datos en tiempo real a Kinesis Data Streams.
2. Los datos se almacenan temporalmente en el stream durante un periodo configurable.
3. Múltiples consumidores pueden leer los mismos datos simultáneamente.
4. Los consumidores procesan o almacenan los datos según sea necesario.

### 6.3. Características Clave

#### 6.3.1. Retención de Datos
- Los datos pueden conservarse en el stream hasta 365 días.
- La persistencia permite reprocesar y reproducir datos por los consumidores.
- Una vez enviados los datos, no pueden eliminarse manualmente; solo expirarán según el período de retención.

#### 6.3.2. Capacidad y Limitaciones
- Cada registro puede tener hasta 1 MB de tamaño.
- El uso típico es para una gran cantidad de datos pequeños en tiempo real.
- El ordenamiento de datos se mantiene si se utiliza el mismo Partition ID para datos relacionados.

#### 6.3.3. Seguridad
- **Cifrado en reposo**: Utilizando AWS KMS (Key Management Service).
- **Cifrado en tránsito**: A través de conexiones HTTPS.

### 6.4. Modos de Capacidad

#### 6.4.1. Modo Provisionado (Provisioned Mode)
- **Configuración**: Se elige manualmente el número de shards del stream.
- **Capacidad de ingesta**: 1 MB/s o 1,000 registros por segundo por shard.
- **Capacidad de lectura**: 2 MB/s por shard.
- **Escalamiento**: Se debe escalar manualmente aumentando o disminuyendo el número de shards.
- **Monitoreo**: Requiere monitorización continua del throughput para ajustar adecuadamente.
- **Facturación**: Se paga por cada shard aprovisionado por hora.

#### 6.4.2. Modo Bajo Demanda (On-demand Mode)
- **Configuración**: No requiere aprovisionar o gestionar la capacidad manualmente.
- **Capacidad inicial**: 4 MB/s o 4,000 registros por segundo por defecto.
- **Escalamiento**: Automático basado en el throughput observado durante los últimos 30 días.
- **Facturación**: Se paga por stream por hora, más la cantidad de datos que entran y salen del stream.

### 6.5. Shards en Kinesis Data Streams

#### 6.5.1. ¿Qué es un Shard?
- Unidad básica de capacidad en Kinesis Data Streams.
- Determina el throughput total disponible para un stream.
- Cada stream puede tener desde 1 hasta miles de shards, según las necesidades.

#### 6.5.2. Capacidad por Shard
- **Ingesta (entrada)**: 1 MB/s o 1,000 registros por segundo.
- **Procesamiento (salida)**: 2 MB/s.

#### 6.5.3. Ejemplo de Dimensionamiento
- Para procesar 10 MB/s o 10,000 registros por segundo de entrada, se necesitarían 10 shards.
- Para escalar hasta 100 MB/s, se requerirían 100 shards.

### 6.6. Casos de Uso Comunes

#### 6.6.1. Análisis en Tiempo Real
- Monitoreo de actividad de usuarios en sitios web.
- Detección de fraudes en transacciones financieras.
- Análisis de tendencias en redes sociales.

#### 6.6.2. Procesamiento de Logs
- Recopilación centralizada de logs de aplicaciones.
- Monitoreo de infraestructura en tiempo real.
- Alertas basadas en patrones o anomalías.

#### 6.6.3. IoT (Internet of Things)
- Captura de datos de sensores y dispositivos conectados.
- Monitoreo de flotas de vehículos o equipos industriales.
- Análisis de telemetría en tiempo real.

#### 6.6.4. Análisis de Métricas
- Monitoreo del rendimiento de aplicaciones.
- Dashboards en tiempo real.
- Sistemas de alerta automatizados.

---

## Resumen para el examen

### Conceptos Clave de Kinesis Data Streams
- **Propósito principal**: Recolección y almacenamiento de datos de streaming en tiempo real.
- **Palabra clave**: "Tiempo real" indica que Kinesis Data Streams podría ser la solución adecuada.
- **Retención de datos**: Hasta 365 días (una vez enviados, no pueden eliminarse manualmente).
- **Tamaño máximo de registro**: 1 MB.
- **Orden de datos**: Garantizado solo dentro del mismo Partition ID.

### Productores y Consumidores
- **Productores**: Aplicaciones, Kinesis Agent, o código usando Kinesis Producer Library (KPL).
- **Consumidores**: Aplicaciones personalizadas, Lambda, Data Firehose, Apache Flink, o código usando Kinesis Client Library (KCL).

### Modos de Capacidad
- **Provisioned**: 
  - Control manual de shards
  - 1 MB/s de entrada y 2 MB/s de salida por shard
  - Pago por shard-hora

- **On-demand**: 
  - Escala automáticamente
  - Capacidad inicial de 4 MB/s
  - Pago por uso (cantidad de datos procesados)

### Shards
- Unidad básica de capacidad en Kinesis Data Streams.
- Determina cuántos datos pueden ingresar y ser procesados por el stream.
- La elección del número correcto de shards es crucial para el rendimiento y costo.

**Consejo para el examen**: Cuando veas preguntas sobre procesamiento de datos en tiempo real, especialmente con términos como "streaming", "procesamiento continuo" o necesidad de procesar datos "a medida que se generan", considera Kinesis Data Streams como una posible solución.


## 7. Amazon Data Firehose

Amazon Data Firehose es un servicio completamente administrado que facilita la carga de datos de streaming en diversos destinos de almacenamiento y análisis. Anteriormente conocido como Kinesis Data Firehose, este servicio se ha expandido para ofrecer más capacidades más allá del ecosistema Kinesis.

### 7.1. Conceptos Fundamentales de Amazon Data Firehose

#### 7.1.1. ¿Qué es Amazon Data Firehose?
- **Definición**: Servicio completamente administrado para enviar datos desde diversas fuentes hacia destinos específicos.
- **Característica clave**: Facilita la entrega de datos de streaming sin tener que desarrollar aplicaciones personalizadas.
- **Diferencia con Kinesis Data Streams**: Mientras que Kinesis Data Streams es un servicio de recopilación de datos en tiempo real, Firehose se enfoca en la carga de estos datos a destinos de almacenamiento.

#### 7.1.2. Naturaleza del Servicio
- **Completamente administrado**: No requiere gestión de infraestructura.
- **Serverless**: Se escala automáticamente según las necesidades.
- **Modelo de pago**: Solo se paga por la cantidad de datos procesados.
- **Temporalidad**: Se considera un servicio "near real-time" (tiempo casi real) debido a su mecanismo de buffer.

### 7.2. Arquitectura y Flujo de Datos

#### 7.2.1. Fuentes de Datos (Productores)

Data Firehose puede recibir datos de múltiples fuentes:

- **Aplicaciones personalizadas**: Utilizando el SDK de AWS para enviar datos directamente.
- **Agentes Kinesis**: Software instalado en servidores para recopilar y enviar datos.
- **Servicios AWS integrados**:
  - Kinesis Data Streams
  - Amazon CloudWatch Logs y Events
  - AWS IoT

#### 7.2.2. Procesamiento de Datos

Una vez que los datos llegan a Firehose, pueden procesarse de las siguientes maneras:

- **Transformación opcional**: Mediante funciones AWS Lambda para convertir formatos o modificar datos.
- **Almacenamiento en buffer**: Los datos se acumulan temporalmente hasta alcanzar un umbral de tamaño o tiempo.
- **Conversión de formato**: Firehose puede convertir datos a formatos como Parquet u ORC.
- **Compresión**: Soporte para compresión gzip o snappy.

#### 7.2.3. Destinos Soportados

Firehose puede entregar datos a diversos destinos:

- **Servicios AWS**:
  - Amazon S3: Para almacenamiento de datos a largo plazo.
  - Amazon Redshift: Para análisis de datos.
  - Amazon OpenSearch: Para búsqueda y análisis.

- **Servicios de terceros**:
  - Datadog
  - Splunk
  - New Relic
  - MongoDB

- **Destinos personalizados**:
  - Endpoints HTTP para integraciones personalizadas.

#### 7.2.4. Mecanismo de Backup
- Opción para almacenar todos los datos o solo los registros fallidos en un bucket de S3.
- Proporciona un mecanismo de recuperación para datos que no pudieron entregarse al destino principal.

### 7.3. Características Principales

#### 7.3.1. Formatos de Datos Soportados
- **Entrada**: CSV, JSON, Parquet, Avro, texto plano, datos binarios.
- **Conversión**: Capacidad para transformar datos a formatos Parquet u ORC para optimizar el almacenamiento y la consulta.

#### 7.3.2. Transformación de Datos
- **Transformaciones integradas**: Conversión de formato y compresión.
- **Transformaciones personalizadas**: Mediante funciones Lambda.
- **Ejemplo**: Convertir datos de formato CSV a JSON antes de enviarlos a S3.

#### 7.3.3. Buffer y Latencia
- Firehose acumula datos en un buffer antes de enviarlos al destino.
- El buffer se puede configurar basado en tamaño (MB) o tiempo (segundos).
- Esta característica de buffering es la razón por la que se considera un servicio "near real-time" en lugar de "real-time".
- El buffer puede desactivarse en casos específicos, pero generalmente se utiliza para optimizar la escritura en los destinos.

#### 7.3.4. Escalado Automático
- El servicio se escala automáticamente para adaptarse al volumen de datos sin intervención manual.
- No hay necesidad de aprovisionar capacidad o gestionar shards como en Kinesis Data Streams.

### 7.4. Comparación: Amazon Data Firehose vs. Kinesis Data Streams

| Característica | Amazon Data Firehose | Kinesis Data Streams |
|----------------|----------------------|----------------------|
| **Propósito principal** | Cargar datos de streaming en destinos específicos | Recolectar y procesar datos en tiempo real |
| **Desarrollo requerido** | Mínimo, servicio completamente administrado | Requiere código personalizado para productores y consumidores |
| **Temporalidad** | Near real-time (casi en tiempo real) | Real-time (tiempo real) |
| **Modos de capacidad** | Escalado automático | Modos provisionado y bajo demanda |
| **Almacenamiento de datos** | No almacena datos (solo buffer temporal) | Hasta 365 días |
| **Capacidad de replay** | No disponible | Sí, permite reprocesar datos históricos |
| **Gestión** | Totalmente serverless | Requiere gestión de shards en modo provisionado |

### 7.5. Casos de Uso Comunes

#### 7.5.1. Almacenamiento de Datos de Streaming
- Cargar flujos continuos de datos en Amazon S3 para análisis posterior.
- Crear lagos de datos (data lakes) con información en tiempo real.

#### 7.5.2. Análisis de Datos
- Enviar datos a Amazon Redshift para análisis de negocios.
- Cargar datos en OpenSearch para búsquedas y visualizaciones en tiempo real.

#### 7.5.3. Monitorización y Observabilidad
- Enviar logs y métricas a servicios como Datadog, Splunk o New Relic.
- Centralizar datos operacionales para monitoreo.

#### 7.5.4. ETL en Tiempo Real
- Transformar y normalizar datos antes de almacenarlos.
- Convertir formatos de datos para optimizar el almacenamiento y el rendimiento de consultas.

---

## Resumen para el examen

### Conceptos Clave de Amazon Data Firehose
- **Propósito principal**: Cargar datos de streaming en destinos específicos.
- **Palabra clave**: "Near real-time" (casi en tiempo real) indica que Amazon Data Firehose podría ser la solución adecuada.
- **Gestión**: Servicio completamente administrado y serverless.
- **Escalamiento**: Automático, sin necesidad de configuración.

### Fuentes y Destinos
- **Fuentes**: Aplicaciones (mediante SDK), Kinesis Agent, Kinesis Data Streams, CloudWatch, IoT.
- **Destinos AWS**: S3, Redshift, OpenSearch.
- **Destinos de terceros**: Datadog, Splunk, New Relic, MongoDB.
- **Destinos personalizados**: Cualquier endpoint HTTP.

### Transformación y Formato
- **Transformación**: Mediante Lambda para conversiones personalizadas.
- **Formatos de entrada**: CSV, JSON, Parquet, Avro, texto, binario.
- **Conversiones posibles**: A Parquet, ORC con compresión gzip o snappy.

### Diferencias con Kinesis Data Streams
- **Firehose**: Carga de datos a destinos, near real-time, sin almacenamiento, sin replay.
- **Kinesis Data Streams**: Recolección de datos, real-time, almacenamiento hasta 365 días, con replay.

**Consejo para el examen**: Cuando veas preguntas sobre carga de datos de streaming en servicios como S3, Redshift u OpenSearch, especialmente con el término "near real-time", considera Amazon Data Firehose como la solución más probable. Si la pregunta menciona la necesidad de desarrollo mínimo o la ausencia de código personalizado, Firehose tiene ventaja sobre Kinesis Data Streams.


## 8. Kinesis Tuning and Troubleshooting

En los entornos de producción, los servicios de streaming como Amazon Kinesis pueden enfrentar diversos desafíos de rendimiento. Esta sección aborda las estrategias de ajuste y solución de problemas comunes que pueden aparecer en el examen AWS Certified Machine Learning - Associate.

### 8.1. Problemas de Rendimiento en Productores (Producers)

#### 8.1.1. Escrituras Lentas (Slow Writes)

Cuando los productores experimentan latencia alta al escribir datos en Kinesis Data Streams, pueden existir varias causas:

##### 8.1.1.1. Límites de Servicio
- **Señales**: Excepciones de throughput (ProvisionedThroughputExceededException)
- **Verificación**: Revisar los logs para identificar excepciones específicas
- **Solución**: 
  - Asegurarse de respetar los límites a nivel de shard (1 MB/s o 1,000 registros por segundo)
  - Verificar límites a nivel de stream para operaciones como CreateStream, ListStreams y DescribeStreams (5-20 llamadas por segundo)

##### 8.1.1.2. Hot Shards (Sesgo de Datos)
- **Definición**: Distribución desigual de datos entre shards, causando sobrecarga en algunos
- **Señales**: Algunos shards con alta utilización mientras otros están infrautilizados
- **Solución**: 
  - Implementar una mejor estrategia de partition key
  - Considerar usar keys aleatorias para mejor distribución
  - Utilizar enhanced monitoring para identificar shards sobrecargados

##### 8.1.1.3. Optimización de Productores Grandes
- **Técnica**: Agrupación de registros (batching)
- **Implementación**: 
  - Utilizar Kinesis Producer Library (KPL) con PutRecords API
  - Agregar múltiples registros pequeños en registros más grandes
  - Consolidar datos en archivos más grandes antes de enviarlos

##### 8.1.1.4. Productores Pequeños (Aplicaciones)
- **Recomendación**: También usar PutRecords para batching
- **Herramienta adicional**: Kinesis Recorder en AWS Mobile SDK para batching automático

#### 8.1.2. Errores Comunes en Productores

##### 8.1.2.1. Errores 500/503
- **Causa**: Excepciones de Amazon Kinesis superiores al 1% del tiempo
- **Solución**: Implementar mecanismo de reintento (retry mechanism)

##### 8.1.2.2. Errores de Conexión desde Flink a Kinesis
- **Posibles causas**:
  - Problemas de red
  - Recursos insuficientes en el entorno Flink
  - Configuración incorrecta de VPC
- **Soluciones**:
  - Verificar configuración de red y subredes
  - Implementar VPC peering si es necesario
  - Aumentar recursos asignados a Flink

##### 8.1.2.3. Errores de Timeout desde Flink a Kinesis
- **Soluciones**:
  - Aumentar el timeout con la configuración request.timeout
  - Establecer un límite de cola más alto en el productor Flink Kinesis

##### 8.1.2.4. Errores de Throttling
- **Técnicas de diagnóstico**:
  - Verificar hot shards usando enhanced monitoring
  - Buscar "micro-spikes" (picos repentinos de tráfico) en los logs
  - Revisar métricas que superan sus límites
- **Soluciones**:
  - Probar diferentes estrategias de particionamiento
  - Implementar backoff exponencial
  - Establecer límites de tasa (rate limiting) para evitar superar la capacidad

### 8.2. Problemas de Rendimiento en Consumidores (Consumers)

#### 8.2.1. Problemas con Kinesis Client Library (KCL)

##### 8.2.1.1. Registros Saltados
- **Causa**: Excepciones no manejadas en el método processRecords
- **Solución**: Implementar manejo de excepciones adecuado

##### 8.2.1.2. Registros Procesados por Múltiples Procesadores
- **Causa**: Está ocurriendo failover en los record processor workers
- **Soluciones**:
  - Ajustar el tiempo de failover
  - Manejar métodos de shutdown con motivo "ZOMBIE"

##### 8.2.1.3. Lecturas Lentas (Slow Reads)
- **Soluciones**:
  - Aumentar el número de shards
  - Revisar si maxRecordsPerCall está configurado muy bajo
  - Verificar si el código del consumidor es demasiado lento (comparar con un procesador vacío)

##### 8.2.1.4. GetRecords Devolviendo Resultados Vacíos
- **Comportamiento**: Es normal, los datos podrían no estar listos
- **Acción**: Continuar llamando a GetRecords

##### 8.2.1.5. Expiración Inesperada del Shard Iterator
- **Causa**: Capacidad de escritura insuficiente en la tabla DynamoDB subyacente
- **Solución**: Aumentar la capacidad de escritura de la tabla DynamoDB

##### 8.2.1.6. Procesamiento de Registros Rezagado
- **Acción temporal**: Aumentar el período de retención
- **Solución real**: Identificar y resolver limitaciones de recursos
- **Métricas a monitorear**: 
  - GetRecords.IteratorAgeMilliseconds
  - MillisBehindLatest

#### 8.2.2. Problemas con Funciones Lambda como Consumidores

##### 8.2.2.1. Función Lambda No Se Invoca
- **Causa común**: Problemas de permisos
- **Solución**: Verificar los permisos IAM del rol de ejecución de Lambda

##### 8.2.2.2. Timeout de Función Lambda
- **Solución**: Aumentar el tiempo máximo de ejecución de la función Lambda

##### 8.2.2.3. Límites de Concurrencia Superados
- **Acción**: Revisar y ajustar los límites de concurrencia
- **Monitoreo**: Vigilar la métrica Iterator Age para detectar rezagos

#### 8.2.3. Excepciones y Errores Comunes en Consumidores

##### 8.2.3.1. ReadProvisionedThroughputExceededException
- **Causa**: Throttling en las operaciones de lectura
- **Soluciones**:
  - Reorganizar shards (reshard) del stream
  - Reducir el tamaño de las solicitudes GetRecords
  - **Utilizar Enhanced Fan-Out** (importante para el examen)
  - Implementar reintentos con backoff exponencial

##### 8.2.3.2. Alta Latencia en Consumidores
- **Métricas a monitorear**:
  - GetRecords Latency
  - Iterator Age
- **Soluciones**:
  - Aumentar el número de shards
  - Incrementar el período de retención
  - Verificar utilización de CPU y memoria (podría necesitar más recursos)

##### 8.2.3.3. Errores 500
- **Causa**: Tasa de excepciones superior al 1%
- **Solución**: Implementar mecanismo de reintento

##### 8.2.3.4. Aplicación KCL Bloqueada o Atascada
- **Soluciones**:
  - Optimizar el método processRecords para hacerlo más rápido
  - Aumentar maxLeasesPerWorker
  - Habilitar logs de depuración de KCL para investigar en profundidad

### 8.3. Estrategias Generales de Optimización

#### 8.3.1. Optimización del Partition Key
- **Importancia**: Clave para evitar hot shards y distribuir datos uniformemente
- **Estrategias**:
  - Usar claves con buena distribución
  - Considerar claves aleatorias si es apropiado
  - Evitar usar claves que resulten en agrupaciones de datos en pocos shards

#### 8.3.2. Ajuste de Capacidad
- **En modo provisionado**:
  - Monitorear y ajustar el número de shards según la demanda
  - Provisionar con margen para picos de tráfico
- **En modo bajo demanda**:
  - Monitorear costos y rendimiento

#### 8.3.3. Uso Efectivo del Batching
- **Beneficios**: Reduce el número de llamadas a la API, mejora el throughput
- **Implementación**:
  - Agrupar registros cuando sea posible
  - Considerar el balance entre latencia y throughput

#### 8.3.4. Mecanismos de Resiliencia
- **Retry mechanism**: Implementar reintentos para transacciones fallidas
- **Exponential backoff**: Aumentar gradualmente el tiempo entre reintentos
- **Rate limiting**: Establecer límites de tasa para evitar sobrecarga

---

## Resumen para el examen

### Problemas de Productores y Soluciones
- **Escrituras lentas**: Verificar límites de servicio, hot shards, implementar batching
- **Errores 500/503**: Implementar mecanismos de reintento
- **Problemas de conexión**: Revisar configuración de red/VPC
- **Throttling**: Mejorar estrategia de partition key, usar backoff exponencial, implementar rate limiting

### Problemas de Consumidores y Soluciones
- **Registros saltados**: Manejar excepciones en processRecords
- **Múltiples procesadores**: Ajustar tiempo de failover, manejar shutdowns ZOMBIE
- **Lecturas lentas**: Aumentar shards, optimizar código del consumidor
- **ReadProvisionedThroughputExceeded**: Usar Enhanced Fan-Out, reshard, implementar backoff
- **Lambda no se invoca**: Verificar permisos IAM
- **Procesamiento rezagado**: Monitorear Iterator Age, aumentar recursos

### Métricas Clave para Monitoreo
- **GetRecords.IteratorAgeMilliseconds**: Indica cuánto se está rezagando el procesamiento
- **MillisBehindLatest**: Muestra la diferencia temporal entre el último registro procesado y el más reciente
- **GetRecords Latency**: Tiempo que toma obtener registros del stream

**Consejo para el examen**: Presta especial atención a las preguntas sobre solución de problemas que mencionen "Enhanced Fan-Out" como solución para problemas de throughput en consumidores, y a las preguntas relacionadas con "hot shards" que sugieran cambiar la estrategia de partition key como solución.


## 9. Amazon Managed Service for Apache Flink

Amazon Managed Service for Apache Flink es un servicio que permite procesar y analizar datos de streaming en tiempo real, utilizando Apache Flink como tecnología subyacente. Este servicio representa la evolución de las capacidades de procesamiento de streaming de AWS.

### 9.1. Evolución del Procesamiento de Streaming en AWS

#### 9.1.1. Historia y Desarrollo
- **Antecedente**: Amazon Kinesis Data Analytics fue el servicio original para procesamiento de streaming.
- **Evolución del nombre**:
  1. Kinesis Data Analytics (original)
  2. Kinesis Data Analytics for SQL Applications (enfoque en SQL)
  3. Kinesis Data Analytics for Java (versión orientada a Java)
  4. Kinesis Data Analytics for Apache Flink
  5. Amazon Managed Service for Apache Flink (nombre actual)
- **Tendencia en AWS**: Mayor transparencia sobre las tecnologías de código abierto subyacentes.

#### 9.1.2. Kinesis Data Analytics for SQL Applications
- **Funcionalidad**: Permite aplicar operaciones SQL a streams de datos.
- **Estado actual**: Todavía disponible pero probablemente en camino a ser obsoleto.
- **Característica principal**: Transformaciones SQL simples sobre datos de streaming.

### 9.2. Arquitectura de Kinesis Data Analytics for SQL

#### 9.2.1. Componentes Principales
- **Fuentes de entrada**:
  - Kinesis Data Streams
  - Kinesis Data Firehose
  - Tablas de referencia en Amazon S3

- **Procesamiento**:
  - Aplicación de consultas SQL a los datos entrantes
  - Transformación de datos en tiempo real

- **Destinos de salida**:
  - Kinesis Data Streams
  - Kinesis Data Firehose
  - Streams de error para datos que no cumplen con las reglas

#### 9.2.2. Tablas de Referencia
- **Propósito**: Permitir búsquedas rápidas y joins de datos externos.
- **Almacenamiento**: Amazon S3.
- **Ejemplo de uso**: Mapeo de códigos postales a ciudades para enriquecer datos entrantes.
- **Ventaja**: Solución simple y económica para realizar joins con datos estáticos.

#### 9.2.3. Integración con AWS Lambda
- **Capacidad**: Los resultados pueden enviarse a funciones Lambda.
- **Ventajas**: Mayor flexibilidad en el post-procesamiento.
- **Operaciones posibles**:
  - Agregación de filas
  - Traducción a otros formatos
  - Transformaciones adicionales
  - Enriquecimiento de datos
  - Cifrado
  - Integración con otros servicios de AWS

### 9.3. Amazon Managed Service for Apache Flink

#### 9.3.1. ¿Qué es Apache Flink?
- **Definición**: Framework de código abierto para procesamiento de datos de streaming.
- **Característica clave**: Diseñado específicamente para procesamiento de flujos de datos con baja latencia.
- **Relación con Kinesis**: Siempre fue la tecnología subyacente de Kinesis Data Analytics.

#### 9.3.2. Características Principales
- **Servicio serverless**: Completamente administrado por AWS.
- **Soporte de lenguajes**: Java, Python y Scala.
- **APIs disponibles**:
  - **DataStream API**: Para procesamiento de datos de streaming puro.
  - **Table API**: Para acceso SQL a datos de streaming.
- **Implementación**: Las aplicaciones Flink se desarrollan localmente y se cargan a Amazon S3.

#### 9.3.3. Arquitectura y Flujo de Datos

##### 9.3.3.1. Fuentes (Flink Sources)
- **Kinesis Data Streams**: Flujos de datos en tiempo real.
- **Amazon MSK (Managed Streaming for Apache Kafka)**: Alternativa para streaming basada en Kafka.

##### 9.3.3.2. Procesamiento
- **Aplicación Flink**: Código personalizado alojado y ejecutado por el servicio.
- **Origen del código**: La aplicación se almacena en S3 y se carga en el servicio.
- **Tipos de procesamiento**: Transformación, agregación, enriquecimiento, análisis.

##### 9.3.3.3. Destinos (Flink Sinks)
- **Amazon S3**: Para almacenamiento persistente.
- **Kinesis Data Streams**: Para procesamiento adicional.
- **Kinesis Data Firehose**: Para entrega a otros servicios de AWS.
- **Otros servicios AWS**: A través de conectores específicos.

### 9.4. Casos de Uso

#### 9.4.1. ETL en Streaming (Streaming ETL)
- **Definición**: Extracción, transformación y carga de datos en tiempo real.
- **Ventaja**: Procesar datos sobre la marcha, sin necesidad de almacenarlos primero.
- **Aplicación**: Transformar datos para análisis inmediato o almacenamiento optimizado.

#### 9.4.2. Generación Continua de Métricas
- **Propósito**: Monitoreo en tiempo real de indicadores clave.
- **Ejemplo**: Seguimiento de KPIs operativos, métricas de negocio.
- **Valor**: Detección temprana de problemas o oportunidades.

#### 9.4.3. Analítica Responsiva
- **Definición**: Análisis en tiempo real para respuestas inmediatas.
- **Ejemplos**: 
  - Analítica de tráfico web en tiempo real
  - Detección de fraude
  - Sistemas de recomendación dinámicos
- **Diferenciador**: Capacidad de respuesta inmediata basada en datos actuales.

### 9.5. Comparación: Kinesis Data Analytics for SQL vs. Managed Service for Apache Flink

| Característica | Kinesis Data Analytics for SQL | Managed Service for Apache Flink |
|----------------|--------------------------------|----------------------------------|
| **Enfoque** | Transformaciones SQL simples | Framework completo de procesamiento de streaming |
| **Lenguajes** | SQL | Java, Python, Scala |
| **Flexibilidad** | Limitada a operaciones SQL | Alta (código personalizado completo) |
| **Complejidad** | Baja (solo SQL) | Media a alta (requiere desarrollo de aplicaciones) |
| **Integración** | Principalmente Kinesis | Amplia (Kinesis, MSK, S3, etc.) |
| **Estado actual** | Activo pero probable obsolescencia | Activo y en desarrollo |

### 9.6. Consideraciones de Implementación

#### 9.6.1. Desarrollo de Aplicaciones
- Desarrollar la aplicación Flink localmente.
- Probar exhaustivamente antes de desplegar.
- Cargar el JAR o código fuente en un bucket S3.
- Configurar el servicio para utilizar el código desde S3.

#### 9.6.2. Escalabilidad
- El servicio se escala automáticamente.
- Capacidad de manejar eventos de alta carga.
- Gestión transparente de recursos subyacentes.

#### 9.6.3. Monitoreo
- Integración con Amazon CloudWatch para métricas.
- Logs disponibles para diagnóstico.
- Alertas configurables para situaciones anómalas.

---

## Resumen para el examen

### Evolución de Servicios
- **Historia**: Kinesis Data Analytics → Managed Service for Apache Flink
- **Tecnología subyacente**: Apache Flink ha sido siempre la base de estos servicios

### Kinesis Data Analytics for SQL
- **Funcionalidad**: Aplicar SQL a streams de datos
- **Característica distintiva**: Tablas de referencia en S3 para joins simples
- **Estado**: Aún disponible pero probablemente será obsoleto

### Amazon Managed Service for Apache Flink
- **Naturaleza**: Servicio serverless para aplicaciones Flink
- **Lenguajes**: Java, Python, Scala
- **APIs**: DataStream API (para streaming) y Table API (para SQL)
- **Implementación**: Desarrollo local → S3 → Servicio administrado

### Fuentes y Destinos
- **Fuentes**: Kinesis Data Streams, MSK (Kafka)
- **Destinos**: S3, Kinesis Data Streams, Kinesis Data Firehose, otros servicios

### Casos de Uso Principales
- **Streaming ETL**: Procesamiento en tiempo real
- **Métricas continuas**: Monitoreo en tiempo real
- **Analítica responsiva**: Análisis inmediato para toma de decisiones

**Consejo para el examen**: Si ves preguntas sobre procesamiento avanzado de streams que requieren más que simples transformaciones SQL, o que mencionan múltiples lenguajes de programación (Java, Python, Scala), la respuesta probablemente sea Amazon Managed Service for Apache Flink, no Kinesis Data Analytics for SQL.


## 10. Kinesis Analytics Costs y RANDOM_CUT_FOREST

Esta sección aborda aspectos importantes sobre el modelo de costos de Amazon Kinesis Analytics y una de sus funcionalidades más destacadas: RANDOM_CUT_FOREST para detección de anomalías.

### 10.1. Modelo de Costos de Kinesis Analytics

#### 10.1.1. Naturaleza Serverless
- **Definición**: Como servicio serverless, Kinesis Analytics no requiere la gestión de infraestructura subyacente.
- **Pago por uso**: Solo se paga por los recursos consumidos durante la ejecución de las aplicaciones.
- **Escalado automático**: El servicio escala automáticamente según las necesidades sin intervención manual.

#### 10.1.2. Consideraciones de Costo
- **Nivel de precio**: A pesar de su naturaleza serverless, Kinesis Analytics no es económico comparado con otros servicios serverless de AWS.
- **Impacto potencial**: Puede representar una parte significativa de los costos de AWS en entornos de procesamiento intensivo de datos.
- **Recomendación importante**: Apagar las aplicaciones de Kinesis Analytics cuando no estén en uso para evitar cargos innecesarios.

#### 10.1.3. Factores que Influyen en el Costo
- **Volumen de datos procesados**: Mayor cantidad de datos significa mayor costo.
- **Complejidad de las consultas SQL**: Consultas más complejas pueden requerir más recursos.
- **Tiempo de ejecución**: Las aplicaciones se facturan por el tiempo que están activas, independientemente del volumen de datos procesados.

### 10.2. Seguridad en Kinesis Analytics

#### 10.2.1. Gestión de Permisos
- **Integración con IAM**: Uso de permisos IAM para controlar el acceso a servicios de origen y destino.
- **Configuración**: Se pueden configurar políticas específicas para permitir que las aplicaciones de Kinesis Analytics se comuniquen con servicios upstream y downstream.
- **Modelo de seguridad**: Sigue el principio de privilegio mínimo, otorgando solo los permisos necesarios.

### 10.3. Schema Discovery

#### 10.3.1. ¿Qué es Schema Discovery?
- **Definición**: Funcionalidad que analiza automáticamente los datos entrantes para inferir un esquema.
- **Propósito**: Facilita la configuración inicial de columnas SQL para su aplicación de Kinesis Analytics.

#### 10.3.2. Funcionamiento
- **Análisis automático**: Examina muestras de datos del stream de entrada.
- **Inferencia de esquema**: Determina tipos de datos y nombres de columnas basados en los patrones observados.
- **Visualización en consola**: Permite ver en tiempo real cómo el servicio interpreta los datos entrantes.

#### 10.3.3. Personalización
- **Edición manual**: Posibilidad de corregir o ajustar el esquema inferido según sea necesario.
- **Validación**: Permite verificar que el esquema detectado es correcto antes de implementar la aplicación.

### 10.4. RANDOM_CUT_FOREST para Detección de Anomalías

#### 10.4.1. ¿Qué es RANDOM_CUT_FOREST?
- **Definición**: Función SQL especializada disponible en Kinesis Data Analytics.
- **Propósito**: Detección de anomalías en tiempo real sobre columnas numéricas en un stream de datos.
- **Base científica**: Algoritmo respaldado por publicaciones académicas de AWS.

#### 10.4.2. Funcionamiento del Algoritmo
- **Enfoque**: Método novedoso para identificar valores atípicos (outliers) en conjuntos de datos.
- **Análisis en tiempo real**: Detecta anomalías a medida que los datos fluyen a través del stream.
- **Aplicación**: Funciona sobre cualquier columna numérica dentro del stream de datos.

#### 10.4.3. Implementación en SQL
- **Uso como función SQL**: Se integra directamente en las consultas SQL de Kinesis Analytics.
- **Sintaxis básica**: RANDOM_CUT_FOREST(columna_numérica)
- **Resultado**: Devuelve una puntuación de anomalía que puede utilizarse para identificar valores atípicos.

#### 10.4.4. Caso de Uso Práctico
- **Ejemplo documentado**: Detección de anomalías en el uso del metro de Nueva York durante el Maratón.
- **Escenario**: Monitoreo del flujo de pasajeros a través de torniquetes para identificar patrones inusuales.
- **Valor**: Permite identificar automáticamente comportamientos anómalos sin establecer umbrales fijos.

### 10.5. Consideraciones para el Examen

#### 10.5.1. Puntos Clave sobre Costos
- **Naturaleza serverless**: No requiere gestión de infraestructura, pero se debe tener cuidado con los costos.
- **Mejores prácticas**: Apagar las aplicaciones cuando no estén en uso para optimizar costos.

#### 10.5.2. Detección de Anomalías
- **RANDOM_CUT_FOREST**: Función específica para detección de anomalías en streams de datos.
- **Aplicabilidad**: Ideal para escenarios que requieren identificación de valores atípicos en tiempo real.
- **Relevancia para el examen**: Solución recomendada para preguntas sobre detección de anomalías en streams.

---

## Resumen para el examen

### Modelo de Costos de Kinesis Analytics
- **Serverless**: Pago por uso sin gestión de infraestructura
- **Costo relativamente alto**: Más costoso que otros servicios serverless
- **Recomendación crítica**: Apagar aplicaciones cuando no estén en uso

### Seguridad
- **IAM**: Control de acceso a servicios de origen y destino
- **Permisos**: Configurables para interacción con otros servicios AWS

### Schema Discovery
- **Función**: Detección automática de esquema de datos
- **Utilidad**: Facilita la configuración inicial de aplicaciones
- **Personalización**: Editable manualmente desde la consola de AWS

### RANDOM_CUT_FOREST
- **Propósito**: Detección de anomalías en tiempo real
- **Implementación**: Función SQL nativa en Kinesis Analytics
- **Aplicación**: Cualquier columna numérica en un stream de datos
- **Caso típico**: Identificación de comportamientos atípicos en métricas operativas

**Consejo para el examen**: Si encuentras preguntas relacionadas con la detección de anomalías, outliers o comportamientos inusuales en streams de datos, RANDOM_CUT_FOREST en Kinesis Analytics es probablemente la respuesta correcta. Esta función está específicamente diseñada para este propósito y AWS la destaca como una solución innovadora para estos casos de uso.


## 11. Amazon MSK (Managed Streaming for Apache Kafka)

Amazon Managed Streaming for Apache Kafka (MSK) es un servicio completamente administrado que facilita la creación y operación de aplicaciones que utilizan Apache Kafka para el procesamiento de datos de streaming. Este servicio proporciona una alternativa a Amazon Kinesis Data Streams con características específicas y mayor flexibilidad de configuración.

### 11.1. Conceptos Fundamentales de Amazon MSK

#### 11.1.1. ¿Qué es Amazon MSK?
- **Definición**: Servicio completamente administrado para Apache Kafka en AWS.
- **Funcionalidad**: Permite crear, actualizar y eliminar clústeres de Kafka sin gestionar la infraestructura subyacente.
- **Gestión automática**: AWS administra los nodos de broker de Kafka y los nodos de ZooKeeper.

#### 11.1.2. Comparación con Kinesis
- **Arquitectura similar**: Ambos manejan streams de datos con productores y consumidores.
- **Diferencias clave**:
  - MSK ofrece más opciones de configuración personalizada.
  - MSK permite mensajes más grandes (hasta 10 MB con configuración personalizada vs. 1 MB límite fijo en Kinesis).
  - MSK utiliza APIs y herramientas específicas de Kafka.

### 11.2. Arquitectura de Amazon MSK

#### 11.2.1. Componentes Principales
- **Brokers de Kafka**: Nodos que almacenan y procesan los datos de streaming.
- **Nodos ZooKeeper**: Gestionan la coordinación del clúster y mantienen estados compartidos.
- **Topics de Kafka**: Canales donde se publican los mensajes, similar a los streams en Kinesis.
- **Particiones**: Divisiones de los topics para permitir paralelismo.

#### 11.2.2. Flujo de Datos
1. **Productores**: Aplicaciones que envían datos a los topics de Kafka.
2. **Replicación**: Los datos se replican entre brokers para alta disponibilidad.
3. **Consumidores**: Aplicaciones que leen datos de los topics para procesamiento.
4. **Destinos**: Los datos procesados pueden enviarse a diversos servicios como EMR, S3, SageMaker, etc.

#### 11.2.3. Configuración del Clúster
- **Zonas de disponibilidad**: Se recomienda 2-3 AZs para alta disponibilidad.
- **Tipo de instancia de broker**: Por ejemplo, m5.large.
- **Número de brokers por AZ**: Configurable y escalable con el tiempo.
- **Volumen EBS**: Entre 1 GB y 16 TB, permite retención flexible de datos.
- **VPC y subredes**: MSK se despliega dentro de una VPC en subredes privadas.

### 11.3. Seguridad en Amazon MSK

#### 11.3.1. Encriptación
- **En tránsito**: 
  - Entre brokers: TLS habilitado por defecto (puede deshabilitarse).
  - Entre clientes y brokers: TLS opcional (habilitado por defecto).
- **En reposo**: Encriptación de volúmenes EBS mediante AWS KMS.

#### 11.3.2. Seguridad de Red
- **Security Groups**: Se pueden adjuntar a clientes Kafka para controlar el tráfico.
- **VPC**: El clúster se despliega en una VPC privada.

#### 11.3.3. Autenticación y Autorización
- **Mutual TLS (AuthN)**: 
  - Utiliza certificados TLS para encriptación y autenticación.
  - Requiere Kafka ACLs para autorización a nivel de topic.

- **SASL/SCRAM**: 
  - Sistema de autenticación basado en usuario/contraseña.
  - Requiere Kafka ACLs para autorización.

- **IAM Access Control**: 
  - Utiliza políticas IAM para autenticación y autorización.
  - Similar al control de acceso en Kinesis Data Streams.

- **Nota importante**: Las ACLs de Kafka para Mutual TLS y SASL/SCRAM no pueden gestionarse mediante políticas IAM, deben definirse dentro del clúster Kafka.

### 11.4. Monitorización en Amazon MSK

#### 11.4.1. CloudWatch Metrics
- **Monitorización básica**: Métricas a nivel de clúster y broker.
- **Monitorización mejorada**: Métricas adicionales de broker y a nivel de topic.
- **Monitorización a nivel de topic**: Métricas detalladas para cada stream de datos.

#### 11.4.2. Prometheus
- **Monitorización de código abierto**: Extracción de datos mediante JMX Exporter o Node Exporter.
- **Protocolo Prometheus**: Estándar de la industria para monitorización.

#### 11.4.3. Envío de Logs de Broker
- Pueden enviarse a:
  - CloudWatch Logs
  - Amazon S3
  - Kinesis Data Streams

### 11.5. MSK Connect

#### 11.5.1. ¿Qué es MSK Connect?
- **Definición**: Implementación administrada del framework Kafka Connect.
- **Propósito**: Facilitar la integración de datos entre Kafka y otros sistemas.
- **Características principales**:
  - Workers de Kafka Connect gestionados por AWS.
  - Capacidades de auto-escalado.
  - Compatibilidad con conectores de Kafka existentes.

#### 11.5.2. Funcionamiento
- **Despliegue de workers**: AWS gestiona la infraestructura.
- **Plugins de conectores**: Se implementan como plugins en MSK Connect.
- **Conexiones disponibles**: Amazon S3, Redshift, OpenSearch, Debezium, entre otros.

#### 11.5.3. Ejemplo de Flujo
1. Se establece un clúster MSK.
2. Se crean workers de MSK Connect.
3. Se implementa un conector (por ejemplo, conector S3).
4. Los workers extraen datos de topics de MSK.
5. Los datos se escriben automáticamente en el destino (S3 en este ejemplo).

#### 11.5.4. Modelo de Precios
- Aproximadamente $0.11 por worker por hora.
- El número de workers escala según el volumen de datos.

### 11.6. MSK Serverless

#### 11.6.1. Concepto y Características
- **Definición**: Versión sin servidor de Amazon MSK.
- **Diferencia clave**: No requiere provisionar ni gestionar capacidad.
- **Escalado automático**: MSK aprovisiona y escala recursos automáticamente (cómputo y almacenamiento).

#### 11.6.2. Simplificación Operativa
- **Responsabilidad del usuario**: Solo definir topics y número de particiones.
- **Gestión de recursos**: Totalmente gestionado por AWS.

#### 11.6.3. Seguridad
- **Control de acceso**: IAM Access Control para todos los clústeres.

#### 11.6.4. Modelo de Precios
- **Cargo por clúster**: Aproximadamente $0.75 por clúster por hora ($558/mes).
- **Cargo por partición**: Aproximadamente $1 por partición por mes.
- **Otros cargos**: Almacenamiento, datos de entrada y datos de salida.

### 11.7. Casos de Uso y Selección entre MSK y Kinesis

#### 11.7.1. Escenarios Ideales para MSK
- **Mensajes grandes**: Cuando se necesitan mensajes mayores a 1 MB.
- **Configuraciones personalizadas**: Para casos que requieren ajustes específicos de Kafka.
- **Ecosistema Kafka**: Cuando ya se utiliza Kafka en la organización o se requieren herramientas específicas del ecosistema.
- **Retención de datos flexible**: Para casos que necesitan retención prolongada basada en volumen en lugar de tiempo.

#### 11.7.2. Consideraciones de Selección
- **Experiencia del equipo**: Si el equipo ya conoce Kafka, MSK puede ser más adecuado.
- **Complejidad vs. Simplicidad**: Kinesis es generalmente más simple; MSK ofrece más flexibilidad pero mayor complejidad.
- **Integración con servicios AWS**: Ambos se integran bien, pero Kinesis tiene integración nativa con más servicios AWS.
- **Modelo de costos**: Comparar el modelo basado en capacidad (MSK) versus el basado en uso (Kinesis).

---

## Resumen para el examen

### Conceptos Clave de Amazon MSK
- **Definición**: Servicio completamente administrado para Apache Kafka en AWS.
- **Diferencias con Kinesis**: Mayor configurabilidad, soporte para mensajes más grandes (hasta 10 MB con configuración).
- **Arquitectura**: Brokers de Kafka, ZooKeeper, Topics y Particiones como componentes centrales.

### Seguridad en MSK
- **Encriptación**: En tránsito (TLS) y en reposo (KMS).
- **Autenticación**: Tres opciones principales:
  - Mutual TLS + Kafka ACLs
  - SASL/SCRAM + Kafka ACLs
  - IAM Access Control
- **Importante**: Las Kafka ACLs no pueden gestionarse mediante IAM, requieren configuración dentro del clúster.

### Funcionalidades Adicionales
- **MSK Connect**: Integración administrada con otros servicios mediante Kafka Connect.
- **MSK Serverless**: Versión sin servidor que gestiona automáticamente el aprovisionamiento y escalado.

### Monitorización
- **CloudWatch**: Tres niveles (básico, mejorado, nivel de topic).
- **Prometheus**: Para monitorización de código abierto.
- **Logs**: Pueden enviarse a CloudWatch, S3 o Kinesis.

**Consejo para el examen**: El examen probablemente presentará escenarios donde deberás elegir entre Kinesis y MSK. Recuerda que MSK es preferible cuando: 1) se necesitan mensajes más grandes, 2) se requiere alta configurabilidad, 3) ya se está utilizando el ecosistema Kafka, 4) se necesita una retención de datos personalizable basada en volumen en lugar de tiempo.


## 12. Amazon Kinesis vs. Amazon MSK

En el examen AWS Certified Machine Learning - Associate, es probable que debas elegir entre Amazon Kinesis Data Streams y Amazon MSK (Managed Streaming for Apache Kafka) basándote en diferentes escenarios. Si bien Kinesis Data Streams suele ser la opción preferida por AWS, existen casos específicos donde MSK es la solución más adecuada. Esta sección analiza las diferencias clave entre ambos servicios.

### 12.1. Comparación General

| Característica | Amazon Kinesis Data Streams | Amazon MSK |
|----------------|------------------------------|------------|
| **Tecnología** | Servicio propietario de AWS | Servicio administrado para Apache Kafka (open source) |
| **Terminología** | Streams con Shards | Topics con Particiones |
| **Mensaje máximo** | 1 MB (límite fijo) | 1 MB (por defecto), configurable hasta 10 MB |
| **Escalabilidad** | División y fusión de shards | Solo se pueden añadir particiones |
| **Encriptación** | TLS en tránsito (obligatorio) | TLS en tránsito (opcional) o PLAINTEXT |
| **Seguridad** | IAM para autenticación y autorización | Múltiples opciones (detalladas abajo) |

### 12.2. Tamaño Máximo de Mensaje

#### 12.2.1. Kinesis Data Streams
- **Límite estricto**: 1 MB por mensaje.
- **Flexibilidad**: No es posible superar este límite bajo ninguna circunstancia.
- **Implicación**: Para mensajes grandes, se requiere fragmentación a nivel de aplicación.

#### 12.2.2. Amazon MSK
- **Valor predeterminado**: 1 MB por mensaje.
- **Configurable**: Se puede aumentar hasta 10 MB con configuración personalizada.
- **Caso de uso clave**: Procesamiento de mensajes grandes sin fragmentación.

**Nota para el examen**: Si un escenario menciona la necesidad de manejar mensajes más grandes de 1 MB, descarta automáticamente Kinesis Data Streams y considera Amazon MSK como la respuesta correcta.

### 12.3. Estructura y Escalabilidad

#### 12.3.1. Kinesis Data Streams
- **Estructura**: Data Streams divididos en Shards.
- **Escalado**: 
  - Permite división de shards (shard splitting) para aumentar capacidad.
  - Permite fusión de shards (shard merging) para reducir capacidad.
  - Ajuste flexible de throughput en ambas direcciones.

#### 12.3.2. Amazon MSK
- **Estructura**: Kafka Topics divididos en Particiones.
- **Escalado**:
  - Solo permite añadir particiones a un topic.
  - No es posible eliminar particiones de un topic existente.
  - Escalado más limitado y unidireccional.

### 12.4. Encriptación

#### 12.4.1. Kinesis Data Streams
- **En tránsito**: TLS (Transport Layer Security) habilitado por defecto y obligatorio.
- **En reposo**: Encriptación mediante AWS KMS (Key Management Service).

#### 12.4.2. Amazon MSK
- **En tránsito**: Opciones disponibles:
  - TLS (habilitado por defecto, pero opcional).
  - PLAINTEXT (sin encriptación, para mayor rendimiento).
- **En reposo**: Encriptación mediante AWS KMS.

### 12.5. Seguridad y Control de Acceso

#### 12.5.1. Kinesis Data Streams
- **Mecanismo único**: Políticas IAM para autenticación y autorización.
- **Integración AWS**: Totalmente integrado con el modelo de seguridad estándar de AWS.

#### 12.5.2. Amazon MSK
- **Múltiples mecanismos**: Tres opciones disponibles:

1. **Opción 1**: 
   - **Autenticación**: Mutual TLS (certificados TLS).
   - **Autorización**: Kafka ACLs (Access Control Lists).

2. **Opción 2**:
   - **Autenticación**: SASL/SCRAM (usuario/contraseña).
   - **Autorización**: Kafka ACLs.

3. **Opción 3**:
   - **Autenticación y Autorización**: IAM Access Control (similar a Kinesis).

**Punto importante**: Las Kafka ACLs deben configurarse dentro del clúster Kafka y no pueden gestionarse mediante políticas IAM.

### 12.6. Criterios de Selección para el Examen

#### 12.6.1. Escenarios Favorables para Kinesis Data Streams
- Integración nativa con servicios AWS.
- Necesidad de escalado bidireccional (aumentar y reducir capacidad).
- Preferencia por un modelo de seguridad basado únicamente en IAM.
- Mensajes de tamaño moderado (menos de 1 MB).
- Administración más simple, menor curva de aprendizaje.

#### 12.6.2. Escenarios Favorables para Amazon MSK
- **Mensajes grandes**: Necesidad de procesar mensajes mayores a 1 MB.
- Ecosistema existente de Apache Kafka.
- Requisitos específicos de configuración que Kinesis no puede satisfacer.
- Necesidad de opciones de autenticación y autorización específicas (como Mutual TLS).
- Posibilidad de utilizar conexiones sin encriptación (PLAINTEXT) para maximizar rendimiento.

### 12.7. Evaluación de Escenarios Comunes

#### 12.7.1. Escenario: Mensajes grandes
- **Descripción**: Aplicación que necesita procesar mensajes multimedia de 5 MB.
- **Solución correcta**: Amazon MSK (Kinesis tiene límite de 1 MB).

#### 12.7.2. Escenario: Ecosistema Kafka existente
- **Descripción**: Migración de una aplicación on-premises que ya utiliza Apache Kafka.
- **Solución correcta**: Amazon MSK (compatibilidad directa con Kafka).

#### 12.7.3. Escenario: Seguridad con certificados
- **Descripción**: Requisito de seguridad para autenticación basada en certificados.
- **Solución correcta**: Amazon MSK con Mutual TLS.

---

## Resumen para el examen

### Diferencias Clave entre Kinesis Data Streams y MSK
- **Tamaño máximo de mensaje**: 
  - Kinesis: 1 MB (fijo)
  - MSK: Configurable hasta 10 MB

- **Estructura y terminología**:
  - Kinesis: Streams con Shards
  - MSK: Topics con Particiones

- **Escalabilidad**:
  - Kinesis: Permite dividir y fusionar shards
  - MSK: Solo permite añadir particiones

- **Encriptación en tránsito**:
  - Kinesis: TLS obligatorio
  - MSK: TLS opcional o PLAINTEXT

- **Seguridad**:
  - Kinesis: Solo IAM
  - MSK: Mutual TLS + Kafka ACLs, SASL/SCRAM + Kafka ACLs, o IAM Access Control

**Consejo para el examen**: Si un escenario menciona mensajes grandes (>1 MB), configuraciones personalizadas avanzadas, o requisitos específicos de seguridad como Mutual TLS, considera Amazon MSK como la respuesta correcta. De lo contrario, Kinesis Data Streams suele ser la opción preferida en el ecosistema AWS.