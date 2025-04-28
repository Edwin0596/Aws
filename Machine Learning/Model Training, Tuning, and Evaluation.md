# Model Training, Tuning, and Evaluation

## 1. Activation Functions

### 1.1 Introducción a las Activation Functions

Las funciones de activación son un componente crítico en las redes neuronales. Están presentes en cada neurona o nodo y determinan la salida que se enviará a la siguiente capa basándose en las entradas recibidas. Esencialmente, una función de activación decide "qué salida debe producir la neurona dadas sus entradas".

### 1.2 Funciones de activación lineales y binarias

#### 1.2.1 Linear Activation Function
![Linear Activation Function](images/1-imagen.webb)

- Es la función de activación más simple
- Produce como salida exactamente lo que recibe como entrada (f(x) = x)
- Limitaciones:
  - No permite aprendizaje interesante ni backpropagation efectivo
  - Hace innecesarias las múltiples capas (una red con capas lineales es equivalente a una sola capa)
  - Rara vez se utiliza en la práctica

#### 1.2.2 Binary Step Function
![Binary Step Function](images/2-imagen.webb)

- Produce una salida binaria: 0 cuando la entrada es negativa, 1 cuando es positiva
- Limitaciones:
  - No permite clasificación múltiple (solo puede representar una categoría)
  - Presenta problemas matemáticos debido a su pendiente vertical (derivada infinita)
  - No es estable para el entrenamiento de redes neuronales

### 1.3 Funciones de activación no lineales

Las funciones no lineales son preferibles porque:
- Permiten crear mapeos complejos entre entradas y salidas
- Tienen derivadas utilizables para el aprendizaje mediante backpropagation
- Aprovechan el poder de las arquitecturas multicapa

#### 1.3.1 Sigmoid y TanH
![Sigmoid y TanH](images/3-imagen.webb)

- **Sigmoid (logistic)**:
  - Escala las salidas entre 0 y 1
  - Se usa para clasificación múltiple donde un elemento puede pertenecer a varias categorías

- **TanH (hyperbolic tangent)**:
  - Escala las salidas entre -1 y 1
  - Preferida sobre sigmoid por tener una media cercana a cero
  - Adecuada para redes neuronales recurrentes (RNN)

- Limitaciones de ambas:
  - Problema del gradiente que se desvanece (vanishing gradient) en valores extremos
  - Computacionalmente costosas (operaciones trigonométricas)

#### 1.3.2 ReLU (Rectified Linear Unit)
![ReLU](images/4-imagen.webb)

- Función de activación más popular actualmente
- Definición: f(x) = max(0, x)
- Ventajas:
  - Computacionalmente eficiente (simple de calcular)
  - Permite que los modelos converjan rápidamente
  - No tiene problemas de gradiente infinito
- Limitación:
  - "Dying ReLU problem": Cuando las entradas son negativas, la función produce cero y la neurona puede "morir"

#### 1.3.3 Leaky ReLU y variantes
![Leaky ReLU](images/5-imagen.webb)

- **Leaky ReLU**:
  - Solución al "dying ReLU problem"
  - Introduce una pequeña pendiente negativa para valores negativos
  - La pendiente es un hiperparámetro fijo

- **Parametric ReLU (PReLU)**:
  - Similar a Leaky ReLU pero la pendiente negativa se aprende durante el entrenamiento
  - Más flexible pero computacionalmente más intensivo

- **Otras variantes**:
![Otras variantes de ReLU](images/6-imagen.webb)

  - **ELU (Exponential Linear Unit)**:
    - Usa una función exponencial para valores negativos
    - Favorece cálculos de derivadas más suaves

  - **Swish**:
    - Desarrollada por Google
    - Beneficiosa para redes muy profundas (40+ capas)
    - Probablemente no aparezca en exámenes de AWS

  - **Maxout**:
    - Produce el máximo de todas las entradas
    - ReLU es un caso especial de Maxout
    - Duplica el número de parámetros, haciéndola poco práctica

### 1.4 Softmax

![Softmax](images/7-imagen.webb)

- Se utiliza principalmente en la capa de salida para problemas de clasificación múltiple
- Convierte las salidas en probabilidades para cada clase
- Características:
  - La suma de todas las probabilidades es 1
  - La clase con mayor probabilidad se selecciona como la predicción
  - No puede asignar múltiples etiquetas a un único elemento (para eso se usa sigmoid)

### 1.5 Selección de funciones de activación

Guía práctica para elegir la función de activación adecuada:

| Escenario | Función recomendada |
|-----------|---------------------|
| Capa de salida para clasificación (una sola clase) | Softmax |
| Redes neuronales recurrentes (RNN) | TanH |
| Casos generales | ReLU (primera opción) |
| Si ReLU presenta problemas | Leaky ReLU |
| Si Leaky ReLU no es suficiente | PReLU o Maxout |
| Redes muy profundas (40+ capas) | Swish |
| Asignación de múltiples etiquetas | Sigmoid |

Es importante entender estos conceptos ya que las funciones de activación son temas recurrentes en el examen AWS Certified Machine Learning - Associate.


## 2. Convolutional Neural Networks (CNN)

### 2.1 Introducción a las Convolutional Neural Networks

Las Convolutional Neural Networks (CNN) son un tipo especializado de redes neuronales diseñadas principalmente para procesar datos con estructura de cuadrícula, como imágenes. Su característica fundamental es la capacidad de encontrar patrones independientemente de dónde se encuentren en los datos, lo que se conoce como "feature location invariant".

### 2.2 Fundamentos y funcionamiento

#### 2.2.1 Inspiración biológica
- Las CNN están inspiradas en el funcionamiento del córtex visual humano
- El cerebro procesa imágenes mediante grupos de neuronas especializadas (campos receptivos locales)
- Cada grupo de neuronas responde a una parte específica del campo visual
- Los campos receptivos se superponen para cubrir todo el campo visual

#### 2.2.2 El proceso de convolución
La convolución es el proceso de dividir los datos en pequeños fragmentos superpuestos y procesarlos individualmente. Este enfoque permite:

- Detectar características independientemente de su ubicación
- Construir una jerarquía de características de complejidad creciente:
  - Capa baja: detecta líneas y bordes
  - Capa media: reconoce formas a partir de líneas
  - Capa alta: identifica objetos a partir de formas

#### 2.2.3 Ejemplo práctico
Cuando una CNN procesa una imagen de una señal de STOP:
1. Las primeras capas detectan bordes y contrastes
2. Las capas intermedias reconocen la forma octagonal y las letras
3. Las capas superiores combinan esta información con el color rojo
4. La red clasifica el objeto como una señal de STOP

### 2.3 Aplicaciones de las CNN

Las CNN no están limitadas al análisis de imágenes. Se utilizan en diversos campos:

- **Análisis de imágenes**: Reconocimiento de objetos, clasificación de imágenes, detección facial
- **Procesamiento de lenguaje natural**: Traducción automática, donde no se sabe exactamente dónde estará una frase o palabra clave
- **Análisis de sentimiento**: Identificación de frases que indican emociones específicas en textos
- **Datos volumétricos**: Análisis de datos médicos 3D como tomografías y resonancias

### 2.4 Implementación de CNN con Keras y TensorFlow

#### 2.4.1 Preparación de datos
- Es necesario preservar la estructura dimensional de los datos
- Para imágenes: formato ancho × alto × canales de color
  - Imágenes en blanco y negro: 1 canal
  - Imágenes a color: 3 canales (rojo, verde, azul)

#### 2.4.2 Capas especializadas para CNN
Keras proporciona capas específicas para implementar CNN:

- **Conv2D**: Realiza la convolución en datos 2D (imágenes)
  - También existen Conv1D (para texto) y Conv3D (para datos volumétricos)
- **MaxPooling2D**: Reduce el tamaño de los datos para disminuir la carga computacional
  - Toma el valor máximo en cada bloque de la imagen
  - También existen MaxPooling1D y MaxPooling3D
- **Flatten**: Convierte los datos multidimensionales en un vector unidimensional
  - Necesario para conectar con capas densas tradicionales

#### 2.4.3 Arquitectura típica de una CNN
```
Input → Conv2D → MaxPooling2D → Dropout → Flatten → Dense → Dropout → Softmax → Output
```

- **Conv2D**: Procesa la imagen mediante convolución
- **MaxPooling2D**: Reduce dimensiones para optimizar procesamiento
- **Dropout**: Previene el sobreajuste
- **Flatten**: Prepara los datos para las capas densas
- **Dense**: Capa tradicional de perceptrón multicapa
- **Softmax**: Capa de salida para clasificación

### 2.5 Desafíos y optimizaciones

#### 2.5.1 Desafíos computacionales
- Las CNN son muy intensivas en términos de CPU, GPU y memoria
- Requieren grandes volúmenes de datos de entrenamiento
- La obtención y procesamiento de datos es a menudo el mayor desafío
  - Ejemplo: Tesla recopila imágenes de cámaras de sus vehículos para entrenar sus sistemas

#### 2.5.2 Hiperparámetros
Las CNN tienen numerosos hiperparámetros ajustables:
- Tamaño del kernel (área de convolución)
- Número de capas
- Número de unidades (filtros)
- Cantidad de pooling
- Funciones de activación
- Optimizadores
- Funciones de pérdida

#### 2.5.3 Arquitecturas pre-diseñadas
Para facilitar la implementación, existen arquitecturas de CNN ya optimizadas:

| Arquitectura | Aplicación principal | Características |
|--------------|----------------------|-----------------|
| LeNet-5 | Reconocimiento de escritura | Primera CNN exitosa, relativamente simple |
| AlexNet | Clasificación de imágenes | Red más profunda que LeNet |
| GoogLeNet | Clasificación de imágenes | Introduce módulos "inception" que agrupan capas de convolución |
| ResNet | Clasificación de imágenes avanzada | Red muy profunda con "skip connections" para mantener rendimiento |

ResNet (especialmente ResNet-50) es particularmente relevante en el contexto de AWS, ya que se utiliza frecuentemente en algoritmos de clasificación de imágenes dentro de SageMaker.

### 2.6 Consideraciones para AWS

En el contexto de AWS, las CNN son utilizadas principalmente en SageMaker para tareas como:
- Clasificación de imágenes
- Detección de objetos
- Segmentación de imágenes
- Análisis de documentos

AWS proporciona infraestructura optimizada para entrenar y desplegar CNN, incluyendo instancias con GPU para acelerar el entrenamiento y la inferencia.


## 3. Recurrent Neural Networks (RNN)

### 3.1 Introducción a las Recurrent Neural Networks

Las Recurrent Neural Networks (RNN) son redes neuronales especializadas en procesar datos secuenciales. A diferencia de las redes neuronales tradicionales, las RNN mantienen un "estado" que les permite recordar información previa y aplicarla al procesamiento actual.

### 3.2 Aplicaciones de las RNN

#### 3.2.1 Procesamiento de secuencias temporales
- **Time series data**: Predicción de comportamientos futuros basados en patrones temporales
  - Web logs: Análisis de patrones de visitas a sitios web
  - Sensor logs: Procesamiento de datos del Internet of Things (IoT)
  - Predicción de mercados financieros: Análisis de comportamiento histórico de acciones
- **Sistemas autónomos**: Predicción de trayectorias en vehículos autónomos basándose en movimientos previos

#### 3.2.2 Procesamiento de secuencias no temporales
- **Procesamiento de lenguaje natural**: Traducción automática y análisis de textos
- **Generación de contenido**: Creación de subtítulos para imágenes o videos
- **Música generada por máquina**: Composición de música basada en patrones aprendidos

### 3.3 Funcionamiento de las RNN

#### 3.3.1 Estructura básica
- Una neurona recurrente recibe inputs normales y también la salida de su estado anterior
- Este "bucle" permite que la información persista a través del tiempo
- La función de activación suele ser una tangente hiperbólica (tanh) para preservar información temporalmente

#### 3.3.2 Representación desplegada en el tiempo
- Una RNN puede visualizarse como la misma neurona repetida en diferentes pasos temporales
- En cada paso temporal:
  1. La neurona recibe un nuevo input
  2. También recibe el output del paso temporal anterior
  3. Combina ambos inputs, aplica la función de activación y genera un output
  4. Este output se utiliza en el siguiente paso temporal

#### 3.3.3 Celda de memoria
- Las neuronas recurrentes funcionan como "celdas de memoria" que mantienen información del pasado
- Característica: Los eventos recientes tienen mayor influencia que los antiguos (pueden diluirse con el tiempo)
- Una capa recurrente consiste en múltiples neuronas recurrentes trabajando en paralelo

### 3.4 Topologías de RNN

Las RNN pueden configurarse en diferentes topologías según los requisitos:

| Topología | Input | Output | Ejemplo de aplicación |
|-----------|-------|--------|------------------------|
| Sequence-to-Sequence | Secuencia | Secuencia | Predicción de precios de acciones |
| Sequence-to-Vector | Secuencia | Vector | Análisis de sentimiento de un texto |
| Vector-to-Sequence | Vector | Secuencia | Generación de subtítulos para imágenes |
| Encoders-Decoders | Secuencia | Secuencia | Traducción automática entre idiomas |

### 3.5 Entrenamiento de RNN

#### 3.5.1 Backpropagation Through Time (BPTT)
- Las RNN se entrenan mediante backpropagation a través del tiempo
- Cada paso temporal actúa como una capa adicional durante el entrenamiento
- Desafío: Los pasos temporales pueden acumularse rápidamente, aumentando la complejidad

#### 3.5.2 Truncated BPTT
- Para limitar el costo computacional, se limita el backpropagation a un número determinado de pasos temporales
- Permite entrenar RNN en secuencias largas sin recursos excesivos

### 3.6 Variantes avanzadas de RNN

#### 3.6.1 LSTM (Long Short-Term Memory)
- Soluciona el problema de la "dilución" de información antigua
- Mantiene estados separados de memoria a corto y largo plazo
- Permite que información importante del pasado distante influya en el presente
- Ideal para secuencias donde la importancia no está relacionada con la posición temporal

#### 3.6.2 GRU (Gated Recurrent Unit)
- Simplificación de LSTM que ofrece rendimiento similar
- Requiere menos recursos computacionales
- Popular en la práctica por su equilibrio entre calidad y eficiencia

### 3.7 Desafíos en la implementación de RNN

- Extremadamente sensibles a la elección de topología e hiperparámetros
- Requieren importantes recursos computacionales debido a la simulación temporal
- Pueden no converger si los parámetros no están bien ajustados
- Es recomendable basarse en investigaciones previas para elegir topologías y parámetros

### 3.8 RNN en el contexto de AWS

- SageMaker incluye soporte para modelos basados en RNN
- Útil para:
  - Forecasting de series temporales
  - Análisis de comportamiento de usuarios
  - Procesamiento de lenguaje natural
  - Detección de anomalías en secuencias de datos
- AWS proporciona instancias optimizadas para el entrenamiento de RNN con alta demanda computacional
- Recomendable usar implementaciones preconfiguradas cuando sea posible debido a la complejidad del ajuste