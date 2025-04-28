# AWS Managed AI Services

## 1. Amazon Comprehend

### 1.1 Introducción a Amazon Comprehend

Amazon Comprehend es un servicio de procesamiento de lenguaje natural (NLP) que es completamente gestionado y serverless. Utiliza machine learning para encontrar información relevante y relaciones en textos.

### 1.2 Capacidades principales

- Entender el lenguaje del texto
- Extraer frases clave, lugares, personas, marcas o eventos
- Determinar el sentimiento (positivo o negativo) del texto
- Analizar texto mediante tokenización y análisis de partes del discurso
- Organizar colecciones de archivos de texto por temas

### 1.3 Casos de uso

- Analizar interacciones con clientes (como emails) para identificar experiencias positivas o negativas
- Crear grupos de artículos por temas que Comprehend descubre automáticamente

### 1.4 Configuraciones avanzadas

#### 1.4.1 Clasificación personalizada
- Permite definir cómo queremos que Comprehend categorice los documentos
- Útil para categorizar emails de clientes en tipos específicos (solicitudes de soporte, facturación, quejas, etc.)
- Soporta diferentes tipos de documentos: texto, PDF, Word, imágenes
- Proceso:
  - Crear datos de entrenamiento y almacenarlos en Amazon S3
  - Alimentar estos datos a Amazon Comprehend para construir y entrenar un clasificador personalizado
  - El clasificador analizará nuevos documentos según las categorías definidas
- Puede usarse con análisis en tiempo real o análisis asincrónico para múltiples documentos

#### 1.4.2 Reconocimiento de entidades nombradas (NER)
- Extrae entidades predefinidas de propósito general como personas, lugares, organizaciones, fechas y otras categorías estándar
- Ejemplo: En el texto "Zhang Wei y John de Any Company Financial Services LLC se reunirán el 31 de julio", se reconocería:
  - "Zhang Wei" y "John" como personas
  - "Any Company Financial Services LLC" como organización
  - "31 de julio" como fecha

#### 1.4.3 Reconocimiento de entidades personalizadas
- Permite analizar texto para términos específicos y frases basadas en sustantivos
- Útil para extraer consistentemente números de póliza, frases que impliquen escalamiento de clientes, u otros términos relacionados con el negocio
- Proceso:
  - Entrenar el modelo con una lista de entidades que se buscan y documentos que las contienen
  - El reconocedor de entidades personalizado se entrena y puede usarse para identificar estas entidades específicas
- Puede utilizarse para análisis en tiempo real o asincrónico

### 1.5 Modelos personalizados de Amazon Comprehend

- Permite crear modelos personalizados para reconocimiento de entidades o clasificación de documentos
- Los modelos personalizados se entrenan con tus propios datos
- Comprehend gestiona automáticamente el versionado de los modelos, permitiendo llevar un seguimiento de diferentes versiones según se entrenan con distintos datos
- Los modelos se pueden compartir entre cuentas AWS:
  - Es necesario adjuntar una política IAM a la versión específica del modelo que se quiere compartir
  - La política IAM debe autorizar a la otra cuenta para acceder al modelo
  - La otra cuenta puede importar el modelo directamente desde la Consola de Comprehend
  - Requisitos para compartir modelos:
    - Ambas cuentas deben usar Comprehend en la misma región
    - La cuenta que importa necesita el ARN (identificador) del modelo
    - También necesita conocer la región donde está el modelo
    - Opcionalmente, requiere una clave KMS si el modelo está cifrado

En resumen, Amazon Comprehend se utiliza para procesamiento y comprensión del lenguaje natural, con opciones para clasificaciones personalizadas y reconocimiento de entidades si se entrena el modelo sobre la base de Comprehend. Además, ofrece la capacidad de crear, versionar y compartir modelos personalizados entre cuentas AWS.

## 2. Amazon Translate

### 2.1 Introducción a Amazon Translate

Amazon Translate es un servicio que proporciona traducción de idiomas natural y precisa.

### 2.2 Capacidades principales

- Permite localizar contenido como sitios web y aplicaciones para usuarios internacionales
- Traduce eficientemente grandes volúmenes de texto

### 2.3 Ejemplos de traducción

Amazon Translate puede traducir frases entre múltiples idiomas:
- Inglés: "Hi, my name is Stephen"
- Francés: "Bonjour, je m'appelle Stephen"
- Portugués: "Olá, meu nome é Stephen"
- Hindi: "Namaste, mera naam Stephen hai"

Amazon Translate es un servicio sencillo pero poderoso que facilita la traducción precisa entre diferentes idiomas.

## 3. Amazon Transcribe

### 3.1 Introducción a Amazon Transcribe

Amazon Transcribe permite convertir automáticamente voz en texto. Se le proporciona un audio y automáticamente lo transcribe en texto.

### 3.2 Funcionamiento y características principales

- Utiliza un proceso de deep learning llamado ASR (Automatic Speech Recognition) para convertir voz a texto de manera rápida y precisa
- Puede eliminar automáticamente información de identificación personal (PII) mediante redacción
- Ofrece identificación automática de idiomas para audio multilingüe (reconoce diferentes idiomas como francés, inglés y español en un mismo audio)

### 3.3 Casos de uso

- Transcripción de llamadas de servicio al cliente
- Automatización de subtítulos y leyendas
- Generación de metadatos para activos multimedia para crear un archivo completamente consultable

### 3.4 Mejora de la precisión

#### 3.4.1 Vocabularios personalizados
- Permite capturar términos específicos de dominio o no estándar como palabras técnicas, acrónimos y jerga
- Se pueden añadir palabras específicas, frases o términos de dominio específico
- Ideal para nombres de marcas o acrónimos utilizados frecuentemente
- Se puede aumentar el reconocimiento de nuevas palabras proporcionando sugerencias sobre cómo pronunciarlas

#### 3.4.2 Modelos de lenguaje personalizados
- Enfocado en el contexto, no solo en palabras individuales
- Se entrena el modelo de Transcribe con datos de texto específicos del dominio
- Ayuda a Transcribe a aprender el contexto asociado a una palabra determinada
- No enseña nuevas palabras a Amazon Transcribe, sino que proporciona el contexto de lo que se intenta transcribir

Para obtener la mayor precisión en la transcripción, se recomienda utilizar tanto vocabularios personalizados como modelos de lenguaje personalizados.

### 3.5 Detección de toxicidad

- Función impulsada por machine learning
- Puede usar directamente una muestra de voz para detectar toxicidad
- Utiliza dos tipos de datos:
  - Señales de voz: analiza el tono y el tono del audio para detectar señales como enojo
  - Señales basadas en texto: detecta lenguaje como obscenidades o discurso de odio
- La combinación de audio y texto es útil para describir la toxicidad en una muestra
- Las categorías de toxicidad incluyen: acoso sexual, discurso de odio, amenazas, abuso, obscenidades, insultos y contenido gráfico

## 4. Amazon Polly

### 4.1 Introducción a Amazon Polly

Amazon Polly es el servicio opuesto a Amazon Transcribe, permitiendo convertir texto en voz realista utilizando deep learning. Permite crear aplicaciones que pueden hablar.

### 4.2 Características avanzadas

#### 4.2.1 Lexicons
- Permiten definir cómo leer ciertos fragmentos de texto
- Ejemplos:
  - Escribir "AWS" pero hacer que Polly pronuncie "Amazon Web Services"
  - Escribir "W3C" pero hacer que Polly diga "World Wide Web Consortium"

#### 4.2.2 SSML (Speech Synthesis Markup Language)
- Marcadores que indican cómo debe pronunciarse el texto
- Capacidades:
  - Insertar pausas (breaks)
  - Susurrar textos
  - Pronunciar abreviaturas
  - Enfatizar palabras

#### 4.2.3 Motores de voz
- Múltiples opciones disponibles, desde las más antiguas hasta las más nuevas:
  - Neural
  - Standard
  - Long-form
  - Generative
- Los motores más nuevos ofrecen voces muy similares a las humanas

#### 4.2.4 Speech marks
- Proporcionan información sobre dónde comienza o termina una palabra o frase en el audio
- Polly ofrece tanto el audio como estas marcas de voz
- Útil para sincronización labial o para resaltar palabras mientras se hablan


## 5. Amazon Rekognition

### 5.1 Introducción a Amazon Rekognition

Amazon Rekognition es un servicio que permite encontrar objetos, personas, textos o escenas directamente en imágenes o videos utilizando machine learning.

### 5.2 Capacidades principales

- Análisis facial o búsqueda facial para verificación de usuarios o conteo de personas en fotos
- Creación de bases de datos de rostros familiares
- Comparación de rostros encontrados contra celebridades

### 5.3 Casos de uso

- Etiquetado
- Moderación de contenido
- Detección de texto
- Detección y análisis facial (género, rango de edad, emociones)
- Búsqueda y verificación facial
- Reconocimiento de celebridades
- Análisis de trayectorias (pathing), por ejemplo, para análisis de juegos deportivos

### 5.4 Características específicas

#### 5.4.1 Face liveness
- Verifica si un rostro es real y pertenece a una persona viva durante una llamada

#### 5.4.2 Moderación de contenido
- Garantiza que el contenido sea seguro, por ejemplo, para niños

#### 5.4.3 Detección de etiquetas
- Identifica elementos como logos, personas, objetos, escenas

#### 5.4.4 Detección de texto
- Reconoce texto en imágenes

### 5.5 Custom Labels

- Permite identificar productos específicos o logos en publicaciones de redes sociales
- Proceso:
  - Etiquetar imágenes de entrenamiento (solo se necesitan unos cientos o menos)
  - Cargarlas a Amazon Rekognition
  - Rekognition crea un modelo personalizado basado en estas imágenes
  - El modelo puede reconocer logos, productos u otros elementos personalizados en nuevas imágenes
- Ejemplo de implementación:
  - Etiquetar imágenes con el logo o productos y almacenarlas en Amazon S3
  - Entrenar Amazon Rekognition para crear Custom Labels
  - Analizar contenido de redes sociales para detectar apariciones del logo

### 5.6 Moderación de contenido

- Detecta automáticamente contenido inapropiado, no deseado u ofensivo
- Útil para filtrar contenido dañino en redes sociales o publicidad
- Reduce la necesidad de revisión humana al 1-5% del volumen de contenido
- Si se requiere revisión humana, se puede utilizar Amazon Augmented AI (A2I)

#### 5.6.1 Adaptador de moderación personalizado
- Extiende las capacidades de Rekognition mediante imágenes etiquetadas propias
- Permite definir qué moderar específicamente
- Mejora la precisión de la moderación de contenido
- Funciona mediante:
  - Etiquetado de imágenes
  - Entrenamiento de un adaptador de moderación personalizado
  - Procesamiento de imágenes: aprobación, rechazo o envío a revisión humana
  - La retroalimentación de la revisión humana puede usarse para mejorar el entrenamiento

### 5.7 Ejemplo de uso de la API de moderación de contenido

- Un chatbot que genera imágenes puede utilizar Rekognition para verificar si el contenido es seguro
- Proceso:
  - El usuario solicita una imagen generada
  - El chatbot genera la imagen
  - Se envía la imagen a la API DetectModerationLabels de Amazon Rekognition
  - Rekognition crea etiquetas para la imagen
  - Si las etiquetas no indican contenido dañino, la imagen se devuelve al usuario

## 6. Amazon Lex

### 6.1 Introducción a Amazon Lex

Amazon Lex es un servicio que permite construir chatbots rápidamente para aplicaciones, utilizando voz o texto como interfaz con los chatbots.

### 6.2 Funcionalidades principales

- Construye IA conversacional (conversational AI)
- Soporta múltiples idiomas
- Posee integración profunda con otros servicios AWS:
  - AWS Lambda
  - Amazon Connect
  - Amazon Comprehend
  - Amazon Kendra

### 6.3 Funcionamiento

- El bot comprende la intención (intent) del usuario
- Invoca la función Lambda correcta asociada a esa intención para satisfacerla
- Ejemplo: para reservar un hotel, Lex reconoce la intención de reserva cuando el usuario lo solicita
- Cuando se tiene toda la información necesaria, se invoca una función Lambda que realiza la reserva en el sistema
- Lex responde al usuario confirmando la reserva exitosa

### 6.4 Slots

- Los Slots son parámetros de entrada necesarios para la función Lambda
- Por ejemplo, para reservar un hotel se necesita: ciudad, fecha de check-in, etc.
- El bot conversa automáticamente con el usuario para recopilar toda la información necesaria
- Una vez que tiene todos los Slots completos, invoca la función Lambda para realizar la acción

Amazon Lex permite a los usuarios interactuar con sistemas backend utilizando solo texto y voz, lo que proporciona una experiencia más natural y accesible.

## 7. Amazon Personalize

### 7.1 Introducción a Amazon Personalize

Amazon Personalize es un servicio completamente gestionado de machine learning para construir aplicaciones con recomendaciones personalizadas en tiempo real.

### 7.2 Tipos de recomendaciones

- Recomendaciones de productos personalizadas
- Re-clasificación (re-ranking) de elementos
- Marketing directo personalizado

### 7.3 Características principales

- Utiliza la misma tecnología que Amazon.com
- Lee datos de entrada desde Amazon S3 (interacciones de usuarios, etc.)
- Permite integración de datos en tiempo real mediante Amazon Personalize API
- Proporciona una API personalizada para sitios web, aplicaciones y aplicaciones móviles
- Permite enviar SMS o emails personalizados
- Reduce el tiempo de construcción de modelos de meses a días
- No requiere construir, entrenar ni desplegar soluciones ML

### 7.4 Casos de uso

- Tiendas minoristas
- Medios y entretenimiento

### 7.5 Recipes de Amazon Personalize

Las "recipes" son algoritmos ya implementados en Personalize preparados para casos de uso específicos. Se necesita proporcionar configuración de entrenamiento sobre la recipe para adaptarla a cada caso de uso.

#### 7.5.1 Tipos de recipes

- **USER_PERSONALIZATION**: 
  - User-Personalization-v2
  - Recomendación de elementos para usuarios

- **PERSONALIZED_RANKING**:
  - Personalized-Ranking-v2
  - Clasificación de elementos para un usuario

- **Recomendación de elementos populares**:
  - Trending-Now
  - Popularity-Count

- **RELATED_ITEMS**:
  - Recomendación de elementos similares

- **Next Best Action**:
  - Recomendación de la siguiente mejor acción

- **Segmentos de usuarios**:
  - Item-Affinity
  - Extracción de segmentos de usuarios

Todas las recipes de Amazon Personalize están diseñadas para ofrecer recomendaciones personalizadas basadas en las preferencias del usuario, no para pronósticos u otros propósitos.

## 8. Amazon Textract

### 8.1 Introducción a Amazon Textract

Amazon Textract es un servicio utilizado para extraer texto, como su nombre indica. Permite extraer texto, escritura a mano o datos de cualquier documento escaneado utilizando AI o machine learning.

### 8.2 Capacidades principales

- Extracción de texto de documentos escaneados
- Procesamiento de licencias de conducir, formularios y otros documentos
- Análisis automático de documentos y entrega de resultados como archivo de datos
- Extracción de datos específicos como fecha de nacimiento, ID de documento, etc.
- Capacidad para extraer datos de formularios y tablas
- Soporte para PDFs, imágenes y otros formatos

### 8.3 Casos de uso

- **Servicios financieros**: 
  - Procesamiento de facturas
  - Análisis de informes financieros

- **Sector sanitario**:
  - Procesamiento de registros médicos
  - Gestión de reclamaciones de seguros

- **Sector público**:
  - Procesamiento de formularios fiscales
  - Análisis de documentos de identidad
  - Lectura de pasaportes

## 9. Amazon Kendra

### 9.1 Introducción a Amazon Kendra

Amazon Kendra es un servicio de búsqueda de documentos completamente gestionado, potenciado por machine learning, que permite extraer respuestas desde dentro de un documento.

### 9.2 Tipos de documentos compatibles

- Texto
- PDF
- HTML
- PowerPoint
- Microsoft Word
- FAQs
- Otros formatos

### 9.3 Funcionalidad principal

- Indexa documentos de múltiples fuentes de datos
- Construye internamente un índice de conocimiento potenciado por machine learning
- Proporciona capacidades de búsqueda en lenguaje natural (similar a Google)
- Puede responder preguntas directas con información extraída de documentos

### 9.4 Características avanzadas

- **Aprendizaje incremental**: Aprende de las interacciones y retroalimentación de los usuarios para promover resultados de búsqueda preferidos
- **Ajuste fino de resultados**: Permite personalizar resultados basados en:
  - Importancia de los datos
  - Actualidad de la información
  - Filtros personalizados

Amazon Kendra es la solución a considerar cuando se necesita un servicio de búsqueda de documentos en AWS.

## 10. Amazon Augmented AI (A2I)

### 10.1 Introducción a Amazon Augmented AI (A2I)

Amazon Augmented AI (A2I) es un servicio que proporciona supervisión humana para los modelos de machine learning que hacen predicciones en producción, asegurando que funcionen correctamente.

### 10.2 Flujo de trabajo

- Los datos de entrada se procesan a través de un servicio AWS AI o un modelo de machine learning personalizado
- El modelo realiza una predicción y evalúa su nivel de confianza
- Basado en el nivel de confianza:
  - Predicciones de alta confianza: se devuelven inmediatamente a la aplicación cliente
  - Predicciones de baja confianza: se envían a revisión humana

### 10.3 Proceso de revisión humana

- Los revisores humanos consolidan las predicciones
- Crean puntuaciones ponderadas por riesgo
- Estas puntuaciones se almacenan en Amazon S3
- La aplicación cliente recibe la predicción revisada
- Las predicciones revisadas se retroalimentan al modelo de machine learning para mejorar su calidad

### 10.4 Opciones de revisores humanos

- Empleados propios
- Más de 500,000 contratistas de AWS
- Trabajadores de AWS Mechanical Turk
- Proveedores preseleccionados que cumplen con requisitos de confidencialidad

### 10.5 Compatibilidad con modelos

- Servicios de AI de AWS (como Amazon Rekognition)
- Modelos personalizados desarrollados en Amazon SageMaker
- Modelos alojados en otras plataformas con integración a Amazon A2I

## 11. Amazon's Hardware for AI

### 11.1 Introducción a Amazon EC2 para AI

Amazon EC2 (Elastic Compute Cloud) es uno de los servicios más populares de AWS, que proporciona servidores virtuales. Desde la perspectiva de AI, EC2 ofrece opciones específicas para cargas de trabajo de machine learning.

### 11.2 Tipos de instancias EC2 para AI/ML

| Familia | Descripción | Características | Uso recomendado |
|---------|-------------|-----------------|-----------------|
| **Instancias GPU** | Instancias con unidades de procesamiento gráfico | Familias P y G (P3, P4, P5, G3, G6, etc.) | Entrenamiento y ejecución de modelos de ML |
| **AWS Trainium** | Chips de ML diseñados por AWS | Instancias Trn1 con 16 aceleradores Trainium | Entrenamiento de deep learning para modelos grandes (100+ mil millones de parámetros) |
| **AWS Inferentia** | Chips de ML para inferencia | Instancias Inf1 e Inf2 | Servicio de modelos con alto rendimiento y bajo costo |

### 11.3 Ventajas de hardware especializado AWS

#### 11.3.1 AWS Trainium
- Diseñado específicamente para entrenar modelos de deep learning
- Hasta 50% de reducción de costos comparado con instancias GPU estándar
- Ideal para modelos con más de 100 mil millones de parámetros

#### 11.3.2 AWS Inferentia
- Optimizado para servir modelos de ML (inferencia)
- Hasta 4 veces más throughput que instancias basadas en GPU
- Hasta 70% de reducción de costos comparado con instancias GPU estándar

#### 11.3.3 Beneficios ambientales
- Las instancias Trainium e Inferentia tienen la menor huella ambiental
- Mayor eficiencia energética debido a su optimización específica para tareas de ML

La elección entre instancias GPU estándar, Trainium o Inferentia dependerá de si estás entrenando o sirviendo modelos, y de los requerimientos específicos de tus cargas de trabajo de machine learning.

## 12. Amazon Lookout

### 12.1 Introducción a Amazon Lookout

Amazon Lookout es un servicio para detección de anomalías que utiliza machine learning para detectar y diagnosticar comportamientos inusuales en datos.

### 12.2 Terminología clave

| Término | Descripción |
|---------|-------------|
| **Detector** | Componente que monitorea datasets para encontrar anomalías |
| **Dataset** | Fuente de datos que está siendo monitoreada |
| **Anomalía** | Comportamiento inusual o fuera de lo normal detectado |
| **Measure** | El valor objetivo que se está optimizando (equivalente a "label" en otros contextos ML) |
| **Dimensions** | Factores que influyen en el "measure" (equivalente a "features" en otros contextos ML) |

### 12.3 Variantes de Amazon Lookout

| Variante | Propósito | Casos de uso |
|----------|-----------|-------------|
| **Lookout for Metrics** | Detección de anomalías general | Identificar variaciones inusuales en métricas de rendimiento empresarial, KPIs, datos de campañas publicitarias |
| **Lookout for Equipment** | Monitoreo de equipamiento industrial | Mantenimiento predictivo, integración con sensores IoT, prevención de fallos costosos |
| **Lookout for Vision** | Inspección de calidad automatizada | Monitoreo visual mediante cámaras en líneas de ensamblaje, identificación automática de defectos |

### 12.4 Funcionamiento e integración

![Diagrama de Lookout](https://i.imgur.com/placeholder.jpg)

- **Fuentes de datos**:
  - Amazon S3
  - Amazon Redshift
  - Amazon Athena
  - Amazon CloudWatch
  - Integraciones con terceros (Salesforce, Marketo)

- **Gestión**:
  - Servicio completamente gestionado
  - No requiere preocuparse por infraestructura subyacente

- **Acciones ante anomalías**:
  - Notificaciones vía Amazon SNS
  - Ejecución de funciones AWS Lambda
  - Monitoreo a través de la consola AWS

- **Retroalimentación**:
  - Permite ajustar la precisión
  - Reducción de falsos positivos/negativos
  - Sistema de ajuste en tiempo real

Amazon Lookout es un sistema completo para detección de anomalías que puede adaptarse a múltiples escenarios, desde métricas empresariales hasta inspección visual de calidad y mantenimiento predictivo de equipos.

## 13. Amazon Fraud Detector

### 13.1 Introducción a Amazon Fraud Detector

Amazon Fraud Detector es un servicio completamente gestionado diseñado específicamente para detectar fraudes. Utiliza machine learning para identificar actividades fraudulentas en diversos escenarios.

### 13.2 Casos de uso

Amazon Fraud Detector puede aplicarse a diferentes tipos de fraude:
- Fraude en pagos en línea
- Fraude en creación de nuevas cuentas
- Abuso de programas de prueba
- Intentos de apropiación de cuentas ajenas

### 13.3 Características principales

- Es un servicio completamente gestionado
- Se personaliza automáticamente según tus datos
- Aprende continuamente con el tiempo (retroalimentación)
- Proporciona información sobre la importancia de las características (llamadas "model variables" en Fraud Detector)
- Puede ingerir datos desde Amazon S3 o a través de API
- Ofrece acciones basadas en reglas que puedes definir cuando se detecta algo potencialmente fraudulento
- Se integra con Amazon SageMaker para crear sistemas personalizados más complejos

### 13.4 Funcionamiento

El proceso de funcionamiento de Amazon Fraud Detector es el siguiente:

1. **Ingesta de datos**: Los datos de entrenamiento provienen de S3 o a través de una API
2. **Procesamiento interno**: Amazon Fraud Detector gestiona automáticamente:
   - ETL (Extracción, Transformación y Carga de datos)
   - Ingeniería de características
   - Selección de modelos
   - Entrenamiento de modelos
   - Ajuste de modelos
   - Validación de modelos
   - Despliegue de modelos a endpoints
3. **Inferencia**: A través de la API de predicción de Fraud Detector, puedes consultar si una nueva transacción es potencialmente fraudulenta

### 13.5 Pasos para implementación

1. **Selección de caso de uso**: Elegir el tipo de fraude que quieres detectar (fraude de cuenta, etc.)
2. **Creación de tipo de evento**: 
   - Definir la fuente de datos para el evento
   - Definir las etiquetas que intentas predecir
   - Configurar los permisos IAM necesarios para acceder a los datos
3. **Creación del modelo**: El servicio optimiza y entrena el modelo automáticamente
4. **Revisión del rendimiento**: Verificar que el modelo detecta fraudes adecuadamente sin falsos positivos excesivos
5. **Despliegue del modelo**: Una vez satisfecho con el rendimiento, desplegar el modelo para uso en producción

Amazon Fraud Detector simplifica enormemente la implementación de sistemas de detección de fraude sin necesidad de gestionar la infraestructura subyacente ni los detalles técnicos de los modelos de machine learning.

## 14. Amazon Q Business

### 14.1 Introducción a Amazon Q Business

Amazon Q Business es un asistente de IA generativa completamente gestionado diseñado específicamente para empleados de una empresa. A diferencia de los asistentes IA generales, Amazon Q Business se basa enteramente en el conocimiento y datos de tu propia compañía.

### 14.2 Capacidades principales

- Responder preguntas basadas en los datos internos de la empresa
- Proporcionar resúmenes de documentos corporativos
- Generar contenido corporativo personalizado
- Automatizar tareas rutinarias (solicitudes de tiempo libre, envío de invitaciones a reuniones)

### 14.3 Arquitectura y funcionamiento

Amazon Q Business está construido sobre Amazon Bedrock, pero utiliza múltiples modelos fundacionales. Es un servicio de más alto nivel orientado al caso de uso específico de exponer los datos internos de una empresa a través de una interfaz de LLM generativa.

#### 14.3.1 Componentes clave

**Data Connectors (RAG gestionado)**:
- Permiten conectar con más de 40 fuentes de datos empresariales populares:
  - Servicios AWS: Amazon S3, Amazon RDS, Aurora, WorkDocs
  - Servicios externos: Microsoft 365, Salesforce, Google Drive, Gmail, Slack, SharePoint
- Amazon Q Business realiza el rastreo de estas fuentes para permitir búsquedas y consultas

**Plugins**:
- Permiten a Amazon Q Business interactuar con servicios de terceros
- Integración con: Jira, ServiceNow, Zendesk, Salesforce, etc.
- Capacidad para crear acciones (crear tickets, solicitudes, etc.)
- Posibilidad de crear plugins personalizados para conectar con cualquier aplicación de terceros mediante APIs

### 14.4 Seguridad y autenticación

**IAM Identity Center**:
- Los usuarios se autentican a través de IAM Identity Center
- Garantiza que los usuarios solo tengan acceso a los documentos que les corresponden según sus permisos
- Ofrece una interfaz de inicio de sesión simple con nombre de usuario y contraseña
- Permite integración con proveedores de identidad externos (IDP):
  - Google login
  - Microsoft Active Directory
  - Otros sistemas de autenticación corporativos

### 14.5 Controles administrativos

Los controles administrativos permiten personalizar las respuestas según las necesidades de la organización, similares a los Guardrails de Amazon Bedrock:

- Bloqueo de temas específicos
- Restricción para responder solo con información interna vs. conocimiento externo
- Configuración a nivel global o a nivel de tema específico

Amazon Q Business proporciona una forma segura y efectiva de aprovechar la IA generativa dentro del contexto específico de una empresa, manteniendo la seguridad de los datos y respetando las estructuras de permisos existentes.

## 15. Amazon Q Developer

### 15.1 Introducción a Amazon Q Developer

Amazon Q Developer es un servicio de IA con dos funcionalidades principales: un asistente para consultas sobre AWS y un compañero de código IA.

### 15.2 Asistente para consultas sobre AWS

La primera faceta de Amazon Q Developer permite:

- Responder preguntas sobre documentación de AWS y selección de servicios AWS
- Consultar información sobre recursos en tus cuentas AWS
- Sugerir comandos CLI (Command Line Interface) para realizar cambios en tus cuentas
- Analizar tu factura de AWS
- Resolver errores y realizar troubleshooting

**Ejemplos de uso:**
- Solicitud: "Lista todas mis funciones Lambda" → Amazon Q responderá con todas las funciones Lambda en regiones específicas
- Solicitud: "Cambia el timeout de la función Lambda Test API1 en la región de Singapur a 10 segundos" → Amazon Q proporcionará el comando CLI exacto para ejecutar este cambio
- Solicitud: "¿Cuáles fueron los tres servicios con mayor costo en el primer trimestre de mis cuentas?" → Amazon Q analizará y presentará los servicios más costosos (ej. Amazon SageMaker, Amazon Elastic Container Service, AWS Config)

### 15.3 Compañero de código IA

La segunda faceta de Amazon Q Developer funciona como un asistente de programación:

- Permite codificar nuevas aplicaciones (similar a GitHub Copilot)
- Está especializado en desarrollos basados en AWS
- Soporta múltiples lenguajes: Java, JavaScript, Python, TypeScript, C# (con más por venir)
- Ofrece sugerencias de código en tiempo real mientras programas
- Realiza escaneos de seguridad
- Incluye un agente de software para implementar características, generar documentación o crear la estructura básica para nuevos proyectos

**Compatibilidad con IDEs:**
- Visual Studio Code
- Visual Studio
- JetBrains

**Funcionalidades de código:**
- Responder consultas sobre desarrollo en AWS
- Completar y generar código
- Escanear vulnerabilidades de seguridad
- Depurar, optimizar y mejorar código

Amazon Q Developer representa una herramienta potente para desarrolladores que trabajan con AWS, combinando asistencia para consultas sobre la plataforma con capacidades avanzadas de generación de código específicas para el ecosistema AWS.