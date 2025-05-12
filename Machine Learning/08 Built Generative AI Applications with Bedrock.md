# Building Generative AI Applications with Bedrock

## 1. Building Generative AI with Amazon Bedrock and Foundation Models

### 1.1. Introducción a Amazon Bedrock

Amazon Bedrock es la base de las capacidades de IA generativa de AWS. Es esencialmente una API para construir aplicaciones sobre foundation models, entrenarlos, ajustarlos (fine-tuning) y desarrollar servicios alrededor de ellos.

#### 1.1.1. Foundation Models
Los foundation models son simplemente otro nombre para los modelos grandes de lenguaje (LLMs) o modelos de generación de imágenes que se utilizan en AWS:

- Amazon tiene su propio modelo llamado **Titan**
- OpenAI no está en la lista porque trabaja con competidores de Amazon
- Incluye modelos de **Meta** y **Anthropic** (Claude)
- Amazon ha invertido significativamente en Anthropic, y los modelos Claude son tan buenos o mejores que los de GPT

### 1.2. Características Principales de Bedrock

Bedrock funciona con un enfoque de marketplace:

- Integra modelos de terceros y de Amazon (Titan)
- Proporciona una **API unificada** para usar todos los modelos de manera similar
- Te factura a través de AWS con los modelos de precios de cada proveedor
- Permite **fine-tuning** de algunos de estos modelos
- Admite traer tus propios modelos
- Ofrece soporte para **Retrieval Augmented Generation (RAG)**
- Incluye soporte para **LM agents** (agentes de modelos de lenguaje)

#### 1.2.1. Plataforma Serverless
- No necesitas preocuparte por gestionar servidores para entrenamiento o inferencia
- La IA generativa puede ser costosa, pero no tienes que gestionar la infraestructura
- Si necesitas más control, puedes usar SageMaker

#### 1.2.2. Integración con SageMaker
- Bedrock se integra con **SageMaker Canvas**
- Bedrock puede usarse como base para proyectos de SageMaker
- SageMaker sigue siendo el paraguas para las soluciones de IA de Amazon

### 1.3. API Endpoints de Bedrock

Bedrock ofrece cuatro tipos diferentes de API endpoints:

#### 1.3.1. Bedrock API
- Utilizado para gestionar los modelos subyacentes
- Para desplegar y entrenar/ajustar modelos
- Para todo lo que haces antes de usar el modelo en producción

#### 1.3.2. Bedrock Runtime
- Utilizado para realizar inferencia (generar respuestas en tiempo real)
- Para ejecutar prompts contra los modelos y obtener respuestas
- Para generar embeddings o imágenes a partir de texto

Operaciones comunes:
- **converse**: Enfoque de chat
- **invoke_model**: Enfoque de una sola vez (one-shot)
- Versiones de streaming: **converse_stream** e **invoke_model_with_response_stream**
  - Stream: devuelve la respuesta palabra por palabra en lugar de todo a la vez

#### 1.3.3. Bedrock Agent
- Para gestionar **LM agents** (modelos con herramientas adicionales)
- Permite proporcionar al modelo herramientas para distintas tareas (obtener clima, buscar en documentos, consultar bases de datos, etc.)
- API para gestionar, desplegar y entrenar agentes y bases de conocimiento
- Permite integrar tus propios datos en un foundation model

#### 1.3.4. Bedrock Agent Runtime
- Realiza inferencia con agentes y bases de conocimiento
- Métodos principales: **invoke_agent**, **retrieve** y **retrieve_and_generate**

### 1.4. Seguridad y Permisos

#### 1.4.1. Requisitos para usar Bedrock
- Debes usar Bedrock con un usuario IAM, no con la cuenta root
- El usuario IAM debe tener los permisos relevantes:
  - **AmazonBedrockFullAccess**
  - **AmazonBedrockReadOnly**
  - Los usuarios administrativos incluyen estos permisos automáticamente

#### 1.4.2. Acceso a Foundation Models
- Debes solicitar acceso a los foundation models antes de usarlos
- Es la forma en que Amazon asegura que aceptas los términos de licencia (EULA)
- Los modelos Titan de Amazon se aprueban inmediatamente
- Para otros modelos, la aprobación suele tardar unos minutos
- Debes solicitar acceso por adelantado para evitar sorpresas al desarrollar

### 1.5. Facturación y Precios

- Los terceros establecen sus propios precios
- Te facturan a través de AWS, no recibirás facturas separadas
- Los precios no son evidentes cuando apruebas el acceso
- Verifica los precios en aws.amazon.com/bedrock/pricing
- Algunos modelos son más caros que otros

### 1.6. Experimentación con Bedrock

Bedrock proporciona diferentes "playgrounds" para experimentar:
- Interfaz de chat
- Interfaz de generación de texto
- Interfaz de generación de imágenes

Estos playgrounds permiten:
- Probar rápidamente los foundation models
- Evaluar modelos personalizados
- Probar modelos importados de otros lugares

---

## Resumen para el Examen: Building Generative AI with Amazon Bedrock

Amazon Bedrock es la plataforma serverless de AWS para construir aplicaciones de IA generativa utilizando foundation models (modelos de lenguaje o de generación de imágenes). Ofrece un enfoque de marketplace que integra modelos de terceros como Anthropic (Claude) y Meta, junto con el propio modelo Titan de Amazon. Bedrock proporciona una API unificada para usar estos modelos, permitiendo fine-tuning, soporte para RAG (Retrieval Augmented Generation) y LM agents. Tiene cuatro tipos de API endpoints: Bedrock API (para gestión), Bedrock Runtime (para inferencia), Bedrock Agent (para gestión de agentes) y Bedrock Agent Runtime (para inferencia de agentes). Para usar Bedrock, debes hacerlo con un usuario IAM con los permisos adecuados y solicitar acceso a los modelos antes de usarlos.

### Consejo para el Examen
Para el examen AWS Certified Machine Learning - Associate, enfócate en recordar la diferencia entre los cuatro tipos de API endpoints de Bedrock y sus funciones específicas (Bedrock API, Bedrock Runtime, Bedrock Agent y Bedrock Agent Runtime). Es importante entender que debes solicitar acceso a los foundation models antes de usarlos (excepto Titan que se aprueba automáticamente) y que debes usar un usuario IAM para acceder a Bedrock, no la cuenta root. También recuerda que Bedrock factura los modelos de terceros a través de AWS y que los precios varían según el modelo, por lo que es una buena práctica verificar los costos antes de implementar una solución.



## 2. Fine-Tuning Custom Models and Continuous Pre-Training with Bedrock

### 2.1. Conceptos Básicos de Fine-Tuning

El fine-tuning consiste en extender el entrenamiento del foundation model subyacente que estás utilizando. Permite adaptar un Large Language Model (LLM) existente o un modelo de generación de imágenes a un caso de uso específico.

#### 2.1.1. Ventajas del Fine-Tuning
- **Integración profunda**: Entrenas el modelo con nuevos datos y los incorporas directamente en el modelo
- **No es una solución superficial**: En lugar de "pegar" información encima del modelo, realmente mejoras el modelo con tu propia información
- **Elimina la necesidad de prompt engineering extensivo**: No necesitas incluir ejemplos de tus datos en cada prompt
- **Ahorro de tokens a largo plazo**: Como la información ya es parte del modelo, no necesitas inyectarla en cada prompt
- **Potencialmente más económico a largo plazo**: Puede ser menos costoso que incluir información en cada prompt como en prompt engineering o RAG

#### 2.1.2. Consideraciones Importantes
- **Costoso inicialmente**: El proceso de fine-tuning requiere recursos significativos
- **Consume tiempo**: El entrenamiento puede ser prolongado

### 2.2. Uso de Modelos Fine-Tuned en Bedrock

Una vez que tu modelo está fine-tuned (Amazon Bedrock los llama "custom models"), puedes usarlo como cualquier otro modelo:

- La API es la misma
- El modelo ahora tiene conocimiento adicional sobre lo que lo entrenaste
- Puedes continuar haciendo fine-tuning sobre modelos ya ajustados
- El modelo puede mejorar con el tiempo al agregar nueva información

### 2.3. Aplicaciones del Fine-Tuning

#### 2.3.1. Casos de Uso Comunes
- **Chatbots con personalidad específica**: Enseñar al modelo a comunicarse con un estilo particular
- **Escritura con voz de marca**: Crear contenido que refleje el tono y estilo de comunicación de una marca
- **Actualización de conocimientos**: Entrenar con datos más recientes que el cutoff date del LLM original
- **Incorporación de datos propietarios**:
  - Emails y mensajes para crear un "clon" de comunicación
  - Transcripciones de soporte al cliente para aplicaciones de servicio
  - Datos específicos de un dominio empresarial

#### 2.3.2. Aplicaciones Específicas
- **Clasificación**: Entrenar para categorizar contenido
- **Verificación**: Evaluar si algo es verdadero o falso
- **Tareas especializadas**: Ajustar el modelo para realizar funciones para las que no fue diseñado originalmente

### 2.4. Custom Models en Bedrock

En Bedrock, los modelos fine-tuned se denominan "custom models":

- **Modelos disponibles**: Titan, Cohere y Meta pueden ser fine-tuned (verifica la tarjeta de información del modelo)
- **No todos los modelos permiten fine-tuning**: Es importante verificar la compatibilidad antes de intentarlo

#### 2.4.1. Proceso de Fine-Tuning
El proceso funciona proporcionando pares de preguntas y respuestas:

- Se utiliza un formato JSON simple con pares de "prompts" y "completions"
- Estos son ejemplos de cómo quieres que tu modelo responda a ciertas preguntas
- El modelo puede generalizar y responder a preguntas no vistas siguiendo el patrón
- Los datos se suben a S3 y Bedrock crea un custom model basado en ellos

```json
{
  "prompt": "¿Qué es la AWS Certified Machine Learning - Associate?",
  "completion": "La AWS Certified Machine Learning - Associate es una certificación que valida la capacidad para diseñar, implementar, desplegar y mantener soluciones de machine learning en la plataforma AWS."
}
```

#### 2.4.2. Fine-Tuning para Modelos de Imágenes
- Puedes proporcionar prompts y enlaces a imágenes en S3
- Útil para modelos text-to-image y image-to-embedding
- Permite ajustar el modelo a tu estilo visual específico

### 2.5. Seguridad en Fine-Tuning

Para realizar fine-tuning con información propietaria, segura o sensible:

- **Método recomendado**: Usar VPC con PrivateLink
- Esta configuración asegura que los datos sensibles permanezcan protegidos durante el proceso de fine-tuning
- **Importante para el examen**: Si quieres hacer custom models en Bedrock usando información sensible, debes usar VPC con PrivateLink

### 2.6. Consideraciones de Costo y Tiempo

- El fine-tuning puede ser costoso debido a la computación intensiva
- Toma tiempo considerable completar el proceso
- A largo plazo puede ser más económico que otras alternativas
- Algunas personas prefieren enfoques más simples como RAG por estas razones

### 2.7. Continued Pre-Training en Bedrock

Existe otra forma de fine-tuning llamada "continued pre-training" en Bedrock:

#### 2.7.1. Diferencia con Custom Models
- **Datos no etiquetados**: A diferencia del fine-tuning regular que usa pares de prompt-completion
- **Solo texto sin estructura**: Proporciona bloques de texto sin formato específico
- **Ejemplo**: Documentos empresariales que quieres incorporar al conocimiento del modelo

#### 2.7.2. Proceso y Ventajas
- Subir documentos a S3 y solicitar a Bedrock que realice continued pre-training
- **Ventaja principal**: Incorpora información al modelo sin necesidad de incluirla en los prompts
- Información "horneada" en el modelo desde el principio
- Similar al fine-tuning regular en términos de costo y tiempo

### 2.8. Uso de Modelos Personalizados

- El modelo personalizado resultante (sea por fine-tuning o continued pre-training) se utiliza como cualquier otro modelo
- Se te asigna un nuevo identificador de modelo
- Usas este identificador en lugar del foundation model original
- El proceso de inferencia es idéntico al de cualquier otro modelo

---

## Resumen para el Examen: Fine-Tuning Custom Models with Bedrock

El fine-tuning en Amazon Bedrock permite extender los foundation models con datos específicos, creando "custom models". Existen dos métodos principales: fine-tuning estándar (con pares de prompt-completion) y continued pre-training (con datos no etiquetados). Estos procesos integran profundamente la información en el modelo, eliminando la necesidad de prompt engineering extensivo y potencialmente ahorrando tokens a largo plazo. Los modelos Titan, Cohere y Meta pueden ser fine-tuned, subiendo los datos a S3 para que Bedrock cree el modelo personalizado. Para aplicaciones con datos sensibles, se debe usar VPC con PrivateLink. Aunque el proceso es costoso y consume tiempo inicialmente, puede ser más eficiente a largo plazo que otras técnicas como RAG o prompt engineering extensivo.

### Consejo para el Examen
Para el examen AWS Certified Machine Learning - Associate, es crucial entender la diferencia entre fine-tuning regular (usando pares de prompt-completion) y continued pre-training (usando datos no etiquetados). Recuerda que no todos los foundation models soportan fine-tuning, así que debes verificar la compatibilidad antes de comenzar. Un punto clave de seguridad que podría aparecer en el examen es que para realizar fine-tuning con datos sensibles, debes utilizar VPC con PrivateLink. También ten presente las ventajas a largo plazo (ahorro de tokens, consistencia) frente a los costos iniciales (tiempo y dinero) del fine-tuning en comparación con otras técnicas como RAG.



## 3. Retrieval-Augmented Generation (RAG) Fundamentals with Bedrock

### 3.1. Conceptos Básicos de RAG

En Amazon Bedrock, Retrieval-Augmented Generation (RAG) se implementa como **knowledge base**. Para entender las knowledge bases, primero debemos comprender qué es RAG.

#### 3.1.1. Definición y Funcionamiento
- RAG puede compararse con un "examen a libro abierto" para Large Language Models (LLMs)
- En lugar de confiar solo en el conocimiento del modelo, consulta una base de datos externa
- Enfoque "asistido" para obtener información más precisa y actualizada

#### 3.1.2. Proceso General de RAG
El proceso de RAG sigue estos pasos:
1. Se recibe un prompt del usuario (ej: "¿Qué dijo el presidente en su discurso de ayer?")
2. El sistema busca en una base de datos externa información relevante a ese prompt
3. La información recuperada se incorpora al prompt original
4. El prompt aumentado se envía al foundation model para generar una respuesta

**Ejemplo de transformación de prompt**:
- **Prompt original**: "¿Qué dijo el presidente en su discurso de ayer?"
- **Prompt aumentado**: "¿Qué dijo el presidente en su discurso de ayer? Considera el siguiente artículo de noticias en tu respuesta: Anoche el presidente dijo..."

### 3.2. Ventajas de RAG (Knowledge Base)

#### 3.2.1. Eficiencia y Actualización
- **Más rápido y económico a corto plazo**: Comparado con fine-tuning para incorporar nueva información
- **Actualización sencilla**: No es necesario re-entrenar el modelo cuando hay nueva información
- **Facilidad de mantenimiento**: Solo se actualiza la base de datos/knowledge base

#### 3.2.2. Capacidades de Búsqueda Semántica
- Utiliza **semantic search** para encontrar información relevante
- Habitualmente implementado con **vector databases**
- Usa **embeddings** que codifican el significado semántico del texto
- Encuentra pasajes de texto semánticamente similares al prompt del usuario

#### 3.2.3. Reducción de Alucinaciones
- Puede prevenir que el modelo "invente" información
- Proporciona datos reales como referencia en el prompt
- Útil para consultas sobre información posterior al cutoff date del modelo
- Mejora la precisión en temas específicos o propietarios

#### 3.2.4. Aplicaciones Prácticas
- Implementación de búsquedas potenciadas por IA
- Acceso a información propietaria sin entrenar directamente el modelo
- Respuestas basadas en fuentes de datos actualizadas

### 3.3. Desventajas y Limitaciones de RAG

#### 3.3.1. Consideraciones de Costo y Eficiencia
- **Aumento de tokens**: Incorporar datos externos en cada prompt incrementa el número de tokens
- A largo plazo, puede ser menos económico que fine-tuning para información frecuentemente utilizada
- Las vector databases consumen considerable espacio de almacenamiento y recursos computacionales

#### 3.3.2. Desafíos Técnicos
- **Sensibilidad al prompt template**: La forma de incorporar la información recuperada afecta los resultados
- **No determinístico**: Puede ser difícil de probar sistemáticamente
- **Posibilidad de alucinaciones**: No elimina completamente el problema de invención de información

#### 3.3.3. Calidad de Resultados
- **Altamente dependiente de la relevancia**: La calidad depende de encontrar información verdaderamente pertinente
- **Fragmentación de información**: Si los chunks de texto no contienen pensamientos coherentes, los resultados serán pobres
- **Complejidad de implementación**: Requiere ajuste cuidadoso para obtener buenos resultados

### 3.4. Arquitectura de un Sistema RAG

#### 3.4.1. Componentes Principales
Un sistema RAG típico consta de:
- **Módulo de consulta (Query Module)**: Procesa la pregunta del usuario
- **Módulo recuperador (Retriever)**: Busca información relevante en la base de datos
- **Base de conocimiento (Knowledge Base)**: Almacena la información externa
- **Generador (Generator)**: El foundation model que produce la respuesta final

#### 3.4.2. Ejemplo Práctico: Sistema para Responder Preguntas de Trivia
Proceso para un sistema RAG que responde a una pregunta tipo Jeopardy:

1. **Entrada**: "En 1986, México se convirtió en el primer país en albergar esta competencia deportiva internacional por segunda vez"
2. **Embedding**: La consulta se convierte en un vector de embedding
3. **Recuperación**: Se buscan documentos relevantes sobre México, deportes y 1986
4. **Aumentación**: Los documentos recuperados se incorporan al prompt
5. **Generación**: El foundation model genera la respuesta ("¿Qué es la Copa Mundial?")

### 3.5. Tipos de Almacenamiento para RAG

#### 3.5.1. Vector Databases
- Almacenan embeddings de textos o documentos
- Permiten búsquedas basadas en similitud semántica
- Más efectivas para encontrar información conceptualmente relacionada que las búsquedas por palabras clave

#### 3.5.2. Enfoques Híbridos
- Combinan bases de datos tradicionales con vector stores
- Pueden mejorar la precisión de la recuperación
- Permiten filtrado adicional basado en metadatos

### 3.6. Implementación en Amazon Bedrock

En Bedrock, RAG se implementa a través de **knowledge bases**:
- Puedes crear y gestionar bases de conocimiento con información propietaria
- Se integra directamente con foundation models disponibles en Bedrock
- Ofrece APIs para recuperación y generación combinadas
- Se puede usar junto con Bedrock Agents para crear aplicaciones más complejas

### 3.7. Consideraciones de Diseño para Knowledge Bases en Bedrock

#### 3.7.1. Segmentación de Datos
- La división de documentos en chunks debe ser cuidadosamente planificada
- Los chunks deben contener unidades coherentes de información
- El tamaño de chunk afecta directamente la calidad de las respuestas

#### 3.7.2. Selección de Modelos de Embedding
- Diferentes modelos de embedding tienen diferentes características
- La elección del modelo afecta la calidad de la recuperación
- Debe ser compatible con el foundation model utilizado

#### 3.7.3. Optimización de Consultas
- Las consultas pueden ser reformuladas para mejorar la recuperación
- La cantidad de documentos recuperados debe ser ajustada
- Los prompts deben estructurarse para maximizar la utilidad de la información recuperada

---

## Resumen para el Examen: Retrieval-Augmented Generation (RAG) with Bedrock

Retrieval-Augmented Generation (RAG) en Amazon Bedrock se implementa como knowledge bases que permiten mejorar las respuestas de los foundation models con información externa. RAG funciona como un "examen a libro abierto" donde el modelo consulta una base de datos antes de responder. El proceso incluye recibir un prompt, buscar información relevante, incorporarla al prompt original y enviar este prompt aumentado al foundation model. Las principales ventajas de RAG incluyen la rapidez para incorporar nueva información sin reentrenamiento, actualización sencilla y búsqueda semántica mediante vector databases. RAG puede reducir alucinaciones al proporcionar datos reales como referencia. Sin embargo, también presenta desventajas como el incremento en tokens, sensibilidad al prompt template y dependencia de la relevancia de la información recuperada.

### Consejo para el Examen
Para el examen AWS Certified Machine Learning - Associate, enfócate en entender las diferencias clave entre RAG (knowledge bases en Bedrock) y fine-tuning (custom models). Recuerda que RAG es más eficiente a corto plazo para incorporar nueva información, mientras que fine-tuning puede ser más eficiente a largo plazo para información frecuentemente utilizada. Presta atención al flujo de trabajo de RAG: prompt → búsqueda en knowledge base → incorporación de información → generación de respuesta. Es importante recordar que la calidad de las respuestas depende principalmente de la relevancia de la información recuperada y de cómo se incorpora al prompt, por lo que la segmentación adecuada de documentos (chunking) y la estructura del prompt son fundamentales para una implementación exitosa.




## 4. Vector Stores and Embeddings with Amazon Bedrock Knowledge Bases

### 4.1. Data Stores para Knowledge Bases

Un componente crítico de RAG (en Bedrock denominado "knowledge base") es el data store donde se almacena la información a consultar. La calidad de las respuestas depende directamente de la capacidad para recuperar resultados relevantes al prompt.

#### 4.1.1. Tipos de Data Stores
Existen diferentes opciones para implementar el data store de una knowledge base:

- **Graph Databases**: Ideales para datos de tipo grafo (recomendaciones, relaciones entre productos, knowledge graphs)
  - Ejemplo: Neo4j
- **Traditional Search Engines**: Para búsqueda de texto tradicional
  - Ejemplo: OpenSearch usando TF-IDF (Term Frequency-Inverse Document Frequency)
- **Vector Databases**: Para búsqueda semántica usando embeddings
  - La opción más común en implementaciones modernas de RAG
- **Hybrid Approaches**: Combinan técnicas tradicionales con búsqueda semántica
  - Pueden ofrecer mejores resultados en ciertos casos

#### 4.1.2. Preferencia por Vector Databases
Los vector databases son la opción más popular actualmente porque:
- Aprovechan la tecnología de IA (embeddings)
- Capturan mejor el significado semántico de la información
- Pueden encontrar relaciones conceptuales que las búsquedas por palabra clave no detectarían

**Nota importante**: En Amazon Bedrock, OpenSearch puede funcionar como vector database y es la opción recomendada para implementar knowledge bases.

### 4.2. Comprendiendo los Embeddings

Para entender cómo funcionan los vector stores, primero debemos comprender qué son los embeddings.

#### 4.2.1. Definición de Embeddings
- Un embedding es un vector (array de números) asociado a datos (texto, imágenes, etc.)
- Representan información en un espacio multidimensional
- Generalmente tienen cientos o miles de dimensiones
- Pueden considerarse como "puntos" en un espacio multidimensional

#### 4.2.2. Características Clave
- **Similitud semántica**: Items con significados similares están cerca en el espacio vectorial
- **Ejemplo**: Las palabras "potato" y "rhubarb" pueden estar cerca por ser ambas vegetales
- **Agrupación temática**: Conceptos relacionados (como "Starship Enterprise", "Spaceballs", "The Orville") se agrupan en la misma región del espacio vectorial por ser naves espaciales de ciencia ficción

#### 4.2.3. Generación de Embeddings
- Se crean usando **embedding foundation models** como Titan
- Es relativamente económico generar embeddings masivamente
- El proceso aprovecha la arquitectura Transformer, donde la generación de embeddings es una fase inicial
- La generación de embeddings es una capacidad intrínseca de los LLMs

### 4.3. Vector Databases en Detalle

#### 4.3.1. Estructura y Funcionamiento
Una vector database almacena:
- El contenido original (texto, imágenes, etc.)
- Los vectores de embedding asociados
- Metadatos adicionales (opcional)

#### 4.3.2. Proceso de Recuperación (Retrieval)
1. **Creación de embedding de consulta**: Se genera un embedding para el prompt o consulta
2. **Búsqueda de vectores similares**: Se buscan los items cuyos embeddings están más cercanos al embedding de la consulta
3. **Recuperación de top-k resultados**: Se obtienen los k items más similares (k-nearest neighbors)

#### 4.3.3. Optimizaciones de Búsqueda
- No es necesario realizar una búsqueda lineal completa
- Las vector databases implementan algoritmos de optimización para reducir el espacio de búsqueda
- Técnicas como Approximate Nearest Neighbor (ANN) permiten búsquedas eficientes incluso con millones de vectores

### 4.4. Implementaciones de Vector Databases

#### 4.4.1. Bases de Datos con Capacidades Vectoriales
Muchas bases de datos tradicionales han añadido capacidades de búsqueda vectorial:
- **OpenSearch/Elasticsearch**: Opción preferida en Bedrock por ser un producto de Amazon
- **SQL Databases**: Varias bases de datos SQL ahora soportan búsqueda vectorial
- **NoSQL Databases**: MongoDB, Cassandra, Redis, etc.
- **Graph Databases**: Neptune también puede trabajar con vectores

#### 4.4.2. Vector Databases Especializadas
Bases de datos construidas específicamente para búsqueda vectorial:
- **Pinecone**: Una de las más populares en el mercado
- **Weaviate**: Alternativa comercial
- **Chroma**: Opción open-source
- **Milvus**: Solución escalable
- **Qdrant**: Enfocada en rendimiento
- **Vespa**: Sistema de búsqueda de alto rendimiento

### 4.5. Implementación Práctica en Bedrock

#### 4.5.1. Ejemplo de Flujo de Trabajo
Caso práctico: Sistema RAG con scripts de Star Trek

1. **Consulta inicial**: "Data, tell me about your daughter Lal."
2. **Generación de embedding**: Se convierte la consulta en un embedding vector
3. **Búsqueda en vector database**: Se buscan los diálogos más cercanos semánticamente
4. **Recuperación de resultados**: Se obtienen los k diálogos más relevantes
5. **Construcción del prompt aumentado**: Se incorporan los resultados al prompt
   - Ejemplo: "You are Commander Data from Star Trek. How might Data respond to the question 'Data, tell me about your daughter Lal?' Taking into account the following related lines from Data: [líneas recuperadas]"
6. **Generación de respuesta**: El foundation model genera una respuesta basada en el prompt aumentado

#### 4.5.2. Factores que Afectan la Calidad de los Resultados
- **Valor de k**: Número de resultados similares a incluir
- **Relevancia de los resultados**: Qué tan bien se alinean con la consulta real
- **Formulación del prompt aumentado**: Cómo se incorporan los resultados en el prompt
- **Chunking**: Cómo se divide el contenido original en fragmentos para almacenar

### 4.6. Métricas de Similitud para Búsqueda Vectorial

#### 4.6.1. Distancia del Coseno (Cosine Similarity)
- La métrica más utilizada para medir la similitud entre embeddings
- Mide el coseno del ángulo entre dos vectores
- Valores más cercanos a 1 indican mayor similitud
- No se ve afectada por la magnitud de los vectores, solo por su dirección

#### 4.6.2. Otras Métricas
- **Distancia euclidiana**: Mide la distancia directa entre puntos
- **Distancia de Manhattan**: Suma de las diferencias absolutas
- **Distancia de Minkowski**: Generalización de las anteriores

### 4.7. Consideraciones para Knowledge Bases en Bedrock

#### 4.7.1. Integración con OpenSearch
- Bedrock facilita la integración directa con OpenSearch como vector database
- Permite aprovechar las capacidades de búsqueda semántica con las ventajas de OpenSearch

#### 4.7.2. Preparación de Datos
- **Chunking óptimo**: División adecuada de documentos en fragmentos coherentes
- **Generación de embeddings**: Selección del modelo adecuado para la generación
- **Metadata**: Aprovechamiento de metadatos para filtrado adicional

#### 4.7.3. Optimización de Consultas
- **Reformulación de consultas**: Modificar la consulta original para mejorar la recuperación
- **Ajuste de parámetros**: Configurar correctamente el número de resultados y umbrales de similitud
- **Evaluación de relevancia**: Monitorear y mejorar la calidad de los resultados recuperados

---

## Resumen para el Examen: Vector Stores and Embeddings with Bedrock

Las knowledge bases en Amazon Bedrock utilizan principalmente vector stores para implementar RAG (Retrieval-Augmented Generation). Los vector stores almacenan el contenido original junto con sus embeddings, que son representaciones vectoriales multidimensionales que capturan el significado semántico del contenido. Estas representaciones permiten búsquedas basadas en similitud semántica (k-nearest neighbors) en lugar de simples coincidencias de palabras clave. El proceso incluye generar un embedding para la consulta, buscar los contenidos con embeddings más similares, y recuperar los resultados más relevantes para incorporarlos al prompt. OpenSearch es la opción preferida en Bedrock por ser un producto de Amazon, aunque existen alternativas especializadas como Pinecone, Weaviate y otras. La calidad de las respuestas depende de factores como el valor de k (número de resultados a incluir), la relevancia de los resultados recuperados, y cómo se incorporan estos resultados en el prompt aumentado.

### Consejo para el Examen
Para el examen AWS Certified Machine Learning - Associate, es crucial entender la diferencia entre búsqueda tradicional y búsqueda semántica basada en embeddings. Recuerda que en Bedrock, OpenSearch es la implementación preferida para vector stores en knowledge bases. Presta atención al proceso completo: creación de embeddings → búsqueda vectorial → recuperación de top-k resultados → incorporación al prompt. Un punto importante a recordar es que la similitud del coseno (cosine similarity) es la métrica más común para medir la similitud entre embeddings. También ten presente que, aunque las vector databases ofrecen búsqueda semántica avanzada, la calidad final de las respuestas depende significativamente de cómo se divide el contenido original (chunking) y cómo se formulan los prompts aumentados.




## 5. Implementing RAG with Amazon Bedrock Knowledge Bases

### 5.1. Knowledge Bases en Amazon Bedrock

En Amazon Bedrock, el enfoque RAG (Retrieval-Augmented Generation) se implementa a través de **knowledge bases**. Estas proporcionan un marco completo para crear sistemas que aumenten las capacidades de los foundation models con información externa.

#### 5.1.1. Fuentes de Datos Compatibles
Las knowledge bases en Bedrock pueden ingerir datos de diversas fuentes:

- **Amazon S3**: Para cargar documentos propios
  - Soporta datos estructurados (JSON) y no estructurados (texto plano)
- **Web Crawler**: Permite rastrear páginas web específicas
  - Útil para sitios públicos que se tienen permiso para rastrear
- **Conectores de Terceros**: Integración directa con:
  - Confluence
  - Salesforce
  - SharePoint

### 5.2. Proceso de Creación de Knowledge Bases

#### 5.2.1. Generación de Embeddings
Para convertir los documentos en vectores que puedan almacenarse en una vector store:

- Se selecciona un **embedding model** disponible en Bedrock
  - Opciones actuales: **Cohere** o **Amazon Titan**
- Se configura la **dimensión del vector**
  - Determina cuántas dimensiones tendrá cada vector de embedding
  - Afecta la precisión y el rendimiento de las búsquedas

#### 5.2.2. Selección del Vector Store
Bedrock ofrece varias opciones para almacenar los embeddings:

- **OpenSearch**: Opción predeterminada con soporte para vector store incorporado
- **MemoryDB**: Alternativa con capacidades vectoriales
- Otras opciones compatibles:
  - **Aurora**
  - **MongoDB Atlas**
  - **Pinecone**
  - **Redis Enterprise Cloud**

#### 5.2.3. Configuración de Chunking
El chunking (división) de documentos es crucial para el rendimiento de la knowledge base:

- **Chunk Size**: Tamaño de cada fragmento (por defecto 300 caracteres)
  - Controla cuánto contenido se asocia con cada embedding
- **Overlap**: Superposición entre chunks consecutivos
  - Ayuda a mantener el contexto entre fragmentos adyacentes

**Consideraciones sobre el Chunking**:
- La división por caracteres es la más común pero no siempre la más efectiva
- Alternativas más coherentes podrían incluir:
  - División por oraciones o párrafos
  - Utilizar datos estructurados con divisiones naturales
  - Implementación de knowledge graphs mediante graph databases

### 5.3. Arquitectura de una Knowledge Base

El diagrama de flujo de una knowledge base en Bedrock incluye:

1. **Fuente de documentos**: S3, web crawler o conectores de terceros
2. **Procesamiento en Bedrock**: Generación de embeddings para cada chunk
3. **Almacenamiento**: Vector store (OpenSearch, MemoryDB u otros)
4. **Procesamiento de consultas**:
   - Recuperación de información relevante mediante búsqueda semántica
   - Incorporación del contexto recuperado al prompt original
   - Envío del prompt aumentado al foundation model
   - Generación de respuesta basada en el contexto proporcionado

### 5.4. Métodos de Uso de Knowledge Bases

#### 5.4.1. Chat with Your Document
Una forma rápida de empezar a utilizar knowledge bases:

- Disponible directamente en la consola de Bedrock
- Permite subir documentos a S3 y empezar a interactuar inmediatamente
- Construye un sistema RAG "quick and dirty" sin necesidad de programación

**Proceso de Chat with Your Document**:
1. Usuario envía una consulta
2. Se realiza búsqueda semántica en OpenSearch para encontrar documentos relevantes
3. Se recupera el contexto (documentos más relevantes)
4. Se combina el contexto con la consulta original para crear una consulta aumentada
5. La consulta aumentada se envía al foundation model
6. El modelo genera una respuesta basada en el contexto proporcionado

#### 5.4.2. Integración en Aplicaciones
Las knowledge bases pueden integrarse directamente en aplicaciones:

- Uso de las APIs de Bedrock para interactuar con knowledge bases
- Permite crear experiencias personalizadas en aplicaciones propias
- Ofrece mayor flexibilidad y control sobre el proceso RAG

#### 5.4.3. Integración con Agents
Las knowledge bases pueden formar parte de sistemas más complejos:

- **Agentic RAG**: Combinación de agents y knowledge bases
- **LM Agents**: Agentes que pueden utilizar knowledge bases como una de sus herramientas
- Permite crear aplicaciones más sofisticadas que pueden realizar acciones además de responder preguntas

### 5.5. Caso de Uso: Implementación Práctica

Un ejemplo de implementación de knowledge base en Bedrock:

- **Objetivo**: Crear un sistema RAG basado en un libro completo
- **Vector Store**: OpenSearch
- **Proceso**:
  1. Crear una knowledge base
  2. Configurar OpenSearch como vector store
  3. Subir el texto completo del libro a S3
  4. Configurar el chunking y embedding
  5. Interactuar con el contenido a través de la interfaz de chat o API

### 5.6. Consideraciones para una Implementación Efectiva

#### 5.6.1. Selección del Embedding Model
- El modelo de embedding afecta directamente la calidad de la búsqueda semántica
- Considerar la compatibilidad con el foundation model utilizado
- Evaluar el equilibrio entre dimensionalidad y rendimiento

#### 5.6.2. Optimización del Chunking
- Evaluar diferentes estrategias de chunking según el tipo de contenido
- Considerar la coherencia semántica de los chunks
- Ajustar el tamaño y overlap según los resultados obtenidos

#### 5.6.3. Selección del Vector Store
- OpenSearch es la opción predeterminada y recomendada en AWS
- Considerar otras alternativas basadas en requisitos específicos de escalabilidad o rendimiento
- Evaluar los costos asociados con cada opción

#### 5.6.4. Pruebas y Refinamiento
- Probar diferentes configuraciones y parámetros
- Evaluar la relevancia de las respuestas
- Refinar el sistema iterativamente basado en los resultados

---

## Resumen para el Examen: Implementing RAG with Bedrock Knowledge Bases

Las knowledge bases en Amazon Bedrock proporcionan una implementación completa de RAG (Retrieval-Augmented Generation) que permite mejorar las respuestas de los foundation models con información externa. El proceso incluye la ingesta de datos desde S3, web crawlers o conectores de terceros; la generación de embeddings utilizando modelos como Cohere o Amazon Titan; el almacenamiento en vector stores como OpenSearch o MemoryDB; y la configuración del chunking para dividir los documentos en fragmentos manejables. Para utilizar una knowledge base, se puede emplear la funcionalidad "Chat with Your Document" en la consola de Bedrock, integrarla en aplicaciones mediante las APIs de Bedrock, o incluirla como parte de un sistema de LM Agents (Agentic RAG). La calidad de las respuestas depende en gran medida de la estrategia de chunking, la selección del modelo de embedding y el vector store utilizado.

### Consejo para el Examen
Para el examen AWS Certified Machine Learning - Associate, enfócate en comprender el flujo completo de implementación de knowledge bases en Bedrock: fuentes de datos → generación de embeddings → almacenamiento en vector store → procesamiento de consultas → respuesta aumentada. Recuerda que OpenSearch es la opción predeterminada para vector stores en Bedrock y que el chunking adecuado es crucial para obtener resultados relevantes. Es importante conocer las diferentes formas de utilizar knowledge bases (consola, APIs, integración con agents) y las opciones de modelos de embedding disponibles (Cohere y Amazon Titan). También ten presente que las knowledge bases pueden ingerir datos no solo de S3, sino también de web crawlers y conectores de terceros como Confluence, Salesforce y SharePoint.




## 6. Content Filtering with Amazon Bedrock Guardrails

### 6.1. Introducción a Bedrock Guardrails

Amazon Bedrock Guardrails es una característica fundamental para garantizar la seguridad, gobernanza y control de contenido en aplicaciones de IA generativa. Proporciona mecanismos de filtrado tanto para prompts (entradas) como para respuestas (salidas) en sistemas basados en Bedrock.

#### 6.1.1. Propósito y Aplicación
- Filtrado bidireccional de contenido (entrada y salida)
- Aplicable a cualquier sistema de Bedrock (knowledge bases, agents, etc.)
- Actualmente funciona con foundation models de texto (Titan, Claude, etc.)
- No disponible aún para modelos de generación de imágenes

### 6.2. Capacidades de Filtrado

#### 6.2.1. Niveles de Filtrado
Bedrock Guardrails permite filtrar contenido en dos niveles principales:

- **Nivel de palabra**: Filtrado de palabras específicas
- **Nivel de tema**: Filtrado de temas completos

#### 6.2.2. Métodos de Filtrado
Existen diferentes enfoques para implementar filtros:

- **Filtrado específico**: Lista definida de palabras o temas prohibidos
  - Ejemplo: Prohibir menciones a competidores
  - Ejemplo: Evitar temas como religión o política
  
- **Filtrado automático**: Basado en categorías predefinidas de contenido objetable
  - Categorías como: contenido hateful, sesgado, o inapropiado
  - Niveles de ajuste configurable por categoría

#### 6.2.3. Filtrado de Profanidad
- Filtro incorporado para lenguaje inapropiado
- No requiere que el usuario ingrese manualmente palabras ofensivas
- Se activa mediante una simple opción

### 6.3. Protección de Información Sensible

#### 6.3.1. Filtrado de PII (Personally Identifiable Information)
Bedrock Guardrails puede detectar y manejar automáticamente información personal:

- **Tipos de PII detectables**:
  - Números de teléfono
  - Números de Seguro Social
  - Números de identificación o licencias
  - Direcciones físicas
  - Otros datos personales configurables

#### 6.3.2. Opciones de Manejo de PII
- **Eliminación**: Remover completamente la información personal
- **Enmascaramiento**: Reemplazar la información con etiquetas descriptivas
  - Ejemplo: Una dirección real se reemplaza por "[DIRECCIÓN]"
- Configuración personalizable por tipo de PII

### 6.4. Contextual Grounding Check

Una característica avanzada en Bedrock Guardrails diseñada para reducir alucinaciones en modelos de IA:

#### 6.4.1. Métricas Principales
El Contextual Grounding Check mide dos aspectos clave:

- **Grounding (Fundamentación)**: Evalúa cuánto se basa la respuesta en los datos contextuales proporcionados
  - Mide la similitud entre la respuesta y los datos recuperados de la knowledge base
  - Permite establecer un umbral mínimo de fundamentación
  - Filtra respuestas que "inventan" información no presente en los datos de contexto

- **Relevance (Relevancia)**: Evalúa cuán pertinente es la respuesta a la consulta original
  - Mide la alineación semántica entre pregunta y respuesta
  - Permite filtrar respuestas que se desvían del tema consultado

#### 6.4.2. Funcionamiento con RAG
En sistemas de Retrieval-Augmented Generation (RAG):
- Evalúa si la respuesta se basa en la información recuperada del vector store
- Detecta cuando el modelo se apoya más en su conocimiento previo que en los datos proporcionados
- Ayuda a garantizar que las respuestas estén "ancladas" a la documentación oficial o aprobada

#### 6.4.3. Limitaciones
- La efectividad depende de la calidad del vector store y sus resultados
- Si el sistema de recuperación obtiene información irrelevante, el grounding check puede ser menos preciso
- No es una solución perfecta, pero proporciona una capa adicional de control de calidad

### 6.5. Integración con Sistemas Bedrock

#### 6.5.1. Aplicación Universal
Los guardrails se pueden incorporar en diversos componentes de Bedrock:
- Knowledge bases (sistemas RAG)
- Agents
- Aplicaciones personalizadas que utilizan foundation models

#### 6.5.2. Mensajes de Bloqueo Personalizados
- Cuando un contenido es filtrado, se puede configurar el mensaje que ve el usuario
- Permite mantener una experiencia de usuario coherente con la marca y el tono de la aplicación
- Ofrece la oportunidad de dar retroalimentación constructiva al usuario

### 6.6. Implementación de Guardrails

#### 6.6.1. Proceso de Configuración
La configuración de guardrails implica:
- Selección de los tipos de filtrado a aplicar
- Definición de umbrales y categorías específicas
- Personalización de mensajes de bloqueo
- Integración con los sistemas de Bedrock existentes

#### 6.6.2. Pruebas y Ajustes
- Se recomienda probar extensivamente los guardrails antes de desplegarlos
- Los umbrales pueden requerir ajustes basados en el comportamiento observado
- La retroalimentación de usuarios puede ayudar a refinar las reglas de filtrado

### 6.7. Consideraciones de Gobernanza y Cumplimiento

#### 6.7.1. Casos de Uso Recomendados
- Aplicaciones de cara al cliente donde el contenido inapropiado representa un riesgo
- Sistemas que manejan información sensible o regulada
- Chatbots corporativos donde la precisión es crucial
- Aplicaciones educativas o dirigidas a audiencias vulnerables

#### 6.7.2. Auditoría y Monitoreo
- Los guardrails pueden complementarse con sistemas de monitoreo
- El análisis de contenido bloqueado puede revelar patrones de uso indebido
- Útil para demostrar cumplimiento con políticas internas o regulaciones externas

---

## Resumen para el Examen: Content Filtering with Bedrock Guardrails

Amazon Bedrock Guardrails proporciona mecanismos de filtrado de contenido bidireccional para aplicaciones de IA generativa, protegiéndolas de contenido inapropiado tanto en los prompts como en las respuestas. Ofrece filtrado a nivel de palabra y tema, con opciones para prohibir contenido específico o utilizar categorías predefinidas de contenido objetable. Incluye un filtro de profanidad incorporado y capacidades para detectar y manejar información personal identificable (PII). Una característica avanzada es el Contextual Grounding Check, que mide la fundamentación (cuánto se basa la respuesta en los datos contextuales) y la relevancia (cuán pertinente es la respuesta a la consulta), ayudando a reducir alucinaciones. Los guardrails pueden integrarse con knowledge bases, agents y otras aplicaciones de Bedrock, y permiten personalizar los mensajes de bloqueo que ven los usuarios cuando se filtra contenido.

### Consejo para el Examen
Para el examen AWS Certified Machine Learning - Associate, enfócate en entender los diferentes tipos de filtrado que ofrece Bedrock Guardrails (palabra, tema, automático) y cómo se puede implementar tanto para entrada como para salida. Presta especial atención al Contextual Grounding Check y sus dos métricas principales (grounding y relevance), ya que este es un mecanismo clave para reducir alucinaciones en sistemas RAG. Recuerda que los guardrails funcionan actualmente con modelos de texto pero no con modelos de imágenes, y que pueden configurarse para manejar PII de diferentes maneras (eliminación o enmascaramiento). También es importante entender que los guardrails se pueden integrar con diversos componentes de Bedrock, lo que permite una estrategia coherente de gobernanza de contenido en toda la aplicación.




## 7. Building LLM Agents / Agentic AI with Amazon Bedrock Agents

### 7.1. Fundamentos de LLM Agents

Los LLM Agents (también conocidos como Agentic AI) representan una poderosa evolución en el campo de la IA generativa, permitiendo extender las capacidades de los foundation models mediante el uso de herramientas externas.

#### 7.1.1. Concepto Básico
- Un agent proporciona **tools** (herramientas) a un foundation model
- Permite al modelo acceder a datos y servicios externos
- Incorpora información externa en las respuestas generadas
- El modelo decide qué herramientas usar según el contexto

#### 7.1.2. Arquitectura Conceptual
Desde una perspectiva de alto nivel, un LLM agent típicamente incluye:

- **Core**: Basado en un foundation model (LLM)
- **Planning Module**: Determina qué herramientas usar y cómo dividir las solicitudes en sub-preguntas
- **Memory**: Mantiene el historial de la conversación
- **Tools**: Funciones externas que el agente puede invocar

### 7.2. Funcionamiento de los Agents en Bedrock

#### 7.2.1. Características Clave
- Los agents interpretan **instrucciones en lenguaje natural** para usar herramientas
- No requieren reglas codificadas rígidamente para la invocación de herramientas
- Pueden determinar cuándo y cómo usar cada herramienta basándose en descripciones textuales
- Permiten un enfoque más flexible y potente para el desarrollo de aplicaciones de IA

#### 7.2.2. Implementación Práctica
En Bedrock, los agents se implementan utilizando:

- Un **foundation model** base (como Titan, Claude, etc.)
- **Action Groups** (grupos de acciones) que contienen las herramientas
- **Lambda functions** que ejecutan la lógica de las herramientas
- Instrucciones en lenguaje natural que guían al modelo

### 7.3. Action Groups y Tools

En Amazon Bedrock, las herramientas se organizan en **Action Groups**:

#### 7.3.1. Estructura de un Action Group
- Contiene una o más Lambda functions
- Incluye instrucciones sobre cuándo usar la herramienta
- Define los parámetros necesarios para la función
- Especifica el formato de retorno esperado

#### 7.3.2. Definición de Parámetros
Para cada parámetro de una herramienta se debe definir:
- **Nombre**: Identificador del parámetro
- **Descripción**: Instrucciones en lenguaje natural sobre cuándo y cómo usar el parámetro
- **Tipo**: String, integer, boolean, etc.
- **Required**: Si es obligatorio u opcional

#### 7.3.3. Ejemplo de Action Group
Un action group para obtener información del clima podría incluir:
- **Instrucción**: "Use this function to determine the current weather in a city"
- **Parámetros**:
  - city (string, required): "The name of the city for which weather is requested"
  - units (string, optional): "Temperature units, either 'Fahrenheit' or 'Celsius'"

### 7.4. Integración con Knowledge Bases

Los agents pueden complementarse con knowledge bases para crear sistemas más completos:

#### 7.4.1. Agentic RAG
- Combinación de agents con Retrieval-Augmented Generation
- Permite al agente consultar knowledge bases además de usar herramientas
- Se conoce como "Agentic RAG" en la terminología actual

#### 7.4.2. Configuración de Knowledge Bases
- Se pueden asociar una o más knowledge bases a un agent
- Se proporcionan instrucciones en lenguaje natural sobre cuándo usar cada knowledge base
- Ejemplo: "Use this knowledge base for answering questions about self-employment"

### 7.5. Code Interpreter

Bedrock Agents ofrece una capacidad opcional de **Code Interpreter**:

#### 7.5.1. Funcionalidades
- Permite al agent escribir y ejecutar su propio código Python
- Útil para cálculos matemáticos complejos
- Facilita la generación de visualizaciones y gráficos
- Actúa como una "herramienta auto-construida" por el agent

#### 7.5.2. Casos de Uso
- Análisis de datos complejos
- Cálculos matemáticos
- Generación de gráficos basados en datos
- Resolución de problemas algorítmicos

### 7.6. Despliegue de Agents en Producción

Para utilizar los agents en un entorno de producción, se deben seguir estos pasos:

#### 7.6.1. Creación de Alias
- Un **alias** es un punto de acceso desplegado para un agent
- Representa una instantánea publicada del agent
- Permite gestionar versiones y cambios en el agent

#### 7.6.2. Opciones de Throughput
Existen dos modalidades para configurar el rendimiento:

- **On-Demand Throughput (OT)**:
  - Las cuotas se establecen a nivel de cuenta
  - Adecuado para tráfico moderado
  - Sin compromiso de capacidad

- **Provisioned Throughput (PT)**:
  - Permite comprar capacidad adicional
  - Recomendado para tráfico elevado
  - Soporta consultas más grandes y mayor volumen de tokens

#### 7.6.3. Invocación de Agents
- Se utiliza **invoke_agent** con el alias ID del agent
- Se llama al endpoint **agents for Amazon Bedrock runtime**
- Formato: `invoke_agent(aliasId=alias_id, agentEndpoint=endpoint_url)`

### 7.7. Ejemplo de Implementación Completa

Un ejemplo práctico de agent podría combinar:

#### 7.7.1. Componentes Integrados
- **Foundation Model**: Base del agent (ej. Claude, Titan)
- **Action Group**: Con Lambda function para obtener el clima
- **Knowledge Base**: Para responder preguntas sobre un tema específico (ej. autoempleo)
- **Guardrails**: Para filtrar contenido inapropiado
- **Code Interpreter**: Para cálculos y visualizaciones

#### 7.7.2. Flujo de Trabajo
1. Usuario envía una consulta
2. Agent determina si necesita:
   - Consultar la knowledge base
   - Usar una herramienta externa
   - Generar código para resolver la consulta
3. Ejecuta las acciones necesarias
4. Integra los resultados en una respuesta coherente
5. Aplica guardrails antes de devolver la respuesta al usuario

### 7.8. Consideraciones de Diseño

#### 7.8.1. Buenas Prácticas
- Proporcionar descripciones claras y detalladas para cada herramienta
- Diseñar herramientas con propósitos específicos y bien definidos
- Probar exhaustivamente las interacciones entre herramientas y knowledge bases
- Implementar guardrails para prevenir uso indebido

#### 7.8.2. Limitaciones
- La efectividad depende de la calidad del foundation model base
- Puede haber latencia adicional debido a las llamadas a herramientas externas
- La comprensión de las instrucciones por parte del modelo no es perfecta
- Posible uso excesivo de herramientas cuando no son necesarias

---

## Resumen para el Examen: Building LLM Agents with Bedrock

Los LLM Agents en Amazon Bedrock permiten extender las capacidades de los foundation models proporcionándoles herramientas para acceder a datos y servicios externos. Estas herramientas se implementan como Lambda functions organizadas en Action Groups, con instrucciones en lenguaje natural que guían al modelo sobre cuándo y cómo usarlas. Los agents pueden complementarse con knowledge bases (creando sistemas "Agentic RAG") y pueden tener la capacidad de Code Interpreter para escribir y ejecutar su propio código Python para resolver problemas complejos o generar visualizaciones. Para desplegar un agent en producción, se crea un alias que actúa como punto de acceso, y se configura el throughput como On-Demand o Provisioned según las necesidades de tráfico. Los agents se invocan mediante el método invoke_agent con el alias ID correspondiente, a través del endpoint de agents for Amazon Bedrock runtime.

### Consejo para el Examen
Para el examen AWS Certified Machine Learning - Associate, enfócate en entender la estructura de los Action Groups y cómo se definen los parámetros con descripciones en lenguaje natural que guían al foundation model. Recuerda la diferencia entre On-Demand Throughput y Provisioned Throughput para despliegues en producción, y cómo se utilizan los alias para gestionar versiones de agents. Presta especial atención a cómo los agents pueden integrar múltiples componentes de Bedrock (foundation models, knowledge bases, guardrails, Code Interpreter) para crear aplicaciones completas. Es importante también comprender el concepto de Agentic RAG, que combina el uso de herramientas con la consulta a knowledge bases para obtener información contextual adicional.




## 8. Other Amazon Bedrock Features (Model Evaluation, Bedrock Studio, Watermarks)

### 8.1. Importación de Modelos Personalizados

Amazon Bedrock no limita a los usuarios a utilizar solo los foundation models proporcionados o los custom models derivados de estos. También permite importar modelos propios entrenados desde cero.

#### 8.1.1. Fuentes de Importación
- **SageMaker**: Importación directa de modelos entrenados en SageMaker
- **Amazon S3**: Importación de modelos almacenados en buckets de S3
- Permite incorporar LLMs construidos completamente desde cero

#### 8.1.2. Ventajas
- Flexibilidad para usar modelos altamente especializados
- Integración con todo el ecosistema de Bedrock
- Capacidad de utilizar modelos propietarios junto con los foundation models existentes

### 8.2. Model Evaluation

La evaluación de modelos de IA generativa presenta desafíos únicos debido a la naturaleza no determinística de sus resultados. Bedrock ofrece varias estrategias para evaluar y comparar modelos.

#### 8.2.1. Métricas Automáticas
Bedrock puede calcular automáticamente diversas métricas de calidad:
- **Accuracy**: Precisión de las respuestas
- **Toxicity**: Nivel de contenido inapropiado
- **Robustness**: Consistencia ante variaciones en los inputs
- **BERT Score**: Similitud semántica con respuestas ideales
- **F1**: Medida equilibrada de precision y recall

#### 8.2.2. Conjuntos de Datos de Prueba
Para realizar estas evaluaciones, se pueden utilizar:
- **Conjuntos propios**: Prompts y respuestas ideales definidas por el usuario
- **Conjuntos predefinidos**: Datasets estándar incluidos en Bedrock
  - Diseñados para evaluar capacidades específicas del modelo
  - Incluyen pruebas para evaluar razonamiento, conocimiento y más

#### 8.2.3. Evaluación Humana
Además de las métricas automáticas, Bedrock facilita la evaluación por humanos:

- **Evaluación con equipo propio**: Framework para que tu propio equipo evalúe respuestas
- **Reinforcement Learning from Human Feedback (RLHF)**: Método donde humanos comparan respuestas alternativas
- **AWS Managed Teams**: Evaluadores humanos gestionados por AWS (similar a Mechanical Turk)
  - No es necesario reclutar un equipo propio de evaluadores
  - Proporciona feedback sistemático sobre la calidad de las respuestas

### 8.3. Provisioned Throughput

Para aplicaciones con cargas previsibles y constantes, Bedrock ofrece capacidad dedicada mediante throughput aprovisionado.

#### 8.3.1. Características
- Garantiza capacidad suficiente para cargas constantes
- Disponible para la capa de inferencia de Bedrock
- Complemento al On-Demand Throughput mencionado en secciones anteriores

#### 8.3.2. Casos de Uso
- Aplicaciones empresariales con patrones de uso predecibles
- Sistemas que requieren tiempos de respuesta consistentes
- Entornos donde se prefiere un costo fijo y predecible

### 8.4. Watermark Detection

Bedrock incorpora capacidades de marca de agua (watermarking) para ciertos modelos generativos.

#### 8.4.1. Funcionamiento
- El **Titan Image Generation Foundation Model** de Amazon incorpora marcas de agua invisibles en las imágenes generadas
- Estas marcas no afectan visualmente a la imagen pero pueden ser detectadas mediante herramientas específicas

#### 8.4.2. Módulo de Detección
- Bedrock incluye un módulo de detección de marcas de agua
- Permite verificar si una imagen fue generada por el modelo Titan
- Útil para autenticación y verificación de origen

#### 8.4.3. Aplicaciones
- Verificación de la fuente de contenido
- Detección de manipulaciones
- Cumplimiento de políticas sobre contenido generado por IA

### 8.5. Bedrock Studio

Bedrock Studio es una plataforma colaborativa basada en web para desarrollar aplicaciones con Bedrock sin necesidad de cuentas AWS individuales.

#### 8.5.1. Concepto y Funcionalidad
- Entorno de trabajo web para Bedrock
- Permite usar Bedrock sin tener una cuenta AWS individual
- Facilita la colaboración en equipos y organizaciones

#### 8.5.2. Integración de Seguridad
- Utiliza integración de Single Sign-On (SSO) con IAM
- Compatible con proveedores de identidad corporativos
- Conecta el sistema de autenticación de la organización con IAM de AWS

#### 8.5.3. Ventajas Colaborativas
- Múltiples usuarios pueden trabajar en el mismo proyecto
- Colaboración en componentes específicos dentro de proyectos
- Gestión centralizada de accesos y permisos
- Desarrollo colaborativo sin cuentas AWS individuales

#### 8.5.4. Casos de Uso
- Equipos de desarrollo de IA en entornos empresariales
- Organizaciones con políticas estrictas de acceso a AWS
- Proyectos que requieren colaboración entre múltiples departamentos o equipos

### 8.6. Consideraciones para Implementación

#### 8.6.1. Selección de Características
- Evaluar qué características de Bedrock son más relevantes para cada caso de uso
- Considerar la combinación de múltiples características para soluciones completas
- Balancear complejidad y funcionalidad según necesidades específicas

#### 8.6.2. Integración con Ecosistema AWS
- Las características adicionales de Bedrock se integran con otros servicios AWS
- Considerar flujos de trabajo end-to-end que combinen SageMaker, Bedrock y otros servicios
- Aprovechar la interoperabilidad entre características de Bedrock

---

## Resumen para el Examen: Other Amazon Bedrock Features

Amazon Bedrock ofrece varias características adicionales importantes: importación de modelos propios desde SageMaker o S3; evaluación de modelos mediante métricas automáticas (accuracy, toxicity, robustness, BERT Score, F1) y evaluación humana (con equipos propios o gestionados por AWS); provisioned throughput para garantizar capacidad dedicada; watermark detection para verificar imágenes generadas por el modelo Titan; y Bedrock Studio, un entorno colaborativo basado en web que permite usar Bedrock sin necesidad de cuentas AWS individuales mediante integración SSO con IAM y proveedores de identidad corporativos. Estas características complementan las funcionalidades core de Bedrock (foundation models, knowledge bases, agents, guardrails) para crear soluciones completas de IA generativa.

### Consejo para el Examen
Para el examen AWS Certified Machine Learning - Associate, presta especial atención a las opciones de evaluación de modelos, ya que representan un desafío único en IA generativa. Recuerda que existen tanto métricas automáticas como evaluación humana, y que Bedrock ofrece conjuntos de datos predefinidos para testing. También es importante comprender la diferencia entre Bedrock Studio y el acceso tradicional a Bedrock: Studio permite colaboración sin cuentas AWS individuales mediante SSO con IAM. Para el watermarking, recuerda que está específicamente implementado en el Titan Image Generation Model y que Bedrock proporciona un módulo de detección para verificar si las imágenes fueron generadas por este modelo. Finalmente, ten presente que puedes importar tus propios modelos desde SageMaker o S3, no estás limitado a los foundation models proporcionados por Bedrock.