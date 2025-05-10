# Generative IA Models Fundamentals

## 1. The Transformer Architecture

### 1.1 Evolución de los modelos de lenguaje

Los Transformers representan una evolución significativa en el procesamiento de lenguaje natural:

- La "T" en ChatGPT hace referencia a la arquitectura Transformer
- Aunque ChatGPT y GPT-4 son desarrollados por OpenAI (competidor de Amazon), AWS soporta modelos similares
- El examen puede preguntar sobre Transformers en general, no solo implementaciones específicas de AWS

### 1.2 Orígenes: de RNN a Transformers

#### 1.2.1 Recurrent Neural Networks (RNN)

- Las RNN fueron precursoras de los Transformers
- Características principales:
  - Procesan tokens secuencialmente
  - Utilizan un bucle de retroalimentación para mantener información
  - Efectivas para secuencias (texto, series temporales)
  - Mantienen un estado oculto (hidden state) que codifica el significado acumulado

#### 1.2.2 Arquitectura Encoder-Decoder

- Utilizada inicialmente para traducción automática
- Componentes:
  - **Encoder**: Convierte texto de entrada en una representación (hidden state)
  - **Decoder**: Convierte la representación en tokens de salida (otro idioma)
- **Limitación**: Information bottleneck (cuello de botella de información)
  - Un solo vector para representar todo el significado
  - Información del inicio de secuencias largas puede perderse

### 1.3 Self-Attention: el avance clave

- Introducido en 2017 con el paper "Attention is All You Need"
- Ventajas:
  - Cada token tiene su propio estado oculto (no acumulativo)
  - Durante la decodificación, puede acceder a estados de todos los tokens
  - Captura relaciones entre palabras y contexto
  - Maneja mejor las diferencias de orden de palabras entre idiomas

#### 1.3.1 Mecanismo de Attention

- Determina qué otros tokens son relevantes para procesar el token actual
- Utiliza "attention weights" (pesos de atención) para relacionar tokens
- Ayuda a interpretar palabras según su contexto
- **Limitación**: RNNs seguían siendo secuenciales, limitando la paralelización

### 1.4 Transformers: arquitectura completa

- Reemplazan completamente las RNNs por Feedforward Neural Networks
- Características principales:
  - Procesamiento paralelo (no secuencial)
  - Incorpora Self-Attention desde el principio
  - Utiliza Positional Encoding para mantener información de posición
  - Altamente paralelizable, permitiendo entrenar con volúmenes masivos de datos

#### 1.4.1 Ventajas para Large Language Models

- La paralelización permite entrenar con:
  - Todo el contenido de internet
  - Wikipedia completa
  - Gran corpus de libros digitalizados
- Esta capacidad de escala es lo que hace posibles los modelos de lenguaje actuales

## 2. Self-Attention and Attention-Based Neural Networks

### 2.1 Funcionamiento de Self-Attention

- Trabaja con embeddings (vectores multidimensionales) para cada token
- El propósito es producir un promedio ponderado de todos los embeddings de tokens
- Resultado: tokens vinculados a otros tokens relevantes para su contexto

#### 2.1.1 Comprensión contextual

- Las palabras pueden tener significados diferentes según el contexto
- Ejemplo: "novel" (novela/libro vs. novedoso/original)
  - "I read a good novel" → "novel" relacionado con "read" (libro)
  - "Attention is a novel idea" → "novel" relacionado con "idea" (novedoso)
- Self-Attention aprende estas relaciones contextuales

### 2.2 Mecanismo interno de Self-Attention

#### 2.2.1 Matrices Query, Key y Value

- Tres matrices de pesos aprendidas durante el entrenamiento:
  - **Query (Q)**: Qué está buscando el token actual
  - **Key (K)**: Con qué puede coincidir
  - **Value (V)**: Qué información proporciona
- Cada token obtiene vectores Q, K y V multiplicando su embedding por estas matrices

#### 2.2.2 Cálculo de puntajes de atención

- Para cada token, se multiplica su Query con las Keys de todos los demás tokens
- Se usa producto escalar (dot product) → "Scaled Dot-Product Attention"
- Se aplica Softmax para normalizar los puntajes
- Los puntajes determinan cuánta atención prestar a cada token

### 2.3 Variantes y optimizaciones

#### 2.3.1 Masked Self-Attention

- Evita que los tokens "vean" tokens futuros
- Funciona aplicando una máscara que anula (multiplica por cero) la influencia de tokens posteriores
- Utilizado en modelos generativos como GPT
- Simula el procesamiento secuencial mientras mantiene la paralelización

#### 2.3.2 Diferencias entre arquitecturas

- **GPT**: Utiliza únicamente decoders apilados con masked self-attention
- **BERT**: Utiliza únicamente encoders apilados con masked language modeling
- Cada arquitectura implementa el enmascaramiento de manera diferente

#### 2.3.3 Multi-Headed Self-Attention

- Optimización para mayor paralelización
- Divide los vectores Q, K y V en múltiples "cabezas" (matrices)
- Cada cabeza puede aprender diferentes tipos de relaciones
- Permite procesar diferentes aspectos de atención en paralelo

## 3. Applications of Transformers

### 3.1 Fundamentos y limitaciones

Los Transformers son modelos fundamentales (foundation models) que sirven como base para diversas aplicaciones de procesamiento de lenguaje natural. Es importante entender que:
- Por sí solos no representan aplicaciones completas
- Requieren capas adicionales para casos de uso específicos
- Son el componente base sobre el cual se construyen sistemas más complejos

### 3.2 Aplicaciones de chat y conversación

- Los Transformers son la base de sistemas como ChatGPT
- **Limitaciones**:
  - Por sí solos no son buenos chatbots
  - Requieren entrenamiento adicional para:
    - Mantener conversaciones coherentes
    - Retener contexto de mensajes anteriores
    - Moderar contenido inapropiado u ofensivo

### 3.3 Question Answering

- Pueden entrenarse para responder preguntas específicas
- Funcionamiento similar a la traducción:
  - Recibe una pregunta + token de generación de respuesta
  - Procesa y genera una respuesta apropiada
- **Consideraciones importantes**:
  - Pueden generar respuestas que "suenan correctas" pero no lo son
  - No tienen sentido inherente de verdad o falsedad
  - No poseen experiencia personal real

### 3.4 Clasificación de texto

- Añadiendo capas sobre el Transformer para tareas específicas:
  - **Sentiment Analysis**: Clasifica texto como positivo o negativo
  - **Named Entity Recognition**: Identifica y mapea entidades mencionadas
  - Permite simplificar salidas complejas a categorías discretas

### 3.5 Procesamiento de texto

- **Summarization**: Condensa textos largos manteniendo información clave
- **Machine Translation**: Convierte texto entre diferentes idiomas
- **Code Generation**: Traduce requerimientos en código funcional
  - Requiere verificación, ya que no siempre es 100% correcto
  - Puede ahorrar tiempo significativo en desarrollo

### 3.6 Generación de texto para servicios automatizados

- **Automated Customer Service**: Respuestas basadas en transcripciones históricas
- **Precauciones**:
  - Los modelos pueden generar respuestas falsamente positivas
  - Problemas al manejar solicitudes no vistas durante entrenamiento
  - Necesario supervisar y complementar con sistemas de seguimiento

### 3.7 Consideraciones de implementación

- Los Transformers no tienen inherentemente criterio ético o factual
- La verificación humana sigue siendo necesaria en aplicaciones críticas
- Se recomienda desplegar estas herramientas con cautela
- Sistemas de moderación y supervisión son componentes esenciales


## 4. Generative Pre-Trained Transformers: How they Work

### 4.1 Introducción a GPT

- GPT significa "Generative Pre-Trained Transformer"
- Es una implementación específica de la arquitectura Transformer
- Relación con AWS:
  - No está directamente vinculado a AWS (es de OpenAI/Microsoft)
  - El examen AWS puede preguntar sobre conceptos generales de GPT
  - Versiones antiguas como GPT-2 son de código abierto y pueden usarse en AWS (vía Hugging Face)

### 4.2 Arquitectura específica de GPT

#### 4.2.1 Estructura de decoders
- GPT utiliza exclusivamente bloques decoders (a diferencia de otros modelos)
  - BERT: solo utiliza encoders
  - T5: utiliza encoders y decoders
- Cada bloque decoder contiene:
  - Una capa de Self-Attention
  - Una Feedforward Neural Network
- Los bloques se apilan verticalmente, creando una red masiva
  - GPT-2: Cientos de millones de parámetros
  - GPT-4: Estimado en más de un trillón de parámetros

#### 4.2.2 Procesamiento paralelo
- Todo se procesa en paralelo (no secuencialmente)
- No hay separación estricta entre "entrada" y "salida"
- Funciona generando el siguiente token repetidamente:
  - Recibe una secuencia de tokens
  - Predice el siguiente token más probable
  - Añade ese token a la secuencia y repite

### 4.3 Procesamiento de entrada

#### 4.3.1 Tokenización
- Convierte texto en secuencias de números (tokens)
- Características:
  - Algunas palabras se mapean a un solo token
  - Otras palabras se dividen en múltiples tokens (ej. "indivisible" → "in", "div", "isible")
  - Secuencias comunes pueden formar un único token (ej. "123")
  - Puntuación y caracteres especiales tienen sus propios tokens

#### 4.3.2 Token Embedding
- Convierte tokens en vectores multidimensionales
- La distancia entre vectores representa similitud semántica:
  - Palabras con significados similares están más cercanas
  - Palabras no relacionadas están más alejadas
- Dimensionalidad: Miles de dimensiones por token

#### 4.3.3 Positional Encoding
- Aporta información sobre la posición relativa de cada token
- Utiliza funciones sinusoidales (seno y coseno)
- Ventajas:
  - Funciona para secuencias de cualquier longitud
  - Permite identificar relaciones posicionales cercanas
  - Se implementa alternando valores de seno y coseno

### 4.4 Procesamiento y generación de tokens

#### 4.4.1 Mecanismo de generación
- GPT procesa toda la secuencia de tokens en cada paso
- El flujo de trabajo es:
  1. Procesa la secuencia de entrada
  2. Genera un vector de salida para el próximo token
  3. Multiplica este vector por la matriz de embeddings
  4. Obtiene probabilidades para cada posible siguiente token
  5. Selecciona un token basado en estas probabilidades
  6. Añade el token a la secuencia y repite

#### 4.4.2 Control de temperatura
- Determina la aleatoriedad en la selección del siguiente token:
  - Temperatura 0: Siempre selecciona el token más probable
  - Temperatura alta: Introduce aleatoriedad, permitiendo más variedad
- Útil para:
  - Obtener respuestas consistentes (temperatura baja)
  - Generar ideas creativas o diversas (temperatura alta)

### 4.5 Entrenamiento de GPT

- Entrenamiento no supervisado en corpus masivos de texto:
  - Internet (incluyendo Wikipedia, Reddit)
  - Libros digitalizados
  - Artículos académicos
- No requiere etiquetas específicas:
  - El texto mismo proporciona la "respuesta correcta"
  - Se entrena para predecir el siguiente token
  - Aprende patrones y relaciones del lenguaje natural
- Optimiza para comprender el lenguaje en sí, no para tareas específicas

## 5. LLM Key Terms and Controls

### 5.1 Representación de datos en LLMs

#### 5.1.1 Tokens

- Representaciones numéricas de palabras o partes de palabras
- Permiten que los modelos trabajen con números en lugar de texto
- Son la unidad básica de procesamiento en un LLM
- Pueden representar:
  - Palabras completas
  - Partes de palabras
  - Signos de puntuación
  - Caracteres especiales

#### 5.1.2 Embeddings

- Vectores multidimensionales que codifican el significado de los tokens
- Características principales:
  - Palabras con significados similares tienen embeddings cercanos en el espacio vectorial
  - Permiten capturar relaciones semánticas entre palabras
  - Pueden unificar tokens de diferentes idiomas que significan lo mismo
  - Son la base del entendimiento semántico del modelo

### 5.2 Controles para generación de texto

#### 5.2.1 Top-P (Nucleus Sampling)

- Define un umbral de probabilidad para incluir tokens candidatos
- Funcionamiento:
  - Establece un umbral de probabilidad acumulada
  - Considera solo tokens cuya probabilidad acumulada está por debajo del umbral
  - Valores más altos generan resultados más variados/aleatorios
  - También conocido como "muestreo de núcleo"

#### 5.2.2 Top-K

- Selecciona un número fijo "K" de tokens candidatos
- Funcionamiento:
  - Elige los K tokens más probables como candidatos
  - Mayor valor de K = más opciones = más aleatoriedad
  - Menor valor de K = menos opciones = más consistencia
  - Es independiente de las probabilidades absolutas

#### 5.2.3 Temperature

- Controla la aleatoriedad en la selección del siguiente token
- Escala:
  - Temperatura alta (cerca de 1.0): Resultados más aleatorios/creativos
  - Temperatura baja (cerca de 0.0): Resultados más consistentes/deterministas
- Se aplica después de filtrar candidatos con Top-P o Top-K
- Impacta directamente la "creatividad" del modelo

### 5.3 Limitaciones técnicas

#### 5.3.1 Context Window

- Número de tokens que un LLM puede procesar simultáneamente
- Define:
  - Cuántas palabras circundantes puede considerar para contexto
  - El alcance del mecanismo de Self-Attention
  - La "memoria" efectiva del modelo para referencias anteriores

#### 5.3.2 Maximum Number of Tokens

- Límite superior para la cantidad total de tokens
- Se aplica a:
  - Tokens de entrada (prompt)
  - Tokens de salida (respuesta generada)
- Varía según el modelo y la implementación específica

## 6. Fine-Tuning and Transfer Learning with Transformers

### 6.1 Introducción al Fine-Tuning

- El fine-tuning es la aplicación de transfer learning a modelos Transformer
- Proceso:
  - Se parte de un modelo pre-entrenado (GPT, LLaMA, etc.)
  - Se adapta a tareas específicas con datos de entrenamiento adicionales
  - Se preserva el conocimiento lingüístico general del modelo base
- Ventaja principal: 
  - Aprovecha los modelos existentes como punto de partida
  - Requiere significativamente menos datos y recursos que entrenar desde cero

### 6.2 Métodos de Fine-Tuning

#### 6.2.1 Fine-Tuning completo
- Re-entrenamiento de todo el modelo con nuevos datos
- Todos los parámetros se actualizan durante el entrenamiento
- Requiere más recursos computacionales
- Adecuado cuando el dominio objetivo difiere significativamente del original

#### 6.2.2 Layer Freezing (Congelación de capas)
- Se "congelan" ciertas capas del modelo (generalmente inferiores)
- Solo se entrenan las capas superiores
- Aplicaciones:
  - Entrenar nuevos tokenizadores para idiomas específicos
  - Adaptación a dominios especializados
  - Preservar características fundamentales del modelo original

#### 6.2.3 Añadir nuevas capas
- Se mantiene intacto el modelo pre-entrenado
- Se añaden capas adicionales encima para tareas específicas
- Ejemplos:
  - Capa de clasificación para sentiment analysis
  - Capa de traducción para machine translation
  - Capa de extracción para question answering

### 6.3 Aplicaciones prácticas

#### 6.3.1 Personalización de comportamiento
- Ajuste de tono o personalidad mediante ejemplos
- Casos de uso:
  - Asistentes virtuales con estilos específicos de comunicación
  - Adaptación a tonos corporativos particulares
  - Simulación de personalidades o estilos de escritura

#### 6.3.2 Adaptación a nuevas tareas
- Transformación de modelos de lenguaje en sistemas especializados
- Ejemplos:
  - Clasificación de texto (sentiment analysis)
  - Generación de respuestas específicas de dominio
  - Resumen automático de textos
  - Creación de chatbots especializados

#### 6.3.3 Personalización con datos propios
- Entrenamiento con:
  - Emails y respuestas históricas
  - Transcripciones de servicio al cliente
  - Documentación interna de la empresa
  - Scripts, libros o corpus específicos
- Permite que el modelo emule patrones de respuesta específicos
- Crea sistemas que responden como lo haría una persona o entidad particular

### 6.4 Consideraciones para implementación

- El fine-tuning puede lograrse con relativamente pocos ejemplos (few-shot learning)
- La calidad de los datos de entrenamiento es crítica para el resultado
- Es importante balancear:
  - Preservación del conocimiento general del modelo base
  - Adaptación específica a la nueva tarea
  - Evitar el overfitting (sobreajuste) al conjunto de entrenamiento
- Evaluación continua para asegurar que el modelo mantiene el comportamiento deseado

## 7. AWS Foundation Models and SageMaker JumpStart with Generative AI

### 7.1 ¿Qué son los Foundation Models?

- Modelos Transformer pre-entrenados y masivos
- Diseñados para ser punto de partida para fine-tuning
- Características principales:
  - Entienden los fundamentos del funcionamiento del lenguaje
  - Pueden adaptarse a tareas específicas mediante fine-tuning
  - Representan la base sobre la cual construir aplicaciones

### 7.2 Disponibilidad de modelos en AWS

#### 7.2.1 Modelos NO disponibles en AWS

| Modelo | Proveedor | Motivo de no disponibilidad |
|--------|-----------|----------------------------|
| GPT (serie actual) | OpenAI/Microsoft | Competidor directo (Azure) |
| BERT | Google | Competidor directo (Google Cloud) |
| DALL-E | OpenAI/Microsoft | Competidor directo (Azure) |

**Excepciones**: 
- GPT-2 y derivados (GPT-J): Disponibles por ser open source
- Versiones antiguas de modelos pueden estar accesibles

#### 7.2.2 Modelos disponibles en AWS

| Modelo | Proveedor | Características | Uso principal |
|--------|-----------|-----------------|---------------|
| Jurassic-2 | AI21 Labs | Multilingual | Generación de texto en múltiples idiomas |
| Claude | Anthropic | Conversaciones | Chatbots, Q&A, automatización de flujos |
| Stable Diffusion | Stability AI | Generación de imágenes | Creación de arte, logos, diseños |
| Amazon Titan | Amazon | Propósito general | Generación, resumen, embeddings |
| LLaMA | Meta | Generación de texto | Múltiples aplicaciones NLP |
| Segment Anything | Meta | Segmentación de imágenes | Análisis y procesamiento de imágenes |

### 7.3 Características específicas de los modelos

#### 7.3.1 Jurassic-2 (AI21 Labs)
- Especialización: Multilingual
- Idiomas soportados: Español, francés, alemán, portugués, italiano, holandés
- Principal fortaleza: Diversidad lingüística

#### 7.3.2 Claude (Anthropic)
- Especialización: Conversaciones
- Capacidades:
  - Question answering
  - Workflow automation
  - Diálogos avanzados

#### 7.3.3 Stable Diffusion (Stability AI)
- Especialización: Generación de imágenes
- Aplicaciones:
  - Creación de arte digital
  - Diseño de logos
  - Generación de diseños visuales

#### 7.3.4 Amazon Titan
- Producto propio de Amazon (lanzamiento inicial)
- Capacidades:
  - Generación de texto generalista
  - Resumen de textos
  - Question answering
  - **Embeddings API**: 
    - Calcula similitudes entre tokens/frases
    - Aplicaciones en personalización y búsqueda
    - Recomendaciones basadas en similitud semántica

### 7.4 SageMaker JumpStart

#### 7.4.1 Descripción y funcionalidad
- Plataforma para lanzar notebooks pre-configurados
- Integración con modelos pre-entrenados
- Facilita la experimentación rápida con Foundation Models

#### 7.4.2 Características principales
- Notebooks pre-poblados con ejemplos
- Guías para:
  - Cargar el modelo
  - Alimentar datos
  - Consultar y obtener resultados
  - Fine-tunar para tareas específicas
  - Gestionar recursos (apagado, etc.)

#### 7.4.3 Integración con Hugging Face
- Acceso al ecosistema completo de Hugging Face
- Modelos disponibles incluyen:
  - Falcon
  - BLOOM-Z
  - GPT-J
  - GPT-2
  - Alexa Large Language Model
- Permite experimentar con cualquier modelo de la plataforma

### 7.5 Estrategia de AWS en Foundation Models

- Rol como agregador de modelos
- Similitud con Hugging Face (centralización de recursos)
- Propuesta de valor:
  - Acceso concentrado a múltiples proveedores
  - Integración nativa con ecosistema AWS
  - Facilidad de implementación y experimentación
- Desarrollo propio (Amazon Titan) combinado con partnerships
- Enfoque en facilitar el acceso y uso rather than desarrollo desde cero

