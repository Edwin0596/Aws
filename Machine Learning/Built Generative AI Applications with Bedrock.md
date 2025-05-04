# Built Generative AI Applications with Bedrock

## 1. Amazon Bedrock - Overview

### 1.1 Introducción a Amazon Bedrock

- Es la base (bedrock) de las capacidades de IA generativa de Amazon
- Utiliza foundation models como base para construir aplicaciones
- Foundation models: Son Large Language Models o modelos de generación de imágenes subyacentes

### 1.2 Foundation Models disponibles

| Proveedor | Modelo | Descripción |
|-----------|--------|-------------|
| Amazon | Titan | Modelo propio de Amazon |
| Meta | LLaMA | Modelos de texto generales |
| Anthropic | Claude | LLMs para conversación |

**Nota importante**: OpenAI no está disponible en AWS debido a que trabaja con competidores de Amazon.

### 1.3 Características principales

#### 1.3.1 Enfoque API unificada
- Bedrock es una API para construir aplicaciones sobre foundation models
- Permite entrenar, fine-tunar y construir alrededor de estos modelos
- Proporciona una interfaz unificada para diferentes providers

#### 1.3.2 Modelo de negocio
- Funciona como marketplace de modelos
- Los proveedores terceros cobran a través de AWS
- Cada proveedor tiene su propio modelo de pricing
- Necesidad de verificar costos antes de usar modelos

#### 1.3.3 Capacidades adicionales
- Fine-tuning de modelos
- Soporte para Bring Your Own Model (BYOM)
- Retrieval Augmented Generation (RAG)
- LM Agents
- Integración con SageMaker Canvas

### 1.4 APIs de Bedrock

#### 1.4.1 Bedrock API
- Gestión de modelos subyacentes
- Deployment y training/fine-tuning
- Operaciones pre-producción

#### 1.4.2 Bedrock Runtime
- Inference en tiempo real
- Generación de embeddings
- Generación de imágenes desde texto
- Operaciones principales:
  - Converse
  - Invoke Model
  - Converse Stream
  - Invoke Model with Response Stream

#### 1.4.3 Bedrock Agent
- Framework para LM Agents
- Gestión de knowledge bases
- Permite incorporar datos propios
- Operaciones pre-producción para agents

#### 1.4.4 Bedrock Agent Runtime
- Inference con agents
- Métodos principales:
  - Invoke Agent
  - Retrieve
  - Retrieve and Generate

### 1.5 Consideraciones de seguridad y acceso

#### 1.5.1 Requisitos IAM
- **Obligatorio**: Usar cuenta IAM (NO cuenta root)
- Permisos necesarios:
  - Amazon Bedrock Full Access
  - Amazon Bedrock Read Only
  - Permisos específicos según uso

#### 1.5.2 Proceso de solicitud de acceso
- Requerido para todos los foundation models (excepto Titan)
- Pasos:
  1. Aceptar EULA (End User License Agreement)
  2. Aprobación automática en minutos
  3. Facturación a través de AWS
- Titan: Aprobación inmediata
- Otros modelos: Aprobación en pocos minutos

### 1.6 Pricing

- Los third parties establecen los precios
- Facturación centralizada a través de AWS
- Sin separación de facturas por proveedor
- Importante: Verificar pricing en aws.amazon.com/bedrock/pricing
- La aprobación de acceso no muestra precios explícitos

### 1.7 Playgrounds disponibles

Bedrock proporciona entornos de prueba interactivos:
- Chat interface
- Text generation interface  
- Image generation interface
- Evaluación de modelos custom/importados

## 2. Fine-Tuning Custom Models and Continuous Pre-Training with Bedrock

### 2.1 Conceptos fundamentales de Fine-Tuning

- Extiende el entrenamiento del foundation model base
- Adapta el modelo para casos de uso específicos
- Incorpora información propietaria directamente al modelo
- Ventajas sobre prompt engineering:
  - Elimina necesidad de grandes prompts con ejemplos
  - Ahorra tokens a largo plazo
  - Potencialmente más económico en uso continuo
- Desventaja principal: Costo inicial elevado y tiempo prolongado

### 2.2 Características de los modelos fine-tuneados

- Se utilizan igual que cualquier otro modelo en Bedrock
- Mantienen la misma API
- Pueden ser fine-tuneados nuevamente (iterativo)
- Llamados "custom models" en Bedrock

### 2.3 Aplicaciones de Fine-Tuning

| Caso de uso | Descripción | Ejemplo |
|-------------|-------------|---------|
| Personalidad específica | Estilo de comunicación particular | Chatbot que habla como pirata |
| Voz de marca | Comunicación coherente con identidad | Ads con estilo específico |
| Datos propietarios | Información específica de empresa | Clone de comunicación personal |
| Tareas especializadas | Clasificación o validación específica | Detección de veracidad |
| Actualización temporal | Datos más recientes que el modelo base | Información post-cutoff date |

### 2.4 Modelos compatibles

- Modelos fine-tuneables disponibles:
  - Amazon Titan
  - Cohere
  - Meta
- Verificar compatibilidad en la info card del modelo
- No todos los modelos soportan fine-tuning

### 2.5 Proceso de Fine-Tuning

#### 2.5.1 Formato de datos

```json
{
  "prompt": "Pregunta o contexto",
  "completion": "Respuesta esperada"
}
```

- Pares de prompt-completion
- Formato JSON simplificado
- Datos almacenados en S3
- Ejemplos usados para entrenar estilo de respuesta

#### 2.5.2 Fine-Tuning de modelos de imagen

- Soporta tanto text-to-image como image-to-embedding
- Formato: prompts + enlaces a imágenes en S3
- Permite personalizar generación de imágenes

### 2.6 Consideraciones de seguridad

- Para datos sensibles: Usar VPC con PrivateLink
- Importante para cumplimiento de privacidad
- Punto común en exámenes de certificación AWS

### 2.7 Consideraciones económicas

- **Costo inicial**: Elevado
- **Tiempo requerido**: Significativo
- **Beneficio a largo plazo**: Reducción de tokens
- **Comparación con otras técnicas**:
  - Prompt engineering: Menos costoso inicialmente
  - RAG: Alternativa para algunos casos

### 2.8 Continued Pre-Training

#### 2.8.1 Diferencias con Fine-Tuning

| Característica | Fine-Tuning | Continued Pre-Training |
|----------------|-------------|------------------------|
| Tipo de datos | Pares prompt-completion (etiquetados) | Texto plano (no etiquetado) |
| Uso ideal | Entrenar comportamientos específicos | Incorporar datos empresariales |
| Formato | JSON estructurado | Documentos raw |

#### 2.8.2 Aplicaciones típicas

- Documentos empresariales
- Datos sin estructura específica
- Información que se quiere "embebida" en el modelo
- Evitar inclusión repetitiva en prompts

### 2.9 Flujo de trabajo

1. Preparar datos en S3
2. Seleccionar modelo base compatible
3. Crear custom model en Bedrock
4. Entrenar (puede ser prolongado)
5. Usar nuevo modelo con API estándar

## 3. Retrieval-Augmented Generation (RAG) Fundamentals with Bedrock

### 3.1 ¿Qué es una Knowledge Base?

Imagina que estás presentando un examen y puedes consultar un libro de referencia. Eso es exactamente lo que hace RAG (Retrieval-Augmented Generation) para los modelos de lenguaje.

- **En Amazon Bedrock**: RAG se llama "Knowledge Base"
- **En la vida real**: Es como darle acceso a Internet a un chatbot para que pueda buscar información actual
- **Ventaja principal**: Alternative más rápida y económica comparada con el fine-tuning

### 3.2 ¿Cómo funciona RAG paso a paso?

#### 3.2.1 El proceso en términos simples:

Piensa en RAG como un asistente que:
1. Escucha tu pregunta
2. Busca información relevante en una biblioteca de documentos
3. Lee la información encontrada
4. Responde usando tanto su conocimiento previo como la información nueva encontrada

**Ejemplo práctico:**
- **Tu pregunta original**: "¿Qué dijo el presidente en su discurso de ayer?"
- **Lo que hace RAG**: 
  1. Busca en los archivos noticias sobre el discurso del presidente
  2. Encuentra un artículo con la transcripción
  3. **Pregunta modificada**: "¿Qué dijo el presidente en su discurso de ayer? Aquí hay información relevante: Anoche el presidente habló sobre la economía, las reformas educativas..."

### 3.3 Beneficios de usar RAG

| Beneficio | ¿Qué significa en la práctica? |
|-----------|-------------------------------|
| **Rapidez y economía** | Es como actualizar una página web vs reescribir todo un libro |
| **Actualización fácil** | Solo agregas nuevos documentos a la biblioteca, no reentrenando todo |
| **Búsqueda inteligente** | Encuentra respuestas incluso si usas palabras diferentes |
| **Menos alucinaciones** | Como tener fuentes reales vs inventar cosas |
| **Búsqueda AI** | Como Google pero con respuestas elaboradas, no solo links |

### 3.4 ¿Qué son las Vector Databases?

Las vector databases son como bibliotecas que organizan los documentos no por orden alfabético, sino por el significado de lo que contienen.

**En palabras simples:**
- **Técnica tradicional**: Buscar la palabra exacta "perro"
- **Búsqueda vectorial**: Encuentra "canino", "mascota", "cachorro" porque tienen significados relacionados
- **Embeddings**: Convierten texto en "códigos" que representan su significado

### 3.5 Limitaciones de RAG

| Problema | ¿Qué significa esto? |
|----------|---------------------|
| **Más tokens** | Como añadir páginas extra a cada pregunta que haces |
| **Complejidad** | Es como construir un motor de búsqueda dentro de otro robot |
| **Costos** | Vector databases consumen muchos recursos computacionales |
| **Sensibilidad** | Pequeños cambios en cómo preguntas pueden dar respuestas diferentes |
| **Impredecible** | Como un humano, no siempre da la misma respuesta exacta |
| **Depende de calidad** | Si la información guardada es mala, las respuestas serán malas |

### 3.6 Ejemplo: Concurso Jeopardy!

#### 3.6.1 ¿Cómo RAG respondería en Jeopardy!?

**Pregunta del concurso:**
"En 1986, México fue el primer país en hospedar esta competencia deportiva internacional dos veces"

**El proceso interno de RAG:**
1. **Entrada**: Recibe la pregunta de Jeopardy!
2. **Búsqueda**: Encuentra información sobre "México 1986 competencia deportiva"
3. **Recupera**: Artículos sobre la Copa Mundial de fútbol
4. **Genera**: "¿Qué es la Copa Mundial?"

### 3.7 Puntos importantes a recordar

- **RAG no es perfecto**: Aún puede inventar cosas, solo que menos frecuentemente
- **La calidad importa**: 
  - Si guardas información irrelevante, obtendrás respuestas irrelevantes
  - Cómo divides los documentos (chunking) afecta los resultados
  - Cómo formulas las preguntas influye en las respuestas
- **Ventaja técnica**: No necesitas reentrenar el modelo, solo actualizas la base de datos
- **Flexibilidad**: Puede combinar búsquedas tradicionales con búsquedas semánticas

### 3.8 Analogía final

Piensa en RAG como un estudiante en un examen:
- **Sin RAG**: El estudiante solo puede responder con lo que memorizó
- **Con RAG**: El estudiante puede consultar sus apuntes y libros durante el examen
- **El reto**: Saber qué información buscar y cómo usarla correctamente

## 4. Vector Stores and Embeddings with Amazon Bedrock Knowledge Bases

### 4.1 Introducción a los componentes de RAG

RAG funciona como un sistema de "búsqueda inteligente". Para entenderlo mejor, imaginemos que RAG es como un asistente de biblioteca que no solo encuentra libros por su título, sino que entiende de qué trata cada libro y puede encontrar información relacionada incluso si usas palabras diferentes.

#### 4.1.1 Terminología en Bedrock
- **Knowledge Base** es el nombre que Amazon da a RAG
- **Data Store** es donde se guardan todos los documentos que el sistema puede consultar
- La calidad de las respuestas depende completamente de qué tan bien pueda encontrar información relevante

### 4.2 Tipos de almacenamiento disponibles

| Tipo de base de datos | ¿Para qué sirve? | Ejemplo práctico |
|----------------------|------------------|------------------|
| Graph databases | Encuentra conexiones entre cosas | Recomendar productos similares en una tienda |
| Traditional search | Busca palabras exactas en textos | Buscar "python" en documentación |
| Vector databases | Busca por significado | Encontrar textos sobre "felicidad" aunque digan "alegría" |
| Approaches híbridos | Combina búsqueda exacta y semántica | Buscar "iPhone 14" y también "smartphone de Apple" |

### 4.3 Vector Databases explicadas

#### 4.3.1 ¿Qué son y por qué importan?
- Son bases de datos que "entienden" el significado del texto
- En lugar de buscar coincidencias exactas, buscan similitudes de significado
- Son populares porque aprovechan la inteligencia artificial para mejores resultados
- OpenSearch (que pertenece a Amazon) es la opción predeterminada en Bedrock

### 4.4 Embeddings: El cerebro del sistema

#### 4.4.1 ¿Qué son los embeddings?
Imagina que cada palabra o oración es transformada en una lista gigante de números (como coordenadas en un mapa multidimensional). Estos números capturan el "significado" de la palabra o texto.

**Ejemplo visual:**
- Piensa en un mapa donde "perro" y "gato" están cerca porque ambos son mascotas
- "Manzana" y "naranja" están cerca porque ambas son frutas
- "Cohete" y "astronauta" están cerca porque ambos relacionados con el espacio

#### 4.4.2 Cómo funcionan
- Los textos similares tienen embeddings similares
- Se representan como puntos cercanos en un espacio imaginario
- Ejemplo: "papa" y "ruibarbo" estarían cerca (ambos vegetales)
- "Enterprise", "Spaceballs", "The Orville" se agruparían (todos son naves espaciales de ciencia ficción)

#### 4.4.3 Generación de embeddings
- Modelos como Titan convierten texto en embeddings automáticamente
- Es un proceso económico y eficiente
- Los modelos de lenguaje ya tienen esta capacidad incorporada

### 4.5 Cómo funciona la búsqueda vectorial

#### 4.5.1 Proceso paso a paso

1. **Pregunta del usuario**: "¿Cuál es la capital de Francia?"
2. **Conversión a embedding**: El sistema convierte esta pregunta en una lista de números
3. **Búsqueda**: Busca en la base de datos los embeddings más parecidos
4. **Resultado**: Encuentra textos que hablan sobre París como capital
5. **Incorporación**: Añade esta información al prompt original

#### 4.5.2 Optimizaciones para velocidad
- No busca documento por documento (sería muy lento)
- Usa algoritmos inteligentes para buscar solo en áreas prometedoras
- Similar a como Google Maps no calcula todas las rutas posibles, sino las más probables

### 4.6 Opciones de bases de datos vectoriales

#### 4.6.1 Bases de datos tradicionales adaptadas
Muchas bases de datos conocidas han añadido capacidades vectoriales:
- **OpenSearch**: La opción favorita de Amazon en Bedrock
- **SQL databases**: Para datos estructurados
- **Redis**: Para búsquedas ultrarrápidas
- **MongoDB**: Para datos flexibles

#### 4.6.2 Bases de datos especializadas
- **Pinecone**: Líder del mercado, optimizada solo para vectores
- **Chroma**: Fácil de usar para prototipos
- **Weaviate**: Buena para aplicaciones empresariales

### 4.7 Ejemplo práctico: Sistema Star Trek

#### 4.7.1 El escenario
Tienes un sistema que conoce todos los diálogos de Star Trek y alguien pregunta:
"Data, háblame de tu hija Lal"

#### 4.7.2 Cómo funciona internamente
1. **Conversión**: "Data, háblame de tu hija Lal" → [0.1, 0.5, -0.3, ...] (embedding)
2. **Búsqueda vectorial**: Encuentra las líneas de Data que están "cerca" semánticamente
3. **Resultados encontrados**: "Lal significa 'amada' en hindi" o "Data creó a Lal en el episodio 'The Offspring'"
4. **Prompt final**: "Como Data de Star Trek, responde teniendo en cuenta: [aquí los diálogos relevantes]"

#### 4.7.3 Factores que afectan la calidad
- **Cantidad de resultados (K)**: ¿Incluir 3 o 10 líneas? Muy pocas pueden faltar contexto, muchas pueden confundir
- **Relevancia**: Si el sistema encuentra diálogos de Data hablando de otras cosas, la respuesta será pobre
- **Redacción del prompt**: Cómo se incorporan los resultados puede cambiar la respuesta final

### 4.8 Puntos clave para recordar

- RAG es como tener un asistente que busca información antes de responder
- Los vector stores "entienden" significados, no solo palabras exactas
- La calidad depende de encontrar la información correcta
- Hay múltiples opciones de base de datos, cada una con sus ventajas
- En Amazon Bedrock, todo esto se llama "Knowledge Base"

## 5. Implementing RAG with Amazon Bedrock Knowledge Bases

### 5.1 ¿Qué son las Knowledge Bases en Amazon Bedrock?

En Amazon Bedrock, RAG se implementa usando "Knowledge Bases". Piensa en una knowledge base como una biblioteca digital inteligente donde puedes guardar tus documentos y luego hacerle preguntas.

**Fuentes de datos que puedes usar:**
- **S3**: Subir tus propios documentos (PDFs, texto plano, JSON)
- **Web Crawler**: Extraer información de páginas web (con permisos)
- **Conectores terceros**: Confluence, Salesforce, SharePoint

### 5.2 ¿Cómo se procesan los documentos?

#### 5.2.1 El proceso de embedding

Imagina que tienes un libro en español y necesitas traducirlo a un "idioma" que la computadora entienda. Eso es lo que hace el embedding:

1. **Selección de modelo**: Usas un modelo foundation (Cohere o Amazon Titan)
2. **Conversión a vectores**: El modelo convierte tu texto en listas de números
3. **Dimensiones vectoriales**: Puedes controlar cuántos números usa para cada pedazo de texto

#### 5.2.2 Almacenamiento vectorial

Los vectores necesitan un lugar donde vivir:
- **Opción predeterminada**: OpenSearch (la opción por defecto en Amazon)
- **Alternativas**: Memorydb con capacidades vectoriales
- **Otras opciones**: Aurora, MongoDB Atlas, Pinecone, Redis Enterprise

### 5.3 Chunking: Dividir para conquistar

#### 5.3.1 ¿Qué es el chunking?

Es como cortar un libro en pequeños párrafos que la computadora puede entender mejor:
- **Tamaño típico**: 300 caracteres por fragmento
- **Control**: Puedes ajustar el tamaño y superposición de fragmentos
- **Limitación**: Dividir texto arbitrariamente puede romper ideas completas

#### 5.3.2 Mejores prácticas

- Cortar por oraciones o párrafos es mejor que por caracteres
- Los datos estructurados (JSON) funcionan mejor que texto plano
- Las graph databases pueden ser mejores para datos relacionales

### 5.4 Flujo de trabajo completo

**El recorrido de tus documentos:**
1. **Documentos origen** → S3/Web/Conectores
2. **Bedrock** → Crea embedding vectors
3. **Vector store** → Almacena los vectores
4. **Consulta usuario** → Búsqueda semántica
5. **RAG** → Combina resultados con prompt
6. **LLM** → Genera respuesta final

### 5.5 Formas de usar Knowledge Bases

#### 5.5.1 Chat with Your Document
- **Interfaz visual**: En la consola de Bedrock
- **Uso rápido**: Prueba rápida sin programar
- **Funcionalidad**: Sube documento y empieza a preguntar

#### 5.5.2 Integración programática
- **API directa**: Para aplicaciones personalizadas
- **Agentic RAG**: Como parte de sistemas de LM agents más grandes

### 5.6 Ejemplo práctico en Bedrock

**Demostración que verás:**
- Crear una knowledge base usando OpenSearch
- Subir un libro completo como documento
- Construir un sistema RAG rápidamente
- Hacer preguntas sobre el contenido del libro

### 5.7 Puntos clave para recordar

- **Bedrock = RAG automático**: No necesitas configurar todo manualmente
- **Flexibilidad de fuentes**: Puedes usar múltiples tipos de datos
- **Control granular**: Ajusta embedding y chunking según necesites
- **Integración fácil**: Desde consola hasta API avanzada
- **Escalabilidad**: Funciona desde prototipos hasta producción

## 6. Content Filtering with Amazon Bedrock Guardrails

### 6.1 ¿Qué son los Guardrails de Amazon Bedrock?

Los Guardrails son como un portero de discoteca para tu sistema de IA. Controlan lo que entra y lo que sale de tus modelos de Bedrock.

**Función principal:**
- Filtran contenido en prompts entrantes
- Filtran respuestas salientes
- Trabajan con knowledge bases y agents
- Compatible con modelos de texto (no imágenes aún)

### 6.2 Tipos de filtrado disponibles

#### 6.2.1 Filtrado por palabras y temas

| Tipo de filtro | ¿Qué hace? | Ejemplo |
|---------------|------------|---------|
| **Palabras específicas** | Bloquea términos exactos | Nombres de competidores |
| **Temas completos** | Evita categorías enteras | Política, religión |
| **Contenido objetable** | Filtra por nivel de ofensividad | Odio, sesgo, discriminación |

#### 6.2.2 Filtros automáticos

1. **Profanidad**: Ya viene con lista de palabrotas incluida
2. **PII (Información Personal)**: 
   - Detecta teléfonos, SSN, direcciones
   - Puede remover o enmascarar: `[dirección]`

### 6.3 Contextual Grounding Check (Verificación de contexto)

Esta característica ayuda a prevenir alucinaciones:

#### 6.3.1 Grounding (Fundamentación)
- Mide si la respuesta está basada en los documentos proporcionados
- Identifica cuando el modelo "inventa" información
- Usa un sistema de puntuación con umbrales ajustables

**Ejemplo práctico:**
- Doc proporcionado: "La empresa tiene 500 empleados"
- Respuesta buena: "La empresa cuenta con 500 empleados"
- Respuesta mala: "La empresa tiene más de 2000 empleados"

#### 6.3.2 Relevancia
- Evalúa si la respuesta corresponde a la pregunta
- Filtra respuestas que se desvían del tema

### 6.4 Cómo funcionan los Guardrails

#### 6.4.1 Implementación
- Se aplican automáticamente a todos los prompts
- Funcionan con knowledge bases y agents
- Personalizables según necesidades empresariales

#### 6.4.2 Manejo de contenido bloqueado
- Permite configurar mensajes personalizados
- Controla lo que ve el usuario cuando algo es filtrado

### 6.5 Consideraciones importantes

- **Limitaciones**: Los metrics no son perfectos
- **Dependencia de calidad**: La efectividad depende de la calidad del vector store
- **Configurabilidad**: Todo es ajustable según tus necesidades

### 6.6 Casos de uso prácticos

1. **Protección de marca**: Evitar menciones de competidores
2. **Cumplimiento regulatorio**: Prevenir divulgación de PII
3. **Calidad de respuestas**: Asegurar precisión y relevancia
4. **Ambiente laboral**: Filtrar contenido inapropiado

### 6.7 Analogía simple

Piensa en Guardrails como un sistema de tres capas:
1. **Entrada**: Como un filtro de spam para emails
2. **Procesamiento**: Como un editor que revisa que todo esté correcto
3. **Salida**: Como un moderador que aprueba respuestas

## 7. Building LLM Agents / Agentic AI with Amazon Bedrock Agents

### 7.1 ¿Qué son los LM Agents?

Los LM Agents (también llamados Agentic AI) son como darle "superpoderes" a tu modelo de IA, permitiéndole usar herramientas externas.

**Concepto fundamental:**
- Extienden las capacidades de los foundation models
- Permiten acceso a datos y servicios externos
- El modelo decide qué herramientas usar y cuándo

### 7.2 Arquitectura conceptual

Piensa en un agent como un asistente personal inteligente:

| Componente | Función | Analogía |
|------------|---------|----------|
| **Core (Foundation Model)** | Cerebro del sistema | Tu asistente personal |
| **Planning Module** | Decide qué herramientas usar | Organizador/planificador |
| **Tools** | Herramientas disponibles | Calculadora, teléfono, etc. |
| **Memory** | Historial de conversación | Libreta de notas |

**Lo asombroso:** El agente entiende cómo usar herramientas solo con descripciones en inglés simple.

### 7.3 Herramientas en Bedrock

#### 7.3.1 ¿Qué son las herramientas?

En Bedrock, las herramientas son **Lambda functions** que:
- Ejecutan código Python/JavaScript
- Acceden a APIs externas
- Realizan tareas específicas

#### 7.3.2 Action Groups (Grupos de Acción)

**Definición:** Conjuntos de herramientas relacionadas

**Componentes necesarios:**
1. **Instrucciones**: "Usa esta función para obtener el clima actual"
2. **Parámetros**: 
   - Nombre: `city`
   - Descripción: "Ciudad para consultar clima"
   - Tipo: string
   - Requerido: sí

**Ejemplo práctico:**
```json
{
  "tool": "weather_function",
  "instruction": "Usa esta función para el clima actual",
  "parameters": {
    "city": {
      "type": "string",
      "description": "Nombre de la ciudad",
      "required": true
    },
    "units": {
      "type": "string", 
      "description": "Fahrenheit o Celsius",
      "required": false
    }
  }
}
```

### 7.4 Capacidades avanzadas

#### 7.4.1 Manejo inteligente de parámetros
- Pregunta incompleta: "¿Cuál es el clima actual?"
- Agent: "¿En qué ciudad necesitas el clima?"
- Extrae información del contexto automáticamente

#### 7.4.2 Integración con Knowledge Bases
- Combina herramientas externas con búsqueda semántica
- Llamado "Agent RAG" cuando se combinan
- Ejemplo: "Usa esto para preguntas sobre autoempleo"

#### 7.4.3 Code Interpreter (Intérprete de código)
- El agent puede escribir su propio código Python
- Útil para:
  - Cálculos matemáticos complejos
  - Generación de gráficos
  - Visualizaciones de datos

### 7.5 Implementación en producción

#### 7.5.1 Aliases
**¿Qué son?** Puntos de entrada ("endpoints") para tu agent

**Tipos de throughput:**
- **On-demand**: Para tráfico normal
- **Provisioned**: Para alto volumen

#### 7.5.2 API de uso
```
POST /bedrock-agent/invoke
Headers: {
  "x-amz-bedrock-agent-alias": "alias-id"
}
```

### 7.6 Ejemplo completo

Un agent que combina:
1. **Foundation model**: Cerebro del sistema
2. **Lambda function**: Para clima (hardcoded: 75°F soleado)
3. **Knowledge base**: Información sobre autoempleo
4. **Guardrails**: Filtrado de contenido

### 7.7 Ventajas clave

- **Flexibilidad**: Agrega funciones sin reentrenar
- **Automatización**: Decide por sí mismo qué usar
- **Escalabilidad**: Desde prototipos hasta producción
- **Simplicidad**: Configuración con lenguaje natural

### 7.8 ¿Por qué esto es revolucionario?

**Antes:** APIs rígidas con reglas estrictas
**Ahora:** "Describe qué hace la herramienta y el agent la usará correctamente"

Es como tener un desarrollador que entiende instrucciones en inglés y puede conectarse a cualquier servicio que describas.

## 8. Other Amazon Bedrock Features (Model Evaluation, Bedrock Studio, Watermarks)

### 8.1 Importación de modelos personalizados

#### 8.1.1 Bring Your Own Model (BYOM)

**Opciones disponibles:**
- Modelos entrenados en SageMaker
- Modelos almacenados en S3
- Foundation models creados desde cero

**Ventaja:** No estás limitado a los modelos que ofrece Bedrock por defecto

### 8.2 Evaluación de modelos

#### 8.2.1 El desafío de evaluar IA generativa

La naturaleza no determinista hace difícil medir calidad. Es como evaluar arte: ¿quién decide qué respuesta es mejor?

#### 8.2.2 Métricas automáticas disponibles

| Métrica | ¿Qué mide? |
|---------|------------|
| **Accuracy** | Precisión de respuestas |
| **Toxicity** | Contenido ofensivo |
| **Robustness** | Estabilidad ante variaciones |
| **BERT Score** | Similitud semántica |
| **F1 Score** | Balance precisión/recuperación |

#### 8.2.3 Evaluación con datasets

**Dos opciones:**
1. **Custom datasets**: Tu propio conjunto de preguntas/respuestas ideales
2. **Built-in datasets**: Pruebas estándar prediseñadas

### 8.3 Evaluación humana

#### 8.3.1 Framework para evaluación humana

**Conceptos:**
- Comparación lado a lado de respuestas
- Votación humana sobre calidad
- Similar a RLHF (Reinforcement Learning from Human Feedback)

#### 8.3.2 Equipos de evaluación

**Opciones disponibles:**
- **Tu propio equipo**: Traer tus evaluadores
- **AWS Managed Teams**: Servicio similar a Mechanical Turk

### 8.4 Throughput provisionado

#### 8.4.1 Capacidad garantizada

- Asegura capacidad constante para cargas estables
- Se aplica a la capa de inferencia
- Útil para aplicaciones en producción

### 8.5 Detección de marcas de agua

#### 8.5.1 Titan Image Generator

**Características:**
- Embebe marcas de agua automáticamente
- Permite identificar imágenes generadas por IA
- Módulo de detección incluido en Bedrock

**Aplicación práctica:** Verificar origen de imágenes generadas

### 8.6 Bedrock Studio

#### 8.6.1 Entorno colaborativo

**¿Qué es?**
- Aplicación web para equipos
- Acceso sin cuentas AWS individuales
- Workspace colaborativo en la nube

#### 8.6.2 Ventajas organizacionales

**Beneficios:**
- Colaboración en proyectos
- Integración con Single Sign-On
- Manejo centralizado de permisos
- No requiere distribuir cuentas AWS

#### 8.6.3 Seguridad y acceso

- Integración con IAM
- Usa identity providers existentes
- Simula cuentas AWS bajo el capó

### 8.7 Resumen de características adicionales

| Característica | Beneficio Principal | Caso de Uso |
|----------------|---------------------|-------------|
| **Model Import** | Flexibilidad total | Custom foundation models |
| **Evaluation** | Medición objetiva | Comparar modelos |
| **Human Evaluation** | Validación cualitativa | Ajuste fino de respuestas |
| **Provisioned Throughput** | Rendimiento garantizado | Apps en producción |
| **Watermarks** | Trazabilidad | Detectar imágenes AI |
| **Bedrock Studio** | Colaboración simplificada | Equipos empresariales |

