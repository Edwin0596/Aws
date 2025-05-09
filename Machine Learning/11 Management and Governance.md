# Management and Governance

## 1. Amazon CloudWatch - Metrics

Amazon CloudWatch proporciona métricas para todos los servicios de AWS, permitiéndote monitorear todo lo que sucede en tu cuenta. 

### 1.1 Conceptos básicos de CloudWatch Metrics

- **Métrica**: Una variable que quieres monitorear
  - Ejemplos: CPUUtilization y NetworkIn para EC2, tamaño del bucket para S3
- **Namespace**: Contenedor de métricas (hay uno por servicio)
- **Dimensiones**: Atributos de una métrica
  - Ejemplo: Una métrica de CPUUtilization puede estar relacionada con un ID de instancia específico
  - Puedes tener hasta 30 dimensiones por métrica
- **Temporalidad**: Todas las métricas están basadas en tiempo y deben tener una marca de tiempo

### 1.2 Características principales

- **CloudWatch Dashboard**: Te permite visualizar múltiples métricas en un solo lugar
- **CloudWatch Custom Metrics**: Puedes crear tus propias métricas personalizadas
  - Caso de uso común: Extraer el uso de memoria de una instancia EC2

### 1.3 Streaming de métricas

Puedes transmitir tus métricas de CloudWatch a destinos externos:

- **Entrega en tiempo real**: Transmisión continua con baja latencia
- **Destinos posibles**:
  - Amazon Kinesis Data Firehose (y desde ahí a cualquier otro destino)
  - Servicios de terceros como Datadog, Dynatrace, New Relic, Splunk, Sumo Logic

#### 1.3.1 Flujo de streaming

1. Las métricas de CloudWatch se transmiten en tiempo real a Kinesis Data Firehose
2. Desde Kinesis Data Firehose puedes enviarlas a:
   - Amazon S3 (para análisis con Amazon Athena)
   - Amazon Redshift (para data warehousing)
   - Amazon OpenSearch (para crear dashboards y análisis)
3. Puedes transmitir todas tus métricas o filtrar solo ciertos namespaces

### 1.4 Aspectos prácticos de CloudWatch Metrics

- Puedes ver métricas por servicio (EC2, EBS, ELB, etc.)
- Por defecto, las métricas se recopilan cada 5 minutos
- Con monitoreo detallado, obtienes métricas cada 1 minuto
- Puedes filtrar por tiempo, dimensión, ID de recurso
- Visualización flexible: gráficos de líneas, áreas apiladas, números, gráficos circulares
- Opciones para añadir métricas a dashboards, descargar como CSV o compartir

## 2. Amazon CloudWatch - Logs

CloudWatch Logs es el lugar ideal para almacenar logs de aplicaciones en AWS.

### 2.1 Estructura de CloudWatch Logs

- **Log Groups**: Representan una aplicación
- **Log Streams**: Representan instancias de logs dentro de una aplicación
  - Pueden ser archivos de logs específicos o contenedores dentro de un clúster
- **Política de expiración**: Puedes retener logs indefinidamente o configurarlos para que expiren entre 1 día y 10 años

### 2.2 Características de seguridad

- Todos los logs están cifrados por defecto
- Puedes configurar tu propio cifrado basado en KMS con tus propias claves

### 2.3 Fuentes de datos para CloudWatch Logs

Varios servicios pueden enviar logs directamente a CloudWatch Logs:

- AWS SDK o CloudWatch Logs Agent/Unified Agent
- Elastic Beanstalk (logs de aplicaciones)
- ECS (logs de contenedores)
- Lambda (logs de funciones)
- VPC Flow Logs (tráfico de red)
- API Gateway (solicitudes)
- CloudTrail (basado en filtros)
- Route53 (consultas DNS)

### 2.4 CloudWatch Logs Insights

CloudWatch Logs Insights es una capacidad de consulta dentro de CloudWatch Logs:

- Permite escribir consultas para analizar tus logs
- Especificas el marco de tiempo para la consulta
- Obtienes resultados y visualizaciones automáticamente
- Puedes ver las líneas de log específicas que generaron la visualización
- Las visualizaciones pueden exportarse o añadirse a un dashboard

#### 2.4.1 Características de CloudWatch Logs Insights

- Consultas simples predefinidas en la consola
- Lenguaje de consulta específico
- Detección automática de campos
- Filtrado por condiciones
- Cálculo de estadísticas agregadas
- Ordenación y limitación de eventos
- Capacidad para guardar consultas y añadirlas a dashboards
- Consulta de múltiples grupos de logs a la vez, incluso en diferentes cuentas
- Es un motor de consultas, no un motor en tiempo real (solo consulta datos históricos)

### 2.5 Exportación de CloudWatch Logs

#### 2.5.1 Exportación por lotes a Amazon S3

- No es en tiempo real (puede tardar hasta 12 horas)
- API: CreateExportTask

#### 2.5.2 Suscripción a CloudWatch Logs

Permite obtener un stream en tiempo real de eventos de log:

- **Destinos posibles**:
  - Kinesis Data Streams
  - Kinesis Data Firehose
  - Lambda

- **Subscription Filter**: Especifica qué eventos de log quieres enviar al destino

#### 2.5.3 Integración de servicios

- **Kinesis Data Streams**: Buena elección para integración con Kinesis Data Firehose, Kinesis Data Analytics, EC2 o Lambda
- **Kinesis Data Firehose**: Envío en tiempo real a S3 u OpenSearch Service
- **Lambda**: Funciones personalizadas o función gestionada que envía datos a OpenSearch Service

#### 2.5.4 Agregación de logs entre cuentas y regiones

- Puedes agregar logs de diferentes cuentas y regiones en un destino común
- Requiere configuración de "destinations" (destinos)
- Proceso:
  1. Crear CloudWatch Log subscription filter en la cuenta emisora
  2. Enviar a un destino de suscripción (representación virtual de Kinesis Data Stream) en la cuenta receptora
  3. Adjuntar una política de acceso al destino
  4. Crear un rol IAM en la cuenta receptora con permisos para enviar registros
  5. Asegurarse de que este rol pueda ser asumido por la primera cuenta

## 3. Amazon CloudWatch - Logs Unified Agent

Los CloudWatch Agents permiten enviar logs y métricas desde instancias EC2 a CloudWatch.

### 3.1 Conceptos básicos

- Por defecto, no se envían logs desde instancias EC2 a CloudWatch
- Necesitas crear y ejecutar un agente (programa pequeño) en tus instancias EC2
- La instancia EC2 debe tener un rol IAM que permita enviar logs a CloudWatch Logs
- Los agentes también pueden configurarse en servidores on-premises

### 3.2 Tipos de agentes

#### 3.2.1 CloudWatch Logs Agent (versión antigua)
- Solo puede enviar logs a CloudWatch Logs

#### 3.2.2 CloudWatch Unified Agent (versión nueva)
- Recopila métricas adicionales a nivel de sistema (RAM, procesos, etc.)
- Envía logs a CloudWatch Logs
- Se puede configurar fácilmente usando SSM Parameter Store
- Permite configuración centralizada para todos los agentes

### 3.3 Métricas recopiladas por el Unified Agent

El CloudWatch Unified Agent puede recopilar métricas detalladas:

- **CPU**: active, guest, idle, system, user, steal
- **Disco**: free, used, total
- **E/S de disco**: writes, reads, bytes, iops
- **RAM**: free, inactive, used, total, cached
- **Netstats**: conexiones TCP/UDP, paquetes de red, bytes
- **Procesos**: número total, dead, blocked, idle, running, sleep
- **Swap Space**: free, used, use percentage

### 3.4 Ventajas del Unified Agent

- Proporciona métricas más detalladas que el monitoreo estándar de EC2
- Recordatorio: Por defecto, EC2 solo proporciona información básica sobre disco, CPU y red (no memoria ni swap)
- Para mayor granularidad, usa CloudWatch Unified Agent

## 4. Amazon CloudWatch - Alarms

Los CloudWatch Alarms se utilizan para desencadenar notificaciones basadas en métricas.

### 4.1 Estados de las alarmas

- **OK**: No activada
- **INSUFFICIENT_DATA**: No hay suficientes datos para determinar un estado
- **ALARM**: Se ha superado el umbral y se envía una notificación

### 4.2 Períodos y resolución

- **Período**: Duración durante la cual quieres que la alarma evalúe la métrica
- Puede ser muy corto o muy largo
- Se aplica también a métricas personalizadas de alta resolución (10 segundos, 30 segundos o múltiplos de 60 segundos)

### 4.3 Objetivos de las alarmas

1. **Acciones en instancias EC2**:
   - Detener, terminar, reiniciar o recuperar instancias
2. **Acciones de Auto Scaling**:
   - Scale out o scale in
3. **Notificaciones a SNS**:
   - Desde SNS se puede conectar a una función Lambda para realizar cualquier acción

### 4.4 Composite Alarms

- Las CloudWatch Alarms normales son para una sola métrica
- Las Composite Alarms monitorean estados de múltiples alarmas
- Puedes usar condiciones AND u OR para ser flexible
- Ayudan a reducir el ruido de alarmas al crear condiciones complejas

#### 4.4.1 Ejemplo de Composite Alarm

1. Crear Alarm A monitoreando CPU de instancia EC2
2. Crear Alarm B monitoreando IOPS de instancia EC2
3. Definir Composite Alarm como la conjunción de Alarm A y Alarm B
4. Si ambas alarmas están en estado ALARM, la Composite Alarm se activa y puede desencadenar una notificación SNS

### 4.5 Recuperación de instancias EC2

- **Status checks**:
  - Instance status check: Verifica la máquina virtual EC2
  - System status check: Verifica la capa de hardware subyacente
  - EBS status check: Verifica la salud de los volúmenes EBS adjuntos

- Puedes definir una CloudWatch Alarm sobre estos checks
- En caso de que se active la alarma, puedes iniciar una recuperación de instancia EC2
- Durante la recuperación, se conservan:
  - IPs privadas, públicas y elásticas
  - Metadatos
  - Grupo de ubicación
- También puedes enviar una alerta a un tema SNS

### 4.6 Características adicionales

- Puedes crear una alarma sobre un filtro de métrica de CloudWatch Logs
- Para probar alarmas y notificaciones, puedes usar el comando CLI "set-alarm-state"
- Esto es útil para verificar que la acción desencadenada por la alarma funciona correctamente

---

## 📝 Resumen para el examen

### CloudWatch Metrics
- Proporcionan datos de TODOS los servicios AWS
- Elementos clave: métricas (variables), namespaces (contenedores), dimensiones (atributos)
- Las métricas son datos basados en TIEMPO con TIMESTAMP
- Monitoreo normal: cada 5 MINUTOS
- Monitoreo detallado: cada 1 MINUTO
- Puedes crear CUSTOM METRICS para datos no estándar (ej. uso de memoria)
- El streaming de métricas permite enviarlas a Kinesis Data Firehose o servicios de terceros

### CloudWatch Logs
- Estructura: LOG GROUPS (aplicaciones) → LOG STREAMS (instancias de logs)
- Retención: desde 1 DÍA hasta 10 AÑOS o NUNCA expirar
- CIFRADO: automático por defecto (opción KMS personalizada)
- Servicios que pueden enviar logs: EC2 (con agente), ECS, Lambda, VPC Flow Logs, API Gateway, CloudTrail, Route53
- CloudWatch Logs Insights: motor de CONSULTAS para logs (NO en tiempo real)
- Exportación:
  - Por LOTES a S3 (hasta 12h)
  - TIEMPO REAL con subscription filters (Kinesis, Lambda)
- Agregación entre cuentas/regiones usando DESTINATIONS

### CloudWatch Logs Unified Agent
- OBLIGATORIO para enviar logs desde EC2 a CloudWatch
- Requiere ROL IAM en EC2
- Dos tipos: Logs Agent (solo logs) y UNIFIED Agent (logs + métricas)
- Unified Agent recopila métricas detalladas: CPU, disco, RAM, red, procesos, swap
- Configuración centralizada con SSM Parameter Store
- Funciona también en servidores ON-PREMISES

### CloudWatch Alarms
- Estados: OK, INSUFFICIENT_DATA, ALARM
- Períodos: duración de evaluación (desde segundos hasta horas)
- Targets:
  - Acciones EC2: stop, terminate, reboot, recover
  - Auto-scaling: scale out/in
  - SNS: notificaciones (que pueden conectar a Lambda)
- COMPOSITE Alarms: combinan múltiples alarmas con AND/OR
- Recovery EC2: mueve instancias a otro host manteniendo IP, metadata
- Pruebas: comando "set-alarm-state" para simular activación

Recuerda que CloudWatch es fundamental para la arquitectura de soluciones ML en AWS, permitiendo monitorización, logging y alarmas automatizadas para tus recursos y aplicaciones.


## 5. AWS X-Ray

AWS X-Ray es uno de los servicios más revolucionarios que ofrece AWS, aunque actualmente está subutilizado. El examen AWS Certified Machine Learning - Associate enfatiza la importancia de conocer este servicio.

### 5.1 Problemas que resuelve X-Ray

La depuración tradicional en producción presenta varios desafíos:

- El método tradicional implica probar localmente, agregar declaraciones de log y redeployar en producción
- Los logs de diferentes aplicaciones tienen formatos distintos
- Es difícil centralizar insights y navegar por CloudWatch Logs
- El análisis de logs es complicado
- Con aplicaciones monolíticas es relativamente sencillo, pero con servicios distribuidos (microservicios), la depuración se vuelve extremadamente complicada
- No existe una vista común de toda la arquitectura de servicios

### 5.2 ¿Qué ofrece AWS X-Ray?

X-Ray proporciona un **análisis visual de tu aplicación**, mostrando:
- Solicitudes de clientes a tu aplicación
- Éxito o fallo de cada solicitud
- Llamadas que hace tu aplicación a otros servicios (IPs, SNS, DynamoDB, etc.)
- Identificación visual de dónde ocurren los errores (ejemplo: si un error proviene de DynamoDB)

### 5.3 Ventajas de AWS X-Ray

- **Troubleshooting de rendimiento** e identificación de cuellos de botella
- **Comprensión de dependencias** en arquitecturas de microservicios
- **Identificación de servicios** que causan problemas
- **Análisis de comportamiento** de cada solicitud
- **Detección de errores y excepciones** basados en solicitudes
- **Verificación de SLAs** de latencia y tiempo de procesamiento
- **Identificación de servicios** que causan lentitud o throttling
- **Determinación de usuarios** impactados por errores

### 5.4 Compatibilidad de X-Ray

X-Ray es compatible con múltiples servicios y entornos:
- AWS Lambda
- Elastic Beanstalk
- ECS
- Load Balancers (ELBs)
- API Gateway
- EC2 instances
- Servidores de aplicaciones on-premises

### 5.5 Funcionamiento de X-Ray

X-Ray utiliza el concepto de "tracing" (seguimiento):

1. **Trace**: Seguimiento end-to-end de una solicitud 
2. **Segments**: Cada componente que maneja la solicitud (base de datos, gateway, load balancer, servidor de aplicaciones) agrega su propio segmento
3. **Subsegments**: Divisiones dentro de los segmentos
4. **Annotations**: Información adicional adjunta a los traces

Se puede configurar para capturar todas las solicitudes o solo un porcentaje (ejemplo: 5 solicitudes por minuto).

**Seguridad**: 
- Autorización mediante IAM
- Cifrado en reposo con KMS

### 5.6 Habilitación de X-Ray

Para habilitar X-Ray se requieren dos elementos clave:

#### 5.6.1 Modificación del código
- Compatible con Java, Python, Go, Node.js, .Net
- Se debe importar el AWS X-Ray SDK
- Requiere mínimas modificaciones de código
- El SDK captura:
  - Llamadas a servicios AWS
  - Solicitudes HTTP/HTTPS
  - Llamadas a bases de datos (MySQL, PostgreSQL, DynamoDB)
  - Llamadas a colas

#### 5.6.2 X-Ray Daemon o integración AWS
- En servidores on-premises o EC2, se debe instalar el X-Ray Daemon
  - Funciona como interceptor de paquetes UDP de bajo nivel
  - Disponible para Linux, Windows y Mac
- Para Lambda u otros servicios integrados, AWS ejecuta el daemon automáticamente

### 5.7 Requisitos de permisos

- Cada aplicación debe tener permisos IAM para escribir datos en X-Ray
- Problema común: X-Ray funciona localmente pero no en EC2
  - Causas: Falta del X-Ray Daemon o permisos IAM incorrectos

### 5.8 Flujo de datos en X-Ray

1. El código de la aplicación (con SDK importado) envía traces al X-Ray Daemon en la máquina
2. El daemon envía lotes de datos cada segundo a AWS X-Ray
3. X-Ray recopila datos de todos los servicios que envían traces
4. El service map se genera automáticamente a partir de los segmentos y traces

Una ventaja importante es que X-Ray es gráfico, lo que permite que incluso personas no técnicas puedan ayudar en la solución de problemas.

### 5.9 Solución de problemas con X-Ray

#### 5.9.1 En EC2:
- Verificar que el rol IAM tenga los permisos adecuados
- Asegurar que la instancia esté ejecutando el X-Ray Daemon

#### 5.9.2 En Lambda:
- Verificar que Lambda tenga el rol de ejecución IAM con la política adecuada
- Confirmar que el código X-Ray esté importado correctamente
- Activar la opción "active tracing" de Lambda en X-Ray

---

## 📝 Resumen para el examen - AWS X-Ray

- AWS X-Ray es un servicio REVOLUCIONARIO para ANÁLISIS VISUAL de aplicaciones distribuidas
- Es ENFATIZADO en el examen AWS ML Associate (espera preguntas sobre este tema)
- Proporciona SERVICE MAP visual de toda la arquitectura
- Muestra TRAZAS END-TO-END de solicitudes permitiendo ver el flujo completo
- Componentes clave: TRACES, SEGMENTS, SUBSEGMENTS, ANNOTATIONS
- Para implementar X-Ray se requieren DOS elementos clave:
  1. MODIFICACIÓN DE CÓDIGO (importar X-Ray SDK)
  2. X-RAY DAEMON (en EC2/on-premises) o INTEGRACIÓN AWS (en Lambda)
- Lenguajes soportados: Java, Python, Go, Node.js, .Net
- El SDK captura: llamadas AWS, HTTP/HTTPS, bases de datos (MySQL, PostgreSQL, DynamoDB), colas
- PERMISOS IAM necesarios para escribir datos a X-Ray
- Problemas comunes: 
  - Falta de X-Ray Daemon en EC2
  - Permisos IAM incorrectos
  - En Lambda: falta activar "active tracing"
- VENTAJAS principales:
  - Troubleshooting visual de rendimiento 
  - Comprensión de dependencias entre microservicios
  - Identificación rápida de errores y servicios problemáticos
  - Verificación de SLAs de latencia
  - Determinación de usuarios impactados

X-Ray facilita enormemente la depuración de aplicaciones distribuidas, permitiendo identificar visualmente dónde ocurren los problemas sin necesidad de revisar múltiples logs.


## 6. Overview of Amazon QuickSight

Amazon QuickSight es una herramienta de análisis y visualización que está explícitamente cubierta en el examen de AWS Certified Machine Learning - Associate.

### 6.1 ¿Qué es Amazon QuickSight?

- Definición oficial: "Servicio de análisis empresarial rápido, fácil y potenciado por la nube"
- Fundamentalmente, es una aplicación que permite crear rápida y fácilmente dashboards, gráficos e informes basados en datos almacenados en AWS o en otros lugares
- Está orientado a usuarios finales y personas de negocios, no a desarrolladores
- Permite a todos los empleados de una organización construir visualizaciones, realizar análisis ad hoc y obtener insights de negocio
- Accesible desde cualquier dispositivo con navegador o dispositivo móvil
- Es una aplicación serverless (sin servidor)

### 6.2 Fuentes de datos compatibles

QuickSight puede conectarse a numerosas fuentes de datos dentro y fuera de AWS:

- **Dentro de AWS**:
  - Amazon Redshift
  - Amazon Aurora/RDS
  - Amazon Athena
  - Bases de datos alojadas en EC2
  - Amazon S3 (datos en data lakes)
  - AWS IoT Analytics

- **Fuera de AWS**:
  - Archivos Excel
  - Archivos CSV/TSV
  - Formatos de logs
  - Aplicaciones SaaS (como Salesforce)
  - Cualquier base de datos que soporte JDBC o ODBC

### 6.3 Preparación de datos

QuickSight permite realizar algunas modificaciones a los datos antes de analizarlos o visualizarlos:

- Cambiar nombres de columnas
- Añadir campos calculados
- Ejecutar consultas SQL
- Cambiar tipos de datos
- Realizar ETL limitado (no es su función principal)

### 6.4 Funcionamiento interno

- **SPICE** (Super-fast, Parallel, In-memory Calculation Engine): motor subyacente para acelerar consultas
- Utiliza almacenamiento columnar en memoria y generación de código máquina
- Limitado a 10 GB de memoria por usuario
- Altamente disponible, duradero y escalable (hasta cientos de miles de usuarios simultáneos)

### 6.5 Casos de uso de QuickSight

- Exploración interactiva y ad hoc de datos
- Visualización de datos mediante gráficos
- Creación de dashboards y reportes de KPI
- Análisis de logs almacenados en S3
- Visualización de datos desde bases de datos locales o en AWS
- Análisis de datos de aplicaciones SaaS

### 6.6 Capacidades de Machine Learning en QuickSight

#### 6.6.1 Machine Learning Insights
- **ML-Powered Anomaly Detection**: 
  - Utiliza el algoritmo Random Cut Forest de Amazon
  - Detecta anomalías en datos de ventas, latencia del sitio web, etc.

- **ML-Powered Forecasting**:
  - También utiliza Random Cut Forest
  - Elimina automáticamente anomalías en los datos
  - Realiza pronósticos teniendo en cuenta la estacionalidad
  - Excluye valores atípicos e imputa valores faltantes

- **Auto Narratives**:
  - Permite construir dashboards con narrativas integradas
  - Cuenta la historia de los datos en lenguaje natural
  - Se puede personalizar la narrativa automática generada

**Nota importante**: Estas son las únicas capacidades de machine learning directas en QuickSight. Si el examen pregunta sobre otras características o métricas de ML en QuickSight, no existen actualmente.

#### 6.6.2 QuickSight Q (Add-on)
- Responde preguntas de negocio mediante procesamiento de lenguaje natural (NLP)
- Permite formular preguntas como "¿Cuáles son los artículos más vendidos en Florida?"
- Requiere configuración adicional:
  - Se necesita entrenamiento personal para usarlo
  - Hay que configurar "temas" asociados con conjuntos de datos
  - Los conjuntos de datos y campos deben ser "amigables para NLP"
  - Se debe definir explícitamente cómo manejar las fechas
- Disponible como complemento con costo adicional en regiones seleccionadas

### 6.7 QuickSight Paginated Reports

- Nueva característica (finales de 2022)
- Informes con formato altamente detallado y multipágina
- Diseñados para ser impresos para la administración
- Se pueden construir a partir de dashboards existentes de QuickSight

### 6.8 ¿Para qué NO usar QuickSight?

- Tradicionalmente no se usaba para informes preformateados, pero ahora es posible con Paginated Reports
- No está diseñado para ETL completo (mejor usar AWS Glue para eso)

### 6.9 Seguridad en QuickSight

- **Autenticación**: Ofrece MFA (autenticación multifactor)
- **Conectividad VPC**: Posible añadiendo el rango de IP de QuickSight a las reglas de grupo de seguridad
- **Seguridad a nivel de fila (Row-Level Security, RLS)**: Permite a los propietarios de conjuntos de datos controlar el acceso a nivel de fila
- **Seguridad a nivel de columna**: Disponible solo en la edición Enterprise, permite restringir usuarios a campos específicos
- **Acceso a VPC privada**: Posible mediante Elastic Network Interface (ENI)
- **AWS Direct Connect**: Para crear un enlace privado seguro con recursos locales

### 6.10 Gestión de usuarios

- Los usuarios se definen a través de:
  - IAM
  - Proceso de registro por correo electrónico
  - SSO basado en SAML
  - Integración con Active Directory (solo en edición Enterprise)

- La facturación se realiza por usuario, por lo que es importante controlar quién tiene acceso

---

## 📝 Resumen para el examen - Amazon QuickSight

- QuickSight es una herramienta de ANÁLISIS y VISUALIZACIÓN de datos SERVERLESS
- Está EXPLÍCITAMENTE cubierta en el examen AWS ML Associate
- Orientado a USUARIOS DE NEGOCIO, no a desarrolladores técnicos
- Se puede conectar a MÚLTIPLES FUENTES DE DATOS:
  - Dentro de AWS: Redshift, Aurora/RDS, Athena, EC2, S3
  - Fuera de AWS: Excel, CSV, logs, aplicaciones SaaS, bases de datos JDBC/ODBC
- Utiliza SPICE (Super-fast, Parallel, In-memory Calculation Engine) como motor de procesamiento
- Limitado a 10 GB de memoria POR USUARIO
- Capacidades de MACHINE LEARNING:
  - ML-Powered ANOMALY DETECTION (usando Random Cut Forest)
  - ML-Powered FORECASTING (también con Random Cut Forest)
  - AUTO NARRATIVES para crear dashboards con explicaciones automáticas
- QuickSight Q es un ADD-ON que usa NLP para responder preguntas de negocio
  - Requiere CONFIGURACIÓN ESPECÍFICA y ENTRENAMIENTO
  - No es automático "out of the box"
- PAGINATED REPORTS es una nueva característica para informes formateados multipágina
- SEGURIDAD incluye:
  - MFA
  - Conectividad VPC
  - Seguridad a nivel de FILA y COLUMNA (esta última solo en Enterprise)
  - Acceso VPC privado mediante ENI
- Gestión de USUARIOS a través de IAM, email, SAML-SSO o Active Directory
- La FACTURACIÓN es POR USUARIO, importante controlar el acceso

QuickSight es una herramienta poderosa para visualización y análisis de datos con algunas capacidades de ML integradas, especialmente útil para usuarios no técnicos que necesitan insights de negocio rápidos.


## 7. Types of Visualizations in Amazon QuickSight

El examen AWS Certified Machine Learning - Associate suele incluir preguntas sobre qué tipo de visualización es más apropiada para determinados conjuntos de datos y objetivos de análisis. Esta sección explora los diferentes tipos de visualizaciones disponibles en Amazon QuickSight.

### 7.1 QuickSight Dashboards

- Son capturas instantáneas de un análisis
- Son de solo lectura una vez creados
- Pueden compartirse con otros usuarios de QuickSight
- Los usuarios que acceden al dashboard no pueden editar o cambiar los filtros
- Son útiles para presentar vistas de datos empresariales clave para que otros las consuman

### 7.2 Tipos de visualizaciones en QuickSight

#### 7.2.1 AutoGraph
- Selecciona automáticamente la visualización más apropiada basada en las propiedades de los datos
- Las visualizaciones se eligen para revelar los datos y las relaciones de manera efectiva
- A veces acierta, otras veces puede ser necesario construir sobre su sugerencia

#### 7.2.2 Visualizaciones para comparación y distribución

**Bar Charts (Gráficos de barras)**
- Útiles para comparación y distribución de datos
- Pueden ser verticales u horizontales
- Ideales para mostrar histogramas y distribuciones
- Permiten comparar datos entre diferentes categorías o periodos

**Line Charts (Gráficos de línea)**
- Diseñados para visualizar cambios o tendencias a lo largo del tiempo
- Pueden ser simples o incluir múltiples series
- También existen Area Line Charts (gráficos de área) y Stacked Area Line Charts (gráficos de área apilados)
- Son la opción ideal cuando la pregunta se refiere a visualizar tendencias temporales

**Scatter Plots (Gráficos de dispersión)**
- Muestran correlaciones entre dos variables
- Representan datos brutos como puntos en un plano 2D
- Permiten identificar patrones, clusters o correlaciones lineales
- Ejemplo: mostrar la relación entre el tiempo de espera entre erupciones y la duración de las mismas en un géiser

**Heat Maps (Mapas de calor)**
- Útiles para codificar por color datos en 2D
- Cada celda contiene un valor que se representa mediante un color
- Permite identificar patrones "calientes" o "fríos" en una matriz de datos
- Ejemplo: mostrar valoraciones de restaurantes (eje X) por críticos gastronómicos (eje Y)

#### 7.2.3 Visualizaciones para agregación

**Pie Charts (Gráficos circulares)**
- Muestran la proporción de diferentes categorías respecto al total
- Útiles para visualizar porcentajes o proporciones
- Ejemplo: distribución de hablantes nativos de inglés por país

**Donut Charts (Gráficos de dona)**
- Similar a los gráficos circulares, pero con un agujero en el centro
- Útiles para comparar valores para elementos en una dimensión
- Ideales para mostrar porcentajes de una cantidad total
- Mejores cuando la precisión no es crítica y hay pocos elementos

**Tree Maps (Mapas de árbol)**
- Muestran datos jerárquicos con rectángulos anidados
- Útiles para agregación jerárquica
- Cada rectángulo puede contener sub-rectángulos que representan niveles inferiores de la jerarquía
- Ejemplo: exportaciones de metales (rectángulo principal) con desglose por tipos específicos de metales

#### 7.2.4 Visualizaciones para datos tabulares

**Pivot Tables (Tablas dinámicas)**
- Útiles para resumir datos y medir valores en la intersección de dos dimensiones
- Permiten aplicar funciones estadísticas a los datos
- Ideales para datos multidimensionales
- Ejemplo: agregar datos de ventas por región y fecha de envío

#### 7.2.5 Visualizaciones para métricas y objetivos

**KPIs (Indicadores Clave de Rendimiento)**
- Comparan valores clave con sus valores objetivo
- Muestran métricas importantes junto con su estado relativo al objetivo
- Útiles para dashboards ejecutivos y seguimiento de objetivos

**Gauge Charts (Gráficos de indicador)**
- Visualizan datos en estilo de indicador (como en el tablero de un coche)
- Muestran cuánto hay de lo que se está midiendo (como combustible en un tanque)
- Útiles para mostrar uso de ancho de banda, capacidad, etc.

#### 7.2.6 Visualizaciones geoespaciales

**Geospatial Charts (Gráficos geoespaciales)**
- Mapas con datos superpuestos
- El tamaño de los círculos/marcadores representa la cantidad en esa ubicación
- Útiles para visualizar datos basados en información geográfica
- Ejemplo: población por estado o región

#### 7.2.7 Visualizaciones para datos textuales

**Word Clouds (Nubes de palabras)**
- Muestran la frecuencia de palabras o frases en un conjunto de datos
- El tamaño de cada palabra representa su frecuencia de aparición
- Útiles para analizar datos textuales y encontrar términos predominantes
- Ejemplo: destinos de viaje más frecuentes, palabras más utilizadas en un texto

### 7.3 Selección de visualizaciones según el objetivo

| Objetivo | Tipo de visualización recomendada |
|----------|-----------------------------------|
| Comparación y distribución | Bar Charts (gráficos de barras) |
| Tendencias a lo largo del tiempo | Line Charts (gráficos de línea) |
| Correlación entre variables | Scatter Plots (gráficos de dispersión) |
| Patrones en datos matriciales | Heat Maps (mapas de calor) |
| Proporciones y porcentajes | Pie Charts o Donut Charts (gráficos circulares o de dona) |
| Datos jerárquicos | Tree Maps (mapas de árbol) |
| Datos multidimensionales | Pivot Tables (tablas dinámicas) |
| Seguimiento de métricas vs. objetivos | KPIs o Gauge Charts |
| Datos geográficos | Geospatial Charts (gráficos geoespaciales) |
| Análisis de texto | Word Clouds (nubes de palabras) |

---

## 📝 Resumen para el examen - Visualizaciones en QuickSight

- Los DASHBOARDS son CAPTURAS de análisis de SOLO LECTURA que pueden compartirse
- AUTOGRAPH elige automáticamente la visualización más apropiada para tus datos
- Para COMPARACIÓN y DISTRIBUCIÓN:
  - BAR CHARTS (gráficos de barras) para histogramas y comparar categorías
  - LINE CHARTS (gráficos de línea) para TENDENCIAS A LO LARGO DEL TIEMPO
  - SCATTER PLOTS (gráficos de dispersión) para mostrar CORRELACIÓN entre variables
  - HEAT MAPS (mapas de calor) para codificar por color datos en 2D
- Para AGREGACIÓN:
  - PIE CHARTS (gráficos circulares) para mostrar PROPORCIONES
  - DONUT CHARTS (gráficos de dona) para PORCENTAJES del total
  - TREE MAPS (mapas de árbol) para datos JERÁRQUICOS
- Para DATOS TABULARES:
  - PIVOT TABLES (tablas dinámicas) para datos MULTIDIMENSIONALES
- Para MÉTRICAS y OBJETIVOS:
  - KPIs para comparar valores con objetivos
  - GAUGE CHARTS (gráficos de indicador) para mostrar niveles
- Para DATOS GEOGRÁFICOS:
  - GEOSPATIAL CHARTS (gráficos geoespaciales) para visualizar datos en mapas
- Para DATOS TEXTUALES:
  - WORD CLOUDS (nubes de palabras) para frecuencia de términos

Es CRÍTICO para el examen saber CUÁNDO usar cada tipo de visualización según el objetivo y la naturaleza de los datos. Las preguntas suelen presentar un escenario y pedir la visualización más apropiada.


## 8. Amazon CloudTrail

Amazon CloudTrail es un servicio que proporciona gobernanza, cumplimiento y auditoría para tus cuentas de AWS, permitiendo un seguimiento detallado de todas las acciones realizadas en tu infraestructura.

### 8.1 Fundamentos de CloudTrail

- CloudTrail está **habilitado por defecto** en todas las cuentas AWS
- Proporciona un historial de todos los eventos y llamadas a la API realizadas en tu cuenta AWS
- Registra acciones realizadas a través de:
  - Consola AWS
  - SDK
  - CLI 
  - Otros servicios de AWS
- Los logs pueden enviarse a:
  - CloudWatch Logs
  - Amazon S3
- Se puede crear un "trail" para:
  - Aplicarlo a todas las regiones
  - Aplicarlo a una sola región
  - Acumular eventos de todas las regiones en un único bucket S3

### 8.2 Casos de uso

El caso de uso principal de CloudTrail es la auditoría y detección de problemas:

- Identificar quién realizó una acción específica (ejemplo: terminación de una instancia EC2)
- Determinar cuándo se realizó una acción
- Analizar patrones de actividad sospechosos
- Cumplir con requisitos de conformidad y auditoría

### 8.3 Tipos de eventos en CloudTrail

#### 8.3.1 Management Events

- Representan operaciones realizadas sobre recursos en tu cuenta AWS
- Por defecto, los trails están configurados para registrar Management Events
- Incluyen operaciones como:
  - Configuración de seguridad (ejemplo: IAM AttachRolePolicy)
  - Creación de subnets
  - Configuración de logging

Los Management Events se dividen en:

- **Read Events**: No modifican recursos
  - Ejemplo: Listar usuarios de IAM, listar instancias EC2
- **Write Events**: Pueden modificar recursos
  - Ejemplo: Eliminar una tabla de DynamoDB
  - Generalmente tienen mayor importancia por su potencial destructivo

#### 8.3.2 Data Events

- **No se registran por defecto** debido a su alto volumen
- Incluyen:
  - Actividad a nivel de objeto en Amazon S3 (GetObject, DeleteObject, PutObject)
  - Actividades de ejecución de funciones AWS Lambda (API Invoke)
- También se pueden separar en Read Events (GetObject) y Write Events (DeleteObject, PutObject)

#### 8.3.3 CloudTrail Insights Events

- Eventos especiales generados mediante análisis de patrones de actividad
- Requieren habilitación específica y tienen costos adicionales

### 8.4 CloudTrail Insights

- Analiza eventos para detectar actividad inusual en tu cuenta
- Casos que puede detectar:
  - Aprovisionamiento impreciso de recursos
  - Alcance de límites de servicio
  - Ráfagas de acciones AWS IAM
  - Vacíos en actividades de mantenimiento periódicas

#### 8.4.1 Funcionamiento de CloudTrail Insights

1. Analiza las actividades normales de gestión para crear una línea base
2. Examina continuamente eventos de tipo Write para detectar patrones inusuales
3. Genera Insights Events cuando detecta anomalías
4. Los Insights Events aparecen en:
   - Consola de CloudTrail
   - Amazon S3 (opcional)
   - EventBridge/CloudWatch Events (para automatización, como envío de emails)

### 8.5 Retención de eventos en CloudTrail

- **Por defecto**: Los eventos se almacenan durante 90 días en CloudTrail
- **Para retención a largo plazo**: 
  - Se deben enviar los logs a S3
  - Se puede usar Amazon Athena para analizar los eventos almacenados en S3
- Todos los tipos de eventos (Management, Data e Insights) siguen este flujo de retención

---

## 📝 Resumen para el examen - Amazon CloudTrail

- CloudTrail está HABILITADO POR DEFECTO en todas las cuentas AWS
- Registra TODAS LAS LLAMADAS A LA API y EVENTOS realizados en tu cuenta AWS
- Principales fuentes de eventos: CONSOLA, SDK, CLI y SERVICIOS AWS
- Tres tipos de eventos:
  - MANAGEMENT EVENTS: operaciones sobre recursos (habilitados por defecto)
    * READ EVENTS: no modifican recursos (listar usuarios)
    * WRITE EVENTS: modifican recursos (eliminar tabla)
  - DATA EVENTS: operaciones de alto volumen (NO habilitados por defecto)
    * Ejemplos: S3 (GetObject, PutObject) y Lambda (Invoke)
  - INSIGHTS EVENTS: detección de actividad inusual (requiere habilitación y pago adicional)
- CloudTrail INSIGHTS:
  - Detecta ACTIVIDAD INUSUAL mediante análisis de patrones
  - Alertas enviadas a CONSOLA, S3 y EVENTBRIDGE
  - Casos de uso: provisioning impreciso, límites de servicio, ráfagas IAM
- RETENCIÓN:
  - 90 DÍAS por defecto en CloudTrail
  - LARGO PLAZO: enviar a S3 + analizar con ATHENA
- Casos de uso PRINCIPALES:
  - AUDITORÍA (quién hizo qué y cuándo)
  - DETECCIÓN DE PROBLEMAS
  - CUMPLIMIENTO normativo

CloudTrail es esencial para la seguridad y gobernanza en AWS, permitiendo un seguimiento detallado de todas las acciones y facilitando la investigación de incidentes y el cumplimiento normativo.


## 9. AWS Config

AWS Config es un servicio que permite realizar auditoría y registrar el cumplimiento de normas de tus recursos en AWS, basándose en reglas preestablecidas y manteniendo un registro histórico de los cambios de configuración.

### 9.1 Características principales de AWS Config

- Permite auditar y registrar la conformidad de recursos AWS
- Registra configuraciones y sus cambios a lo largo del tiempo
- Facilita la detección de problemas de configuración
- Permite realizar rollbacks para resolver problemas
- Responde preguntas como:
  - ¿Existe acceso SSH sin restricciones a mis grupos de seguridad?
  - ¿Tienen mis buckets S3 acceso público?
  - ¿Ha cambiado la configuración de mi Application Load Balancer (ALB) a lo largo del tiempo?

### 9.2 Alertas y configuración regional

- Puede enviar alertas o notificaciones SNS por cambios de configuración
- Es un servicio por región (debe configurarse en cada región de interés)
- Permite agregar datos de múltiples regiones y cuentas en un lugar centralizado
- Las configuraciones de recursos pueden almacenarse en Amazon S3 para análisis posterior con servicios como Athena

### 9.3 Tipos de reglas en AWS Config

#### 9.3.1 AWS Managed Config Rules
- Más de 75 reglas predefinidas disponibles
- Cubren escenarios comunes de conformidad y seguridad

#### 9.3.2 Custom Config Rules
- Creadas por el usuario mediante funciones Lambda
- Ejemplos:
  - Evaluar si cada disco EBS es de tipo gp2
  - Verificar si cada instancia EC2 en el entorno de desarrollo es de tipo t2.micro

### 9.4 Tipos de evaluación de reglas

- **Evaluación por cambios de configuración**: Se activan cuando hay un cambio en la configuración de un recurso
  - Ejemplo: Evaluar el tipo de un disco EBS cuando se crea o modifica

- **Evaluación periódica**: Se ejecutan a intervalos regulares
  - Ejemplo: Verificar cada 2 horas que todos los discos EBS son de tipo gp2

### 9.5 Limitaciones y costos

- Las reglas de Config son solo para conformidad y no impiden acciones
- No reemplazan mecanismos de seguridad como IAM
- Costos:
  - $0.003 por ítem de configuración registrado por región
  - $0.001 por evaluación de regla por región

### 9.6 Monitoreo de recursos

- Permite ver la conformidad de un recurso a lo largo del tiempo
- Muestra la configuración de recursos en diferentes momentos
- Se puede vincular con CloudTrail para ver las llamadas a la API realizadas por cada recurso

### 9.7 Remediación de recursos no conformes

- Aunque Config no puede impedir acciones, puede remediar recursos no conformes
- Utiliza SSM Automation Documents para la remediación
- Ejemplo:
  - Monitoreo de claves de acceso IAM expiradas (>90 días)
  - Al detectar una clave no conforme, se aplica el documento SSM "RevokeUnusedIAMUserCredentials"
  - La clave de acceso IAM se desactiva automáticamente

- Opciones de remediación:
  - Usar documentos AWS-Managed
  - Crear documentos de automatización personalizados
  - Invocar funciones Lambda para acciones personalizadas
  - Configurar reintentos (hasta 5) si la remediación inicial falla

### 9.8 Notificaciones

Existen dos métodos principales para recibir notificaciones:

1. **EventBridge**:
   - Se activa cuando los recursos no son conformes
   - Permite enviar eventos a varios servicios para su procesamiento

2. **Amazon SNS**:
   - Recibe cambios de configuración y notificaciones de conformidad
   - Permite filtrado de SNS para centrarse en eventos específicos
   - Las notificaciones pueden enviarse a:
     - Correos electrónicos de administradores
     - Canales de Slack
     - Otros destinos para centralizar alertas

---

## 📝 Resumen para el examen - AWS Config

- AWS Config es un servicio para AUDITORÍA y REGISTRO DE CONFORMIDAD de recursos AWS
- Mantiene un HISTORIAL DE CAMBIOS DE CONFIGURACIÓN para permitir ROLLBACKS
- Es un servicio POR REGIÓN que puede AGREGARSE en múltiples regiones y cuentas
- Tipos de reglas:
  - AWS MANAGED CONFIG RULES (más de 75 predefinidas)
  - CUSTOM CONFIG RULES (creadas con Lambda)
- Tipos de evaluaciones:
  - POR CAMBIO DE CONFIGURACIÓN (cuando cambia un recurso)
  - PERIÓDICA (a intervalos regulares, ej. cada 2 horas)
- LIMITACIÓN IMPORTANTE: Config NO IMPIDE acciones, solo MONITOREA conformidad
- COSTOS: $0.003 por ítem de configuración y $0.001 por evaluación de regla (por región)
- REMEDIACIÓN:
  - Usa SSM AUTOMATION DOCUMENTS para corregir recursos no conformes
  - Puede configurarse con REINTENTOS (hasta 5 veces)
  - Permite invocar LAMBDA para remediación personalizada
- NOTIFICACIONES:
  - EVENTBRIDGE para recursos no conformes
  - SNS para cambios de configuración y alertas de conformidad

AWS Config es un servicio fundamental para garantizar que la infraestructura cumple con los estándares de seguridad y configuración definidos, permitiendo detectar, registrar y remediar problemas de conformidad.


## 10. CloudWatch vs. CloudTrail vs. Config

Una pregunta común en el examen AWS Certified Machine Learning - Associate es distinguir entre CloudWatch, CloudTrail y Config. Aunque son servicios complementarios, cada uno tiene un propósito específico y diferente.

### 10.1 Comparación general

| Servicio | Propósito principal | Enfoque |
|----------|---------------------|---------|
| **CloudWatch** | Monitoreo de rendimiento | Métricas, logs y eventos |
| **CloudTrail** | Registro de actividad de API | Auditoría de acciones y seguridad |
| **Config** | Registro de configuraciones | Conformidad y cambios de configuración |

### 10.2 CloudWatch

- **Propósito**: Monitorear el rendimiento de los recursos y aplicaciones en AWS
- **Características principales**:
  - Recopilación de métricas de rendimiento (CPU, red, etc.)
  - Creación de dashboards personalizados
  - Generación de eventos y alertas
  - Agregación y análisis de logs

### 10.3 CloudTrail

- **Propósito**: Registrar llamadas a la API realizadas en tu cuenta AWS
- **Características principales**:
  - Registro de quién hizo qué acción, cuándo y desde dónde
  - Definición de trails para recursos específicos
  - Servicio global (aunque puede configurarse por región)
  - Historial completo de actividades para auditoría

### 10.4 Config

- **Propósito**: Registrar cambios de configuración y evaluar conformidad
- **Características principales**:
  - Seguimiento de cambios de configuración en recursos
  - Evaluación contra reglas de conformidad
  - Línea de tiempo de cambios y estado de conformidad
  - Interfaz visual para analizar configuraciones

### 10.5 Ejemplo práctico: Elastic Load Balancer (ELB)

Para entender mejor cómo funcionan estos servicios de forma complementaria, veamos cómo cada uno se aplicaría a un Elastic Load Balancer:

#### 10.5.1 CloudWatch para ELB

- Monitorea el número de conexiones entrantes
- Visualiza códigos de error como porcentaje a lo largo del tiempo
- Crea dashboards para entender el rendimiento del balanceador de carga
- Permite crear dashboards globales para múltiples balanceadores

#### 10.5.2 Config para ELB

- Realiza seguimiento de las reglas de grupos de seguridad del balanceador
- Registra cambios de configuración del propio balanceador
- Monitorea modificaciones en certificados SSL
- Implementa reglas de conformidad como:
  - Verificar que siempre haya un certificado SSL asignado
  - Asegurar que no se permita tráfico no cifrado

#### 10.5.3 CloudTrail para ELB

- Rastrea quién realizó cambios en el balanceador mediante llamadas a la API
- Identifica al responsable de cambios en:
  - Reglas de grupos de seguridad
  - Certificados SSL
  - Cualquier otra configuración del balanceador

### 10.6 Complementariedad de los servicios

Estos tres servicios son complementarios y juntos proporcionan una visión completa de tus recursos AWS:

- **CloudWatch**: Te dice **QUÉ** está sucediendo con el rendimiento
- **CloudTrail**: Te dice **QUIÉN** hizo cambios y **CUÁNDO**
- **Config**: Te dice **QUÉ CAMBIOS** se realizaron en la configuración y si cumplen con las reglas

---

## 📝 Resumen para el examen - CloudWatch vs. CloudTrail vs. Config

- **CloudWatch**:
  - Para MÉTRICAS DE RENDIMIENTO (CPU, memoria, red)
  - DASHBOARDS y VISUALIZACIONES
  - ALERTAS basadas en umbrales
  - AGREGACIÓN DE LOGS
  - Responde: "¿CÓMO ESTÁ FUNCIONANDO mi recurso?"

- **CloudTrail**:
  - Para REGISTRO DE ACTIVIDAD DE API
  - Muestra QUIÉN hizo QUÉ ACCIÓN
  - Servicio GLOBAL (puede configurarse por región)
  - AUDITORÍA de seguridad y conformidad
  - Responde: "¿QUIÉN hizo ESE CAMBIO y CUÁNDO?"

- **Config**:
  - Para REGISTRO DE CONFIGURACIONES
  - EVALUACIÓN DE CONFORMIDAD contra reglas
  - LÍNEA DE TIEMPO de cambios
  - CUMPLIMIENTO normativo
  - Responde: "¿QUÉ CONFIGURACIÓN tiene mi recurso y ES CONFORME?"

- **Ejemplo con ELB**:
  - CloudWatch: monitorea CONEXIONES y ERRORES
  - Config: registra CAMBIOS en GRUPOS DE SEGURIDAD y CERTIFICADOS SSL
  - CloudTrail: identifica QUIÉN realizó los cambios

Recuerda que estos servicios son COMPLEMENTARIOS y cada uno tiene un propósito ESPECÍFICO y DIFERENTE. En el examen, las preguntas suelen presentar un escenario y pedir qué servicio es más apropiado para una tarea específica.


## 11. Herramientas de Optimización de Costos y Recursos en AWS

AWS ofrece varias herramientas para ayudar a gestionar y optimizar los costos, así como supervisar el rendimiento y la seguridad de los recursos. Tres de estas importantes herramientas son AWS Budgets, AWS Cost Explorer y AWS Trusted Advisor.

### 11.1 AWS Budgets

AWS Budgets te permite crear presupuestos basados en cargos reales o estimaciones futuras y configurar alarmas cuando los costos excedan los límites establecidos.

#### 11.1.1 Tipos de presupuestos

AWS Budgets ofrece cuatro tipos de presupuestos:

1. **Presupuestos de uso**: Para controlar la cantidad de uso de un servicio específico
2. **Presupuestos de costo**: Para controlar los gastos totales
3. **Presupuestos de reserva**: Para seguir la utilización de instancias reservadas
4. **Presupuestos de Savings Plan**: Para monitorear y gestionar tus Savings Plans

#### 11.1.2 Características principales

- **Seguimiento de utilización de RI**: Para EC2, ElastiCache, RDS y Redshift
- **Notificaciones**: Hasta 5 notificaciones por presupuesto
- **Opciones de filtrado**: Por servicio, cuenta vinculada, etiqueta, opciones de compra, etc.
- **Granularidad**: Mucho más detallado y flexible que las alarmas de facturación estándar

#### 11.1.3 Costos

- Los primeros dos presupuestos son gratuitos
- A partir del tercero: $0.02 por día por presupuesto

### 11.2 AWS Cost Explorer

AWS Cost Explorer es un servicio de facturación que permite visualizar, comprender y gestionar los costos y el uso de AWS a lo largo del tiempo.

#### 11.2.1 Capacidades principales

- **Creación de informes personalizados**: Para analizar datos de costos y uso
- **Análisis multinivel**: Desde un alto nivel (costos totales) hasta niveles detallados (por hora o por recurso)
- **Previsión de costos**: Hasta 12 meses en el futuro basándose en el uso anterior
- **Recomendaciones de ahorro**: Sugiere planes de ahorro óptimos para reducir costos

#### 11.2.2 Visualizaciones disponibles

- **Costos mensuales por servicio AWS**: Ver qué servicios generan más gastos
- **Costos por tipo de instancia**: Identificar instancias costosas para posible optimización
- **Nivel de recurso y por hora**: Análisis detallado del costo por recurso individual
- **Recomendaciones de Savings Plan**: Alternativas a las instancias reservadas con estimaciones de ahorro
- **Previsión de uso**: Estimaciones de costos futuros con intervalos de confianza

### 11.3 AWS Trusted Advisor

AWS Trusted Advisor proporciona una evaluación de alto nivel de tu cuenta AWS sin necesidad de instalar nada adicional.

#### 11.3.1 Categorías de verificación

Trusted Advisor realiza comprobaciones en seis categorías principales:

1. **Optimización de costos**: Identifica oportunidades para reducir gastos
2. **Rendimiento**: Sugiere mejoras para aumentar la eficiencia
3. **Seguridad**: Detecta posibles vulnerabilidades o configuraciones inseguras
4. **Tolerancia a fallos**: Comprueba la resiliencia de tus aplicaciones
5. **Límites de servicio**: Monitorea los límites de servicio para evitar problemas
6. **Excelencia operativa**: Proporciona recomendaciones sobre mejores prácticas

#### 11.3.2 Niveles de comprobación

- **Conjunto básico de comprobaciones**: Disponible para todos los clientes
  - Principalmente enfocado en seguridad y límites de servicio
  - Ejemplos: Permisos de buckets S3, reglas de grupos de seguridad, snapshots públicos de EBS/RDS

- **Conjunto completo de comprobaciones**: Requiere plan de soporte Business o Enterprise
  - Incluye todas las categorías de verificación
  - Acceso programático mediante AWS Support API

### 11.4 Comparación y casos de uso

| Herramienta | Propósito principal | Mejor para | Nivel de acceso |
|-------------|---------------------|------------|-----------------|
| **AWS Budgets** | Control proactivo de costos | Establecer límites de gastos y recibir alertas | Acceso básico gratuito (2 presupuestos) |
| **AWS Cost Explorer** | Análisis y previsión de costos | Visualizar, entender y planificar gastos | Acceso completo para todos los clientes |
| **AWS Trusted Advisor** | Evaluación y optimización general | Identificar riesgos y oportunidades de mejora | Acceso básico gratuito; completo con planes Business/Enterprise |

#### 11.4.1 Escenarios de uso

- **Control de gastos mensual**: 
  - Crea un presupuesto en AWS Budgets para establecer un límite mensual
  - Configura alertas cuando alcances el 80% y 100% del presupuesto

- **Planificación de costos para el próximo año fiscal**:
  - Utiliza Cost Explorer para analizar los patrones de gasto actuales
  - Aprovecha la función de previsión para estimar los costos futuros

- **Optimización general de la cuenta**:
  - Revisa las recomendaciones de Trusted Advisor para identificar:
    - Configuraciones de seguridad inadecuadas
    - Recursos infrautilizados
    - Oportunidades para reducir costos
    - Posibles mejoras de rendimiento

- **Decisión sobre compromiso a largo plazo**:
  - Utiliza Cost Explorer para evaluar recomendaciones de Savings Plan
  - Crea un presupuesto de reserva en AWS Budgets para seguir la utilización de tus instancias reservadas

---

## 📝 Resumen para el examen - Herramientas de Optimización de Costos y Recursos

### AWS Budgets
- Crea PRESUPUESTOS basados en CARGOS REALES o ESTIMACIONES FUTURAS
- CUATRO TIPOS: uso, costo, reserva y Savings Plan
- Seguimiento de UTILIZACIÓN DE RI para varios servicios (EC2, ElastiCache, RDS, Redshift)
- Hasta CINCO NOTIFICACIONES por presupuesto
- FILTRADO por servicio, cuenta, etiqueta, opciones de compra
- MÁS GRANULAR y FLEXIBLE que las alarmas de facturación estándar
- COSTOS: primeros dos presupuestos GRATUITOS, luego $0.02 por día por presupuesto

### AWS Cost Explorer
- VISUALIZACIÓN y ANÁLISIS de costos AWS a lo largo del tiempo
- INFORMES PERSONALIZADOS para analizar datos de costo y uso
- Análisis desde ALTO NIVEL hasta granularidad POR HORA o POR RECURSO
- PREVISIÓN de costos hasta 12 MESES en el futuro
- Recomendaciones de SAVINGS PLAN para optimizar costos
- GRÁFICOS por servicio, tipo de instancia y recurso
- Disponible para TODOS LOS CLIENTES sin costo adicional

### AWS Trusted Advisor
- Evaluación de ALTO NIVEL de cuenta sin instalación adicional
- SEIS CATEGORÍAS: optimización de costos, rendimiento, seguridad, tolerancia a fallos, límites de servicio, excelencia operativa
- DOS NIVELES de comprobación:
  - BÁSICO: principalmente seguridad y límites de servicio (GRATUITO)
  - COMPLETO: todas las categorías (requiere plan BUSINESS o ENTERPRISE)
- Ejemplos de verificaciones: permisos de buckets S3, reglas de grupos de seguridad, snapshots públicos
- Acceso PROGRAMÁTICO vía API solo con plan Business/Enterprise

Las tres herramientas son COMPLEMENTARIAS y juntas proporcionan una solución integral para gestionar y optimizar tus recursos AWS desde diferentes perspectivas: control proactivo (Budgets), análisis detallado (Cost Explorer) y recomendaciones generales (Trusted Advisor).