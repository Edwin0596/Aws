# Machine Learning Operations (MLOps) with AWS

## 1. Deployment Guardrails and Shadow Tests

### 1.1. Deployment Guardrails en SageMaker

SageMaker ofrece características avanzadas para garantizar despliegues seguros de modelos, asegurando que la implementación de nuevos modelos no cause problemas, o si los causa, se detecten rápidamente.

#### 1.1.1. Características Principales
Los deployment guardrails pueden implementarse en:
- Endpoints de inferencia en tiempo real
- Endpoints de inferencia asíncrona (endpoints que proporcionan respuestas asíncronas sin esperar a que se complete el procesamiento)

#### 1.1.2. Función Principal
El objetivo de los deployment guardrails es controlar la transferencia de tráfico hacia un nuevo modelo de manera segura, en lugar de simplemente implementarlo y esperar lo mejor.

### 1.2. Despliegues Blue-Green

Deployment guardrails implementa el concepto de despliegues blue-green:
- **Fleet Azul (Blue)**: Ejecuta el modelo anterior (ya probado)
- **Fleet Verde (Green)**: Ejecuta el nuevo modelo (en fase de implementación)

#### 1.2.1. Modos de Despliegue
SageMaker ofrece tres modos diferentes para gestionar la transición:

1. **All at Once (Todo de una vez)**:
   - Transfiere todo el tráfico inmediatamente al Fleet Verde
   - Monitoriza automáticamente el rendimiento durante un período
   - Solo termina el Fleet Azul si el rendimiento es aceptable

2. **Canary Mode (Modo Canario)**:
   - Transfiere solo una pequeña porción del tráfico al Fleet Verde
   - Monitoriza el rendimiento de esta muestra "canario"
   - Si el rendimiento es aceptable, transfiere el resto del tráfico
   - Finalmente termina el Fleet Azul

3. **Linear Mode (Modo Lineal)**:
   - Transfiere el tráfico en pasos linealmente espaciados
   - Permite configurar tantos pasos como se necesiten
   - Ideal para monitorizar escalabilidad y rendimiento de forma controlada
   - Aumenta gradualmente el tráfico hacia el nuevo modelo

#### 1.2.2. Rollbacks Automáticos
Una característica importante de los deployment guardrails es el soporte para rollbacks automáticos:
- Si se detectan problemas durante la implementación
- El sistema puede revertir automáticamente al Fleet Azul (modelo anterior)
- Asegura que el servicio siempre utilice un modelo funcional

### 1.3. Shadow Tests

Otra característica de SageMaker para despliegues seguros son los shadow tests:

#### 1.3.1. Concepto Principal
Los shadow tests permiten comparar el rendimiento de una "variante sombra" con la producción actual:
- Concepto similar a los deployment guardrails
- La variante sombra recibe un porcentaje del tráfico real
- No afecta las respuestas enviadas a los usuarios
- Solo se utiliza para evaluación comparativa

#### 1.3.2. Proceso de Monitorización
A diferencia de los deployment guardrails:
- La monitorización es manual, no automática
- El ingeniero supervisa el rendimiento de la variante sombra en la consola de SageMaker
- La decisión de promover la variante a producción es manual basada en los resultados observados

---

## Resumen para el Examen: Deployment Guardrails and Shadow Tests

SageMaker ofrece dos funcionalidades clave para implementaciones seguras de modelos: deployment guardrails y shadow tests. Los deployment guardrails permiten controlar la transferencia de tráfico a nuevos modelos mediante despliegues blue-green con tres modos: All at Once (transferencia completa con monitorización), Canary (transferencia parcial inicial) y Linear (transferencia gradual en pasos). Todos estos modos soportan rollbacks automáticos en caso de problemas. Por otro lado, los shadow tests permiten evaluar una variante sombra que recibe tráfico real pero cuyas respuestas no se envían a los usuarios, permitiendo comparar manualmente su rendimiento con el modelo de producción antes de decidir si se implementa.

### Consejo para el Examen
Para el examen AWS Certified Machine Learning - Associate, asegúrate de comprender las diferencias entre los tres modos de deployment guardrails (All at Once, Canary y Linear) y sus casos de uso específicos. El modo Linear es particularmente útil cuando necesitas evaluar la escalabilidad de un nuevo modelo bajo cargas crecientes. También es importante distinguir entre deployment guardrails (que puede realizar rollbacks automáticos) y shadow tests (que requiere evaluación manual en la consola de SageMaker). Recuerda que ambas funcionalidades soportan tanto endpoints de inferencia en tiempo real como endpoints asíncronos.

## 2. SageMaker's Inner Details and Production Variants

### 2.1. Interacción de SageMaker con Docker Containers

SageMaker utiliza Docker containers como base fundamental para la implementación y operación de modelos de machine learning. Esta arquitectura proporciona flexibilidad y portabilidad.

#### 2.1.1. Función de los Containers en SageMaker
- Todo modelo y despliegue en SageMaker debe estar alojado en un Docker container registrado en ECR (Elastic Container Registry)
- Estos containers pueden incluir:
  - Modelos de deep learning pre-construidos
  - Modelos de scikit-learn o Spark ML pre-construidos
  - Código propio construido sobre frameworks soportados como TensorFlow, MXNet, Chainer o PyTorch

#### 2.1.2. Distribución de TensorFlow
- TensorFlow no se distribuye automáticamente entre múltiples máquinas
- Para distribuir el entrenamiento a través de múltiples máquinas con múltiples GPUs se pueden usar:
  - **Horovod**: Framework para distribución de TensorFlow a través de una flota
  - **Parameter servers**: Alternativa para distribuir el procesamiento

#### 2.1.3. Flexibilidad de Implementación
- Es posible desarrollar código de entrenamiento o inferencia desde cero
- Se pueden extender imágenes pre-construidas de los algoritmos de SageMaker
- Cualquier script, en cualquier runtime o lenguaje, puede trabajar con SageMaker siempre que:
  - Esté en un Docker container
  - Cumpla las especificaciones sobre ubicación y llamada del código del modelo

### 2.2. Estructura de Docker en SageMaker

#### 2.2.1. Componentes Básicos
- **Docker file**: Define cómo construir la imagen
- **Docker image**: Incluye código y recursos
- **Docker container**: Instancia creada a partir de una imagen

#### 2.2.2. Flujo de Trabajo en SageMaker
1. Las Docker images se almacenan en Amazon ECR
2. SageMaker extrae la imagen de entrenamiento de ECR
3. Utiliza ese Docker container para entrenamiento, tomando datos de S3
4. Los modelos entrenados se almacenan en S3
5. El código de inferencia accede a estos artefactos del modelo para generar predicciones
6. El modelo se despliega en una flota de servidores con endpoints accesibles

### 2.3. Estructura de Directorios en Containers

#### 2.3.1. Container de Entrenamiento
Estructura bajo el directorio `/opt/ml`:
- **input/**: Contiene:
  - Archivos de configuración
  - Hiperparámetros
  - Archivos de configuración de recursos
  - Datos (posiblemente organizados en channels)
- **code/**: Contiene los archivos de script que ejecutan el código de entrenamiento
- **output/**: Destino para la salida, mensajes de error o fallas

#### 2.3.2. Container de Despliegue
- Debe contener un directorio `/opt/ml/model` donde residen los archivos asociados al código de inferencia

### 2.4. Estructura Completa de una Docker Image

Una imagen Docker completa para SageMaker típicamente contiene:
- **nginx.conf**: Archivo de configuración para el servidor web Nginx (frontend)
- **predictor.py**: Programa que implementa un servidor web Flask para realizar predicciones en tiempo de ejecución
- **Directorio server/**: Contiene el programa que inicia el servidor Gunicorn (ejecuta múltiples instancias de la aplicación Flask)
- **Directorio train/**: Contiene el programa invocado durante el entrenamiento
- **wsgi.py**: Pequeño wrapper utilizado para invocar la aplicación Flask para servir resultados

### 2.5. Ejemplo de Docker File

```
FROM tensorflow:whatever
RUN pip install sagemaker-containers
COPY train.py /opt/ml/code/train.py
ENV SAGEMAKER_PROGRAM=train.py
```

#### 2.5.1. Variables de Entorno Importantes
- **SAGEMAKER_PROGRAM**: Define el script específico que ejecuta el entrenamiento (obligatorio)
- Otras variables opcionales:
  - **TRAINING_MODULE** y **SERVICE_MODULE**: Dónde cargar módulos de TensorFlow, MXNet, etc.
  - **MODEL_DIR**: Donde se guardan los checkpoints del modelo y se suben a S3
  - **SM_CHANNELS**: Define los canales de entrenamiento, prueba y validación
  - **HPS**: Define los hiperparámetros expuestos por el algoritmo

### 2.6. Production Variants

#### 2.6.1. Concepto y Utilidad
- Permiten probar múltiples modelos en tráfico real simultáneamente
- Especialmente útiles cuando la evaluación offline no es efectiva (ej. sistemas de recomendación)
- Facilitan el despliegue controlado de nuevos modelos minimizando riesgos

#### 2.6.2. Variant Weights
- Define cómo SageMaker distribuye el tráfico entre diferentes production variants
- Permite implementar un enfoque gradual:
  - Comenzar con un pequeño porcentaje (ej. 10%) de tráfico al nuevo modelo
  - Aumentar gradualmente este porcentaje según se confirma el rendimiento
  - Eventualmente, migrar todo el tráfico (100%) al nuevo modelo si demuestra mejor rendimiento
  - Descartar la variante anterior una vez completada la migración

#### 2.6.3. Ventajas
- Facilita pruebas A/B en entornos reales
- Valida el rendimiento en condiciones de producción
- Ofrece mecanismo de rollback rápido en caso de problemas imprevistos
- Permite cambiar la distribución de tráfico en tiempo de ejecución

---

## Resumen para el Examen: SageMaker's Inner Details and Production Variants

SageMaker utiliza Docker containers para implementar modelos de machine learning, lo que proporciona flexibilidad para usar cualquier framework o código personalizado. Todos los modelos deben estar en containers registrados en ECR, con una estructura de directorios específica donde `/opt/ml/code` contiene los scripts de entrenamiento y `/opt/ml/model` aloja los archivos de inferencia. Para distribuir TensorFlow entre múltiples máquinas se pueden usar Horovod o parameter servers. Los production variants permiten probar múltiples versiones de un modelo en tráfico real, utilizando variant weights para controlar la distribución del tráfico, facilitando despliegues graduales y pruebas A/B en entornos de producción.

### Consejo para el Examen
Para el examen AWS Certified Machine Learning - Associate, memoriza la estructura de directorios dentro de los containers de SageMaker, especialmente la ubicación del código de entrenamiento (`/opt/ml/code`) y los artefactos del modelo para inferencia (`/opt/ml/model`). Presta atención a la variable de entorno obligatoria `SAGEMAKER_PROGRAM` que especifica el script de entrenamiento principal. Para las preguntas sobre despliegue seguro, recuerda que los production variants con variant weights permiten implementaciones graduales con capacidad de rollback, lo que constituye una mejor práctica frente a reemplazos completos e inmediatos de modelos en producción.


## 3. SageMaker On the Edge: SageMaker Neo and IoT Greengrass

### 3.1. Introducción a SageMaker Neo

SageMaker Neo permite implementar modelos entrenados en dispositivos edge (periféricos), ofreciendo la capacidad de compilar código de inferencia optimizado para diferentes dispositivos embebidos.

#### 3.1.1. Concepto Principal
- Neo permite "entrenar una vez y ejecutar en cualquier lugar"
- Optimiza los modelos para ejecutarse en dispositivos edge con recursos limitados
- Elimina la necesidad de realizar inferencia en la nube, reduciendo latencia

#### 3.1.2. Arquitecturas Soportadas
Neo soporta múltiples arquitecturas para dispositivos edge:
- Procesadores ARM
- Procesadores Intel
- Procesadores NVIDIA

### 3.2. Ventajas de la Implementación en Edge

#### 3.2.1. Reducción de Latencia
- La inferencia se realiza localmente en el dispositivo
- Elimina el tiempo de comunicación con servidores en la nube
- Crítico para aplicaciones sensibles al tiempo como vehículos autónomos

#### 3.2.2. Autonomía Operativa
- Los dispositivos pueden funcionar sin conexión constante a internet
- La toma de decisiones ocurre de manera local e inmediata

### 3.3. Frameworks Soportados

Neo puede compilar modelos creados en diversos frameworks:
- TensorFlow
- MXNet
- PyTorch
- ONNX
- XGBoost

### 3.4. Componentes de Neo

Neo consta de dos componentes principales:
- **Compilador**: Recompila el código para el bytecode específico de los procesadores edge
- **Biblioteca Runtime**: Se ejecuta en los dispositivos edge para consumir el código generado por Neo

### 3.5. Integración con AWS IoT Greengrass

#### 3.5.1. Despliegue a Endpoints HTTPS
- Es posible alojar un modelo compilado con Neo en instancias C5, M5, M4, P3 o P2
- La instancia debe ser del mismo tipo utilizado para compilar el modelo

#### 3.5.2. Despliegue a Dispositivos Edge
- Neo se integra con AWS IoT Greengrass para el despliegue a dispositivos edge
- Greengrass es el mecanismo para implementar código en dispositivos edge
- Permite inferencia en el borde usando datos locales con modelos entrenados en la nube

#### 3.5.3. Flujo de Trabajo Completo
1. Entrenar el modelo en la nube utilizando instancias de SageMaker
2. Compilar el modelo con Neo
3. Desplegar el modelo compilado a dispositivos edge usando IoT Greengrass
4. Greengrass utiliza funciones Lambda extensivamente para aplicaciones de inferencia

---

## Resumen para el Examen: SageMaker On the Edge

SageMaker Neo permite compilar modelos de machine learning para optimizar su ejecución en dispositivos edge con distintas arquitecturas (ARM, Intel, NVIDIA). Neo consta de un compilador y una biblioteca runtime, soportando modelos desarrollados en TensorFlow, MXNet, PyTorch, ONNX y XGBoost. Se integra con AWS IoT Greengrass para el despliegue de modelos en dispositivos edge, permitiendo realizar inferencia localmente con datos locales usando modelos entrenados en la nube, lo que resulta crucial para aplicaciones sensibles a la latencia como vehículos autónomos o cámaras inteligentes.

### Consejo para el Examen
Para el examen AWS Certified Machine Learning - Associate, recuerda la integración entre SageMaker Neo, AWS IoT Greengrass y Lambda. Este flujo de trabajo completo (entrenamiento en la nube, compilación con Neo, despliegue con Greengrass, ejecución con funciones Lambda) aparece frecuentemente en escenarios de preguntas. También es importante recordar que al desplegar un modelo compilado con Neo a un endpoint HTTPS, la instancia debe ser del mismo tipo utilizado durante la compilación.

## 4. SageMaker Resource Management: Instance Types and Spot Training

### 4.1. Selección de Instance Types para Machine Learning

La elección del tipo de instancia adecuado es crucial para optimizar tanto el rendimiento como el costo de las soluciones de machine learning en SageMaker.

#### 4.1.1. Instance Types para Deep Learning
- Los algoritmos de deep learning generalmente se benefician de instancias con GPU
- Para entrenamiento, se recomiendan instancias:
  - **P3**: Instancias de alto rendimiento con GPU
  - **G4**: Opciones más recientes optimizadas para deep learning

#### 4.1.2. Instance Types para Inferencia
- La inferencia suele ser menos exigente computacionalmente que el entrenamiento
- Se pueden utilizar instancias más ligeras y económicas:
  - **C5**: Instancias optimizadas para computación
  - **M5**: Instancias de propósito general

#### 4.1.3. Instance Types para Algoritmos No-DL
- Para algoritmos que no pueden acelerarse en GPU, se recomiendan:
  - **M5**: Instancias de propósito general
  - Otras instancias optimizadas para CPU

### 4.2. Consideraciones de Costo-Eficiencia

#### 4.2.1. Equilibrio de Costos
- Las instancias GPU son significativamente más costosas
- Solo deben utilizarse cuando realmente aportan beneficios de rendimiento
- Una máquina con múltiples GPUs puede ser más rentable que varias instancias CPU para el mismo trabajo

### 4.3. Managed Spot Training

#### 4.3.1. Concepto y Ventajas
- Utiliza instancias EC2 Spot para trabajos de entrenamiento
- Puede reducir los costos hasta en un 90% comparado con instancias on-demand
- Especialmente valioso para entrenamientos extensos y costosos (ej. modelos de traducción automática)

#### 4.3.2. Consideraciones y Limitaciones
- **Interrupciones**: Las instancias Spot pueden interrumpirse en cualquier momento
- **Checkpoints**: Es necesario implementar checkpoints a S3 para poder reanudar el entrenamiento en caso de interrupción
- **Tiempo de entrenamiento**: Puede aumentar debido a:
  - Posibles interrupciones y reanudaciones
  - Tiempo de espera para que las instancias Spot estén disponibles

#### 4.3.3. Balance entre Costo y Complejidad
- Mayor complejidad en la configuración (checkpoints a S3)
- Mayor tiempo total de entrenamiento
- Compensación con un ahorro significativo de costos

---

## Resumen para el Examen: SageMaker Resource Management

SageMaker ofrece distintos tipos de instancias para optimizar el rendimiento y costo de las soluciones de machine learning. Para deep learning, las instancias con GPU (P3, G4) son recomendadas para entrenamiento, mientras que para inferencia se pueden usar instancias más ligeras como C5 o M5. Para algoritmos que no aprovechan GPU, las instancias M5 de propósito general son apropiadas. Managed Spot Training permite reducir costos hasta en un 90% usando instancias EC2 Spot, pero requiere implementar checkpoints a S3 para manejar posibles interrupciones y puede aumentar el tiempo total de entrenamiento.

### Consejo para el Examen
Para el examen AWS Certified Machine Learning - Associate, recuerda que la elección del tipo de instancia depende directamente del algoritmo: siempre usa GPU (P3, G4) para deep learning y considera instancias de propósito general (M5) para otros algoritmos. Al enfrentarte a preguntas sobre optimización de costos, considera Managed Spot Training como una solución viable, pero ten presente que requiere implementar checkpoints a S3 y aceptar posibles aumentos en el tiempo de entrenamiento. Este balance entre costo, complejidad y tiempo es un tema recurrente en el examen.


## 5. SageMaker Resource Management: Automatic Scaling

### 5.1. Automatic Scaling en SageMaker

SageMaker permite configurar el escalado automático de recursos para modelos desplegados en producción, ajustando dinámicamente la capacidad según las necesidades.

#### 5.1.1. Configuración del Escalado
Al implementar un modelo de inferencia, se puede configurar una política de escalado que incluye:
- **Target metrics**: Métricas objetivo que activarán el escalado
- **Capacidad mínima y máxima**: Límites para el número de instancias
- **Cooldown periods**: Períodos de estabilización entre eventos de escalado

#### 5.1.2. Funcionamiento con CloudWatch
- Automatic Scaling trabaja en conjunto con CloudWatch
- CloudWatch monitoriza el rendimiento de los nodos de inferencia
- Basándose en estas métricas, se ajusta dinámicamente el número de instancias

### 5.2. Escalado por Production Variant

#### 5.2.1. Escalado Granular
- El escalado no se aplica al modelo como un todo
- Se ajusta individualmente para cada production variant
- Al cambiar el tráfico entre diferentes variantes en tiempo de ejecución, cada variante se escala de forma independiente

#### 5.2.2. Ventajas
- Mayor eficiencia en la utilización de recursos
- Adaptación precisa a patrones de tráfico variables
- Optimización de costos al escalar solo lo necesario

### 5.3. Mejores Prácticas

#### 5.3.1. Load Testing
- Es fundamental realizar pruebas de carga antes de implementar el escalado automático en producción
- Verificar que la política de escalado funciona según lo esperado
- Evitar problemas de capacidad insuficiente o excesiva en entornos reales

### 5.4. Uso de Availability Zones

#### 5.4.1. Distribución Automática
- SageMaker intenta distribuir automáticamente las instancias entre diferentes availability zones
- Mejora la resiliencia frente a fallos de infraestructura
- Requiere más de una instancia para funcionar (no puede distribuir con una sola instancia)

#### 5.4.2. Recomendaciones de Implementación
- Desplegar múltiples instancias para cada endpoint de producción
- Incluso si la carga solo requiere una instancia, usar múltiples mejora la resiliencia
- Beneficio adicional de disponibilidad y tolerancia a fallos

#### 5.4.3. Configuración de VPC
- Si se utilizan VPC personalizadas con SageMaker:
  - Configurar al menos dos subnets
  - Cada subnet debe estar en una availability zone diferente
  - Garantiza la distribución correcta entre zonas
  - Proporciona continuidad del servicio en caso de fallo catastrófico en una zona

---

## Resumen para el Examen: SageMaker Automatic Scaling

SageMaker Automatic Scaling permite ajustar dinámicamente la capacidad de los modelos desplegados, configurando políticas de escalado con métricas objetivo, capacidades mínimas/máximas y períodos de estabilización. Trabaja con CloudWatch para monitorizar el rendimiento y escala cada production variant de forma independiente. SageMaker también distribuye automáticamente las instancias entre diferentes availability zones para mejorar la resiliencia, pero requiere múltiples instancias y, en caso de usar VPCs personalizadas, al menos dos subnets en diferentes zonas.

### Consejo para el Examen
Para el examen AWS Certified Machine Learning - Associate, presta especial atención a la integración entre Automatic Scaling y CloudWatch, así como al escalado independiente por production variant. Recuerda que el load testing es una práctica esencial antes de implementar el escalado automático en producción. También es importante recordar que para aprovechar la distribución en múltiples availability zones se necesitan al menos dos instancias por endpoint y, en caso de usar VPCs personalizadas, configurar subnets en diferentes zonas. Estos puntos suelen aparecer en preguntas sobre alta disponibilidad y optimización de costos en SageMaker.


## 6. SageMaker: Deploying Models for Inference

### 6.1. Métodos de Despliegue de Modelos para Inferencia

Una vez que se ha entrenado y validado un modelo, es necesario desplegarlo para poder utilizarlo en aplicaciones reales. SageMaker ofrece varias opciones para este propósito.

#### 6.1.1. SageMaker JumpStart
- La forma más sencilla de desplegar modelos
- Permite utilizar modelos pre-entrenados para casos de uso comunes
- Proporciona templates y notebooks que automatizan todo el proceso
- Despliega a endpoints pre-configurados específicos para cada modelo
- Ideal cuando no se requiere personalización detallada

#### 6.1.2. Model Builder (SageMaker Python SDK)
- Ofrece mayor control sobre el proceso de despliegue
- Permite configurar ajustes de despliegue mediante código
- Apropiado cuando se necesita personalizar aspectos específicos del despliegue

#### 6.1.3. AWS CloudFormation
- Proporciona control robusto y avanzado
- Ideal para despliegues consistentes y repetibles
- Recomendado para integración en sistemas CI/CD
- El recurso específico para SageMaker en CloudFormation es `AWS::SageMaker::Model`

### 6.2. Tipos de Inferencia

#### 6.2.1. Real-Time Inference
- Diseñada para cargas de trabajo interactivas con requisitos de baja latencia
- Proporciona respuestas inmediatas para datos individuales
- Ejemplo: evaluación de solicitudes de tarjetas de crédito para detectar fraude
- Requiere infraestructura dedicada y disponible constantemente

#### 6.2.2. SageMaker Serverless Inference
- Elimina la necesidad de gestionar la infraestructura subyacente
- Ventajas:
  - Ahorro de costos durante períodos de inactividad
  - Ideal para tráfico irregular o con picos ocasionales
- Consideraciones:
  - Requiere tolerancia a "cold starts" (arranques en frío)
  - Puede no ser adecuado para requisitos de latencia muy estrictos
  - Menos rentable si el tráfico es constante y uniforme

#### 6.2.3. Asynchronous Inference
- Permite manejar cargas útiles de gran tamaño (hasta 1GB)
- Adecuada para procesos que requieren tiempos prolongados de procesamiento
- Funcionamiento:
  - Se envían los datos rápidamente
  - El procesamiento ocurre de forma asíncrona
  - Se devuelve el resultado cuando está listo
- Apropiada para cargas de trabajo interactivas que pueden tolerar cierta demora en la respuesta
- Evita que las aplicaciones se bloqueen esperando respuestas

### 6.3. Optimización de Despliegues

#### 6.3.1. Auto Scaling
- Ajusta dinámicamente los recursos de computación según el tráfico
- Evita la intervención manual durante aumentos repentinos de tráfico
- Representa un punto intermedio entre gestión completa y serverless

#### 6.3.2. SageMaker Neo
- Optimiza automáticamente los modelos para entornos específicos
- Particularmente útil para despliegues en AWS Inferentia (chips optimizados para inferencia)
- Puede mejorar significativamente el rendimiento de la inferencia
- Herramienta importante para acelerar el proceso de inferencia

---

## Resumen para el Examen: SageMaker Deploying Models for Inference

SageMaker ofrece múltiples métodos para desplegar modelos, desde la sencillez de JumpStart hasta el control avanzado con CloudFormation. Para inferencia, existen tres opciones principales: Real-Time Inference (para respuestas inmediatas), Serverless Inference (para tráfico irregular y ahorro de costos en períodos inactivos) y Asynchronous Inference (para cargas útiles grandes y tiempos de procesamiento prolongados). Estas opciones pueden optimizarse con Auto Scaling para gestionar cambios en el tráfico y SageMaker Neo para mejorar el rendimiento en entornos específicos como AWS Inferentia.

### Consejo para el Examen
Para el examen AWS Certified Machine Learning - Associate, es fundamental memorizar las diferencias entre los tipos de inferencia y cuándo usar cada uno. El examen suele preguntar sobre casos de uso específicos donde debes recomendar el tipo de inferencia más adecuado. Recuerda que Serverless Inference es ideal para tráfico irregular con períodos de inactividad, pero implica aceptar "cold starts". La Asynchronous Inference es la elección correcta para grandes volúmenes de datos (hasta 1GB) y procesos que requieren mucho tiempo de procesamiento. No olvides mencionar SageMaker Neo cuando te pregunten sobre optimización de rendimiento en inferencia, especialmente para dispositivos edge o chips Inferentia.


## 7. SageMaker Serverless Inference and Inference Recommender

### 7.1. SageMaker Serverless Inference

Introducida en 2022, SageMaker Serverless Inference representa el avance de AWS hacia soluciones completamente serverless, eliminando la necesidad de gestionar infraestructura para inferencia.

#### 7.1.1. Concepto y Funcionamiento
- El usuario solo especifica:
  - El container a utilizar
  - Los requisitos de memoria del container
  - Los requisitos de concurrencia
- AWS gestiona automáticamente la capacidad subyacente
- Provisiona y escala la infraestructura según necesidades

#### 7.1.2. Casos de Uso Ideales
- Tráfico infrecuente hacia el modelo de inferencia
- Patrones de tráfico impredecibles
- Cuando se busca optimizar costos basados en uso real

#### 7.1.3. Ventajas de Costos
- Escalado automático hasta cero cuando no hay solicitudes
- Facturación basada exclusivamente en uso
- Mayor tráfico = mayor costo; menor tráfico = menor costo
- Optimización automática de gastos

#### 7.1.4. Monitorización vía CloudWatch
Métricas clave disponibles:
- **Model Setup Time**: Tiempo requerido para desplegar nuevos modelos de inferencia durante el escalado
- **Invocations**: Número de solicitudes procesadas
- **Invocation Errors**: Solicitudes que generaron errores
- **Memory Utilization**: Uso de memoria a lo largo del tiempo

#### 7.1.5. Posibles Problemas
- Proporcionar información incorrecta sobre requisitos de memoria o concurrencia
- Si la información es precisa, SageMaker añadirá o eliminará instancias automáticamente para manejar el tráfico

### 7.2. Amazon SageMaker Inference Recommender

Una herramienta especializada para ayudar a seleccionar los tipos de instancias óptimos para despliegues de inferencia.

#### 7.2.1. Propósito
- Automatiza pruebas de carga y ajuste de modelos
- Puede desplegar automáticamente al tipo de endpoint de inferencia óptimo
- Proporciona recomendaciones sobre configuraciones de infraestructura, no sobre el modelo en sí

#### 7.2.2. Proceso de Funcionamiento
1. Registrar el modelo SageMaker en el Model Registry
2. El sistema realiza benchmarks de diferentes configuraciones de endpoints
3. Recopila y visualiza métricas para cada configuración
4. Facilita la decisión sobre qué tipos de instancias funcionan mejor para el modelo específico

#### 7.2.3. Información Proporcionada
Para cada configuración recomendada, muestra:
- Tipo de instancia sugerido
- Costo por hora
- Costo por inferencia
- Latencia

#### 7.2.4. Modos de Operación

1. **Instance Recommendations**:
   - Ejecuta pruebas de carga en tipos de instancias recomendados automáticamente
   - Proceso rápido (aproximadamente 45 minutos)
   - Ideal para obtener respuestas rápidas sobre posibles tipos de instancias

2. **Endpoint Recommendations** (Custom Load Tests):
   - El usuario especifica explícitamente:
     - Tipos de instancias a probar
     - Patrones de tráfico esperados
     - Requisitos de latencia y throughput
   - Proceso más largo (aproximadamente 2 horas)
   - Resultados más específicos y adaptados a SLAs particulares
   - Más eficiente para requisitos detallados y específicos

---

## Resumen para el Examen: SageMaker Serverless Inference and Inference Recommender

SageMaker Serverless Inference permite desplegar modelos sin gestionar infraestructura, especificando solo el container, requisitos de memoria y concurrencia. AWS gestiona automáticamente la capacidad, escalando a cero durante inactividad y facturando solo por uso real. Se monitoriza mediante CloudWatch con métricas como Model Setup Time, Invocations, Errors y Memory Utilization. Por otro lado, SageMaker Inference Recommender ayuda a seleccionar tipos de instancias óptimos para inferencia, ofreciendo dos modos: Instance Recommendations (rápido, 45 minutos) para obtener sugerencias generales, y Endpoint Recommendations (2 horas) para pruebas personalizadas basadas en requisitos específicos de tráfico, latencia y throughput.

### Consejo para el Examen
Para el examen AWS Certified Machine Learning - Associate, recuerda las diferencias clave entre los tres enfoques de infraestructura para inferencia: Serverless Inference (completamente gestionado, ideal para tráfico variable), Auto Scaling (punto intermedio con gestión parcial) y configuración manual con Inference Recommender. Presta especial atención a los dos modos de Inference Recommender: Instance Recommendations (rápido, genérico) y Endpoint Recommendations (detallado, personalizado). Este conocimiento es crucial para escenarios donde debes recomendar la mejor opción según requisitos específicos de costo, latencia y patrones de tráfico.


## 8. SageMaker Inference Pipelines

### 8.1. Concepto de Inference Pipelines

SageMaker Inference Pipelines permite conectar múltiples containers de Docker para crear flujos de procesamiento de inferencia más complejos y potentes.

#### 8.1.1. Definición y Estructura
- Permite usar más de un container y encadenarlos en secuencia
- Crea una secuencia lineal de containers que trabajan coordinadamente
- Soporta entre 2 y 15 containers en una sola pipeline

#### 8.1.2. Componentes Posibles
- Algoritmos pre-entrenados integrados en SageMaker
- Algoritmos personalizados alojados en containers de Docker
- Containers de SparkML y scikit-learn

### 8.2. Casos de Uso y Aplicaciones

#### 8.2.1. Flujos de Trabajo Completos
Permite integrar en una sola pipeline todas las fases del proceso de inferencia:
- Pre-procesamiento de datos
- Generación de predicciones
- Post-procesamiento de resultados

#### 8.2.2. Integración con SparkML
- Se puede ejecutar con AWS Glue o EMR
- Los containers de SparkML se serializan en formato MLeap
- Permite aprovechar las capacidades de procesamiento distribuido

### 8.3. Modos de Operación

#### 8.3.1. Versatilidad de Implementación
Las inference pipelines pueden aplicarse en dos modos diferentes:
- **Real-time inference**: Para respuestas interactivas a través de servicios web
- **Batch transforms**: Para procesar grandes volúmenes de datos y generar predicciones de forma masiva

#### 8.3.2. Flexibilidad de Uso
- La misma estructura de pipeline puede utilizarse en ambos modos
- Permite reutilizar la lógica tanto para escenarios en tiempo real como para procesamiento por lotes

---

## Resumen para el Examen: SageMaker Inference Pipelines

SageMaker Inference Pipelines permite encadenar entre 2 y 15 containers de Docker en una secuencia lineal para crear flujos completos de inferencia. Estas pipelines pueden incorporar algoritmos pre-entrenados de SageMaker, algoritmos personalizados, y containers de SparkML (serializados en formato MLeap) o scikit-learn. Son útiles para integrar pre-procesamiento, predicción y post-procesamiento en un único flujo, y pueden implementarse tanto para inferencia en tiempo real como para transformaciones por lotes (batch transforms).

### Consejo para el Examen
Para el examen AWS Certified Machine Learning - Associate, recuerda que las Inference Pipelines soportan específicamente entre 2 y 15 containers, y que los containers de SparkML se serializan en formato MLeap cuando se integran en estas pipelines. Un punto clave a recordar es la versatilidad de estas pipelines, que pueden utilizarse tanto para inferencia en tiempo real como para batch transforms sin modificar su estructura. Esta flexibilidad es una ventaja significativa cuando necesitas aplicar el mismo procesamiento a diferentes escenarios de inferencia.


## 9. SageMaker Model Monitor

### 9.1. Concepto y Propósito

SageMaker Model Monitor es una funcionalidad que permite supervisar automáticamente los modelos desplegados para detectar desviaciones de calidad y recibir alertas cuando ocurren cambios significativos.

#### 9.1.1. Objetivos Principales
- Supervisar los modelos desplegados en producción
- Detectar desviaciones de calidad automáticamente
- Alertar sobre cambios que requieren atención
- Integración con CloudWatch para notificaciones

### 9.2. Tipos de Supervisión

#### 9.2.1. Data Drift (Deriva de Datos)
- Detecta cambios en las propiedades estadísticas de los datos
- Ejemplo: ingresos que aumentan con el tiempo debido a la inflación
- Datos faltantes o características que cambian con el tiempo
- Visualiza estos cambios y alerta cuando superan umbrales

#### 9.2.2. Anomalías y Outliers
- Identifica valores atípicos o anómalos en los datos entrantes
- Monitoriza su aparición a lo largo del tiempo
- Alerta cuando se detectan patrones inusuales

#### 9.2.3. Nuevas Características
- Detecta cuando aparecen nuevas características en los datos
- Útil para identificar información adicional disponible
- Alerta sobre posibles reemplazos de características antiguas

#### 9.2.4. Bias (Sesgo)
- Integración con Clarify para detectar sesgos potenciales
- Identificación de desequilibrios entre diferentes grupos
- Supervisión de cambios en el impacto relativo de las características
- Alertas cuando emergen nuevos sesgos

### 9.3. Implementación y Configuración

#### 9.3.1. Facilidad de Uso
- No requiere código (código opcional)
- Configurable a través de SageMaker Studio
- Dashboard basado en web para visualización

#### 9.3.2. Configuración Técnica
- Datos almacenados en S3 con opciones de seguridad estándar
- Requiere configurar un "monitoring schedule" para trabajos recurrentes
- Métricas emitidas a CloudWatch para alertas y acciones

### 9.4. Métricas Específicas de Supervisión

#### 9.4.1. Data Quality (Calidad de Datos)
- Monitoriza propiedades estadísticas de las características
- Incluye media, desviación estándar, mínimo, máximo
- Se compara con una línea base establecida explícitamente

#### 9.4.2. Model Quality (Calidad del Modelo)
- Supervisa métricas como precisión, RMSE, recall
- Requiere establecer una línea base de calidad
- Puede integrarse con etiquetas de "ground truth"
- Compara predicciones con evaluaciones humanas

#### 9.4.3. Bias Drift (Deriva de Sesgo)
- Utiliza Clarify para detectar nuevos sesgos
- Alerta sobre cambios en el equilibrio entre grupos

#### 9.4.4. Feature Attribution Drift (Deriva de Atribución de Características)
- Monitoriza cambios en la importancia relativa de las características
- Utiliza NDCG (Normalized Discounted Cumulative Gain)
- Compara el ranking de características entre datos de entrenamiento y datos en vivo

### 9.5. Model Monitor Data Capture

#### 9.5.1. Funcionalidad
- Registra todas las entradas a los endpoints y las salidas de inferencia
- Entrega los datos a S3 como archivos JSON
- Almacena la información para uso futuro

#### 9.5.2. Aplicaciones
- Entrenamiento adicional con nuevos datos
- Parte del ciclo de retroalimentación para entrenamiento continuo
- Depuración y monitorización
- Comparación automática con métricas de línea base

#### 9.5.3. Compatibilidad
- Soportado para modos de monitorización en tiempo real y por lotes
- Compatible con Python usando la biblioteca Boto
- Compatible con SageMaker Python SDK
- Opción para cifrar los datos capturados

### 9.6. Integración con Otras Herramientas

- **CloudWatch**: Para notificaciones y alarmas
- **TensorBoard**: Para visualización avanzada
- **QuickSight**: Para análisis de datos y dashboards
- **Tableau**: Para visualización y reportes corporativos
- **SageMaker Studio**: Para visualización integrada

---

## Resumen para el Examen: SageMaker Model Monitor

SageMaker Model Monitor permite supervisar automáticamente los modelos desplegados, detectando data drift, anomalías, nuevas características y sesgos. Integrado con CloudWatch para alertas, monitoriza cuatro aspectos principales: calidad de datos (propiedades estadísticas), calidad del modelo (precisión, RMSE), bias drift (usando Clarify) y feature attribution drift (usando NDCG). La funcionalidad Model Monitor Data Capture registra entradas y salidas de los endpoints para entrenamiento adicional, depuración y comparación con líneas base, soportando modos en tiempo real y por lotes, con opciones de cifrado y compatible con diversas herramientas de visualización como TensorBoard, QuickSight y Tableau.

### Consejo para el Examen
Para el examen AWS Certified Machine Learning - Associate, enfócate en los cuatro tipos de supervisión que ofrece Model Monitor: data quality, model quality, bias drift y feature attribution drift. Recuerda que Model Monitor Data Capture puede usarse para crear un ciclo de retroalimentación continua donde los datos capturados en producción se utilizan para reentrenar el modelo. Es importante entender que el monitoreo requiere establecer líneas base explícitas contra las cuales se comparan las métricas actuales, y que todas las alertas se configuran a través de CloudWatch. Este conocimiento es crucial para responder preguntas sobre cómo mantener y mejorar modelos en producción a lo largo del tiempo.


## 10. MLOps with SageMaker, Kubernetes, SageMaker Projects, and SageMaker Pipelines

### 10.1. Introducción a MLOps en AWS

MLOps (Machine Learning Operations) se ha consolidado como disciplina crítica después de la primera versión del examen AWS Certified Machine Learning. SageMaker ha desarrollado varias soluciones para abordar este campo en evolución.

#### 10.1.1. Concepto de MLOps
- Se refiere a la gestión del flujo de trabajo completo alrededor de modelos de ML
- Abarca construcción, prueba y despliegue de modelos
- Integra procesos existentes con nuevas capacidades de AWS

### 10.2. Integración con Kubernetes

Para organizaciones que ya utilizan Kubernetes como base para sus pipelines de ML, AWS ofrece diferentes opciones de integración.

#### 10.2.1. Amazon SageMaker Operators for Kubernetes
- Encapsula operaciones de SageMaker para que Kubernetes pueda utilizarlas
- Permite tratar recursos de SageMaker como cualquier otro recurso de Kubernetes
- Se instala en Amazon Elastic Kubernetes Service (EKS)
- Permite crear trabajos de SageMaker usando la API de Kubernetes
- Herramientas como kubectl pueden iniciar trabajos de SageMaker

#### 10.2.2. Estructura de Operación
- Los operadores de SageMaker se ejecutan junto al kubelet en un nodo EC2
- Operan como controladores de aplicaciones que gestionan aplicaciones en nombre del usuario
- Facilitan la transición gradual hacia SageMaker desde infraestructuras existentes

### 10.3. Componentes para Kubeflow Pipelines

Kubeflow es la solución de MLOps nativa de Kubernetes, y SageMaker ofrece componentes específicos para integrarse con ella.

#### 10.3.1. Tipos de Componentes
SageMaker proporciona componentes para diferentes etapas del proceso de ML:
- **Procesamiento**: Para tareas como contenedores Spark
- **Hyperparameter Tuning**: Para optimización de hiperparámetros
- **Training**: Para entrenamiento de modelos
- **Inference**: Para alojar modelos y realizar predicciones

#### 10.3.2. Beneficios
- Permite flujos de trabajo híbridos de ML
- Ideal para organizaciones con infraestructura on-premises existente
- Facilita la migración gradual hacia la nube
- Permite mantener ciertos procesos on-premises (ej. datos sensibles)

### 10.4. SageMaker Projects

Para quienes prefieren una solución nativa de SageMaker sin depender de Kubernetes, AWS ofrece SageMaker Projects.

#### 10.4.1. Concepto y Funcionalidad
- Solución nativa de MLOps dentro de SageMaker Studio
- Implementa integración continua y despliegue continuo (CI/CD)
- Permite crear flujos de trabajo completos para el ciclo de vida de ML

#### 10.4.2. Capacidades
Permite automatizar todas las etapas del proceso de ML:
- Construcción de imágenes
- Preparación de datos
- Ingeniería de características
- Entrenamiento de modelos
- Evaluación de modelos
- Despliegue de modelos
- Monitorización y actualización

#### 10.4.3. Componentes
- Normalmente utiliza repositorios de código para almacenar el código fuente
- Implementa SageMaker Pipelines para definir los pasos del flujo de trabajo

### 10.5. SageMaker Pipelines

#### 10.5.1. Definición y Propósito
- Mecanismo para encadenar diferentes pasos dentro de SageMaker
- Define flujos de trabajo completos para procesamiento, entrenamiento y despliegue
- Facilita la automatización del ciclo de vida completo del ML

#### 10.5.2. Ejemplo de Pipeline Básico
Un pipeline típico puede incluir pasos como:
1. Procesamiento de datos
2. Entrenamiento del modelo
3. Evaluación del modelo
4. Registro en el Model Registry
5. Almacenamiento en S3

### 10.6. Ejemplo Complejo de Proyecto SageMaker

#### 10.6.1. Flujo de Trabajo End-to-End
Un proyecto SageMaker completo puede integrar múltiples servicios de AWS:

1. **Fase de Construcción**:
   - Commit de código al repositorio
   - Amazon EventBridge detecta el cambio
   - AWS CodePipeline inicia el proceso
   - AWS CodeBuild ejecuta el SageMaker Pipeline
   - El pipeline procesa datos, entrena, evalúa y registra el modelo

2. **Fase de Despliegue**:
   - EventBridge detecta el registro del nuevo modelo
   - Se inicia otro CodePipeline para despliegue
   - CodeBuild prepara CloudFormation
   - CloudFormation despliega el endpoint en entorno de staging
   - CodeBuild realiza pruebas en el endpoint de staging
   - Aprobación manual (si es necesaria)
   - CloudFormation despliega a producción mediante SageMaker hosting

---

## Resumen para el Examen: MLOps with SageMaker

AWS ofrece múltiples opciones para implementar MLOps, adaptándose a diferentes necesidades organizacionales. Para entornos Kubernetes existentes, proporciona SageMaker Operators for Kubernetes y componentes para Kubeflow Pipelines, permitiendo integración gradual y flujos de trabajo híbridos. Para quienes prefieren una solución nativa de AWS, SageMaker Projects ofrece capacidades completas de CI/CD utilizando SageMaker Pipelines para definir flujos de trabajo. Estos proyectos pueden integrar diversos servicios AWS como EventBridge, CodePipeline, CodeBuild y CloudFormation para automatizar todo el ciclo de vida del ML, desde el desarrollo hasta el despliegue en producción.

### Consejo para el Examen
Para el examen AWS Certified Machine Learning - Associate, es crucial entender la diferencia entre las distintas opciones de MLOps: SageMaker Operators for Kubernetes (para integración con Kubernetes existente), componentes para Kubeflow (para integración con pipelines de Kubeflow) y SageMaker Projects con SageMaker Pipelines (solución nativa). Recuerda que SageMaker Projects es el término para la solución completa de MLOps, mientras que SageMaker Pipelines se refiere específicamente a la definición de los pasos del flujo de trabajo dentro de ese proyecto. Las preguntas del examen suelen enfocarse en escenarios donde debes recomendar la mejor solución según la infraestructura existente y los requisitos de integración.


## 11. Containers, Docker, y Servicios de Contenedores de AWS

### 11.1. Fundamentos de Docker

Docker es una plataforma de desarrollo de software diseñada para desplegar aplicaciones en contenedores, proporcionando portabilidad y consistencia entre diferentes entornos.

#### 11.1.1. Características Principales
- Empaqueta aplicaciones en contenedores estandarizados
- Garantiza que las aplicaciones se ejecuten de la misma manera independientemente del entorno
- Elimina problemas de compatibilidad
- Comportamiento predecible y mantenimiento simplificado
- Compatible con cualquier lenguaje, sistema operativo o tecnología

#### 11.1.2. Casos de Uso
- Arquitecturas de microservicios
- Migración de aplicaciones desde entornos on-premises a la nube
- Cualquier escenario que requiera contenedores

#### 11.1.3. Funcionamiento Básico
- Un servidor (como una instancia EC2) ejecuta un agente Docker
- Múltiples contenedores Docker pueden ejecutarse sobre el mismo agente
- Los contenedores pueden contener diferentes aplicaciones (Java, Node.js, MySQL, etc.)
- Los contenedores del mismo tipo pueden ejecutarse múltiples veces

#### 11.1.4. Repositorios Docker
- **Docker Hub**: Repositorio público con imágenes base para múltiples tecnologías
- **Amazon ECR**: Elastic Container Registry para imágenes privadas
- **Amazon ECR Public Gallery**: Versión pública del repositorio ECR

### 11.2. Docker vs Máquinas Virtuales

#### 11.2.1. Diferencias Arquitectónicas
- **Máquinas Virtuales**:
  - Infraestructura → Sistema operativo host → Hipervisor → Sistema operativo invitado + Aplicaciones
  - Cada VM está completamente aislada
  - Modelo utilizado por instancias EC2

- **Docker**:
  - Infraestructura → Sistema operativo host → Docker Daemon → Contenedores
  - Los contenedores comparten recursos y pueden comunicarse entre sí
  - Más ligero y permite ejecutar más aplicaciones por servidor

### 11.3. Proceso de Trabajo con Docker

1. Escribir un Dockerfile que define cómo será el contenedor
2. Construir (build) la imagen Docker
3. Almacenar (push) la imagen en un repositorio (Docker Hub o ECR)
4. Descargar (pull) la imagen desde el repositorio
5. Ejecutar (run) la imagen para crear un contenedor activo

### 11.4. Servicios de Gestión de Contenedores en AWS

#### 11.4.1. Amazon ECS (Elastic Container Service)
- Plataforma propia de AWS para gestión de Docker
- Permite ejecutar contenedores Docker como tareas ECS en un clúster ECS

#### 11.4.2. Tipos de Lanzamiento en ECS

1. **EC2 Launch Type**:
   - El clúster ECS está compuesto por instancias EC2
   - El usuario debe aprovisionar y mantener la infraestructura
   - Cada instancia ejecuta el Agente ECS que la registra en el clúster
   - AWS gestiona la ubicación de los contenedores en las instancias EC2

2. **Fargate Launch Type**:
   - No requiere provisionar infraestructura (serverless)
   - Se definen las tareas con requisitos de CPU y RAM
   - AWS ejecuta las tareas automáticamente
   - Escalado simplificado aumentando el número de tareas
   - Recomendado en el examen por su facilidad de gestión

#### 11.4.3. Roles IAM para Tareas ECS

- **EC2 Instance Profile**: (Solo para EC2 Launch Type)
  - Utilizado por el Agente ECS
  - Permite llamadas API a servicios ECS, CloudWatch Logs, ECR, Secrets Manager, SSM Parameter Store

- **ECS Task Role**:
  - Válido tanto para EC2 Launch Type como para Fargate
  - Un rol específico por tarea
  - Define los servicios AWS a los que puede acceder cada tarea
  - Se especifica en la definición de la tarea

#### 11.4.4. Integración con Load Balancers
- **Application Load Balancer**: Recomendado para la mayoría de casos, compatible con Fargate
- **Network Load Balancer**: Para casos de alto rendimiento o uso con AWS PrivateLink
- **Classic Load Balancer**: No recomendado, sin características avanzadas, incompatible con Fargate

#### 11.4.5. Persistencia de Datos
- Amazon EFS es la opción destacada para volúmenes de datos
- Compatible tanto con EC2 Launch Type como con Fargate
- Permite compartir datos entre tareas en diferentes zonas de disponibilidad
- La combinación Fargate + EFS ofrece una solución completamente serverless

### 11.5. Amazon EKS (Elastic Kubernetes Service)

#### 11.5.1. Concepto y Diferenciación
- Servicio para lanzar y gestionar clústeres Kubernetes en AWS
- Kubernetes es un sistema de código abierto para despliegue, escalado y gestión de aplicaciones en contenedores
- Alternativa a ECS con una API diferente
- ECS es propietario de AWS mientras Kubernetes es estándar en múltiples proveedores cloud

#### 11.5.2. Modos de Lanzamiento
- **EC2 Launch Mode**: Despliega nodos trabajadores como instancias EC2
- **Fargate Mode**: Despliega contenedores serverless en un clúster EKS

#### 11.5.3. Casos de Uso
- Empresas que ya utilizan Kubernetes on-premises o en otra nube
- Preferencia por la API de Kubernetes
- Necesidad de una solución agnóstica entre diferentes proveedores cloud

#### 11.5.4. Tipos de Nodos
- **Managed Node Groups**: AWS crea y gestiona nodos (instancias EC2) como parte de un grupo de Auto Scaling
- **Self-Managed Nodes**: Mayor control y personalización, el usuario crea y registra los nodos
- **Fargate**: Sin mantenimiento de nodos, ejecución serverless de contenedores

#### 11.5.5. Volúmenes de Datos
- Requiere especificar un manifiesto StorageClass en el clúster
- Utiliza drivers compatibles con Container Storage Interface (CSI)
- Soporta Amazon EBS, EFS (único compatible con Fargate), FSx for Lustre y FSx for NetApp ONTAP

### 11.6. Amazon ECR (Elastic Container Registry)

#### 11.6.1. Propósito y Opciones
- Almacena y gestiona imágenes Docker en AWS
- Ofrece repositorios privados (solo para tu cuenta) o públicos (Amazon ECR Public Gallery)

#### 11.6.2. Características
- Integración completa con ECS
- Imágenes almacenadas en Amazon S3
- Acceso protegido mediante IAM
- Análisis de vulnerabilidades de imágenes
- Versionado, etiquetas de imágenes y ciclo de vida

#### 11.6.3. Funcionamiento con ECS
- Las instancias EC2 en un clúster ECS pueden extraer imágenes de ECR
- Se requiere un rol IAM adecuado para permitir esta operación
- Los contenedores se inician en las instancias tras la descarga de las imágenes

---

## Resumen para el Examen: Containers, Docker, y Servicios de Contenedores de AWS

AWS ofrece un ecosistema robusto de servicios para la gestión de contenedores, adaptándose a diferentes necesidades y estrategias de implementación. Docker es la tecnología de contenedores dominante que permite empaquetar aplicaciones con todas sus dependencias para garantizar consistencia entre entornos.

Amazon ECS (Elastic Container Service) es el servicio nativo de AWS para gestionar contenedores Docker, ofreciendo dos opciones de implementación: EC2 Launch Type, donde tú gestionas la infraestructura subyacente, y Fargate Launch Type, completamente serverless y que elimina la necesidad de administrar servidores. Es crucial entender que ECS utiliza "tareas" como unidad de trabajo (equivalente a contenedores o grupos de contenedores) que se ejecutan en "clústeres".

Los roles IAM en ECS se dividen en dos categorías: EC2 Instance Profile (utilizado por el agente ECS en cada instancia EC2) y ECS Task Role (que define los permisos específicos para cada tarea individual). Esta distinción es importante para implementar correctamente un modelo de seguridad de privilegio mínimo.

Amazon EKS (Elastic Kubernetes Service) proporciona una alternativa basada en el estándar de código abierto Kubernetes, ideal para entornos multi-cloud o para organizaciones que ya utilizan Kubernetes. A diferencia de ECS (que tiene una API propietaria de AWS), EKS sigue el estándar Kubernetes, facilitando la portabilidad entre proveedores cloud. EKS introduce terminología específica como "pods" (equivalente a tareas en ECS) y diferentes estrategias de gestión de nodos.

Para la persistencia de datos, Amazon EFS destaca como la solución más versátil, siendo compatible tanto con ECS como con EKS en modo Fargate, permitiendo compartir datos entre contenedores en diferentes zonas de disponibilidad.

Amazon ECR completa el ecosistema proporcionando un repositorio seguro para almacenar, gestionar y desplegar imágenes Docker, con características avanzadas como análisis de vulnerabilidades, versionado y políticas de ciclo de vida, todo integrado con el modelo de seguridad IAM de AWS.

En el examen, es probable que se presenten escenarios donde debas determinar qué servicio (ECS o EKS) y qué tipo de lanzamiento (EC2 o Fargate) es más adecuado según los requisitos específicos de gestión, portabilidad, control y costos.

### Consejo para el Examen
Para el examen AWS Certified Machine Learning - Associate, enfócate en las diferencias entre ECS (propietario de AWS) y EKS (basado en Kubernetes de código abierto), y entre los tipos de lanzamiento EC2 y Fargate. Recuerda que Fargate es generalmente la opción recomendada para casos de uso simples por su naturaleza serverless, mientras que EKS es preferible para organizaciones que ya utilizan Kubernetes o necesitan portabilidad entre nubes. También es importante entender la diferencia entre EC2 Instance Profile y ECS Task Role en términos de permisos, y recordar que Amazon EFS es la única opción de almacenamiento compatible con Fargate tanto en ECS como en EKS.


## 12. AWS Batch

### 12.1. Concepto y Funcionalidad

AWS Batch es un servicio que permite ejecutar trabajos por lotes (batch jobs) basados en imágenes Docker, ofreciendo una solución para procesamiento asíncrono y planificado.

#### 12.1.1. Características Principales
- Ejecuta trabajos por lotes basados en imágenes Docker
- Aprovisiona dinámicamente las instancias necesarias
- No requiere gestionar manualmente la infraestructura subyacente
- Servicio completamente serverless

#### 12.1.2. Aprovisionamiento Inteligente
- Determina automáticamente la cantidad y tipo óptimos de instancias
- Basado en el volumen de trabajos y sus requisitos específicos
- Puede utilizar instancias EC2 estándar o instancias Spot para optimización de costos
- No requiere gestionar clústeres

### 12.2. Integración y Orquestación

#### 12.2.1. Planificación de Trabajos
- Integración con CloudWatch Events para ejecutar trabajos según programación
- Orquestación de trabajos mediante AWS Step Functions

#### 12.2.2. Funcionamiento con Recursos
- Las instancias EC2 se crean dentro de tu cuenta AWS
- Los recursos son gestionados por el servicio Batch
- Acceso completo a los recursos dentro de tu cuenta

### 12.3. Diferencias con AWS Glue

| AWS Batch | AWS Glue |
|-----------|----------|
| Puede ejecutar cualquier tipo de trabajo por lotes | Enfocado específicamente en ETL |
| Soporta cualquier entorno mediante imágenes Docker | Solo soporta código Apache Spark (Scala o Python) |
| Ideal para tareas generales de procesamiento por lotes | Especializado en transformación de datos |
| Ejemplos: tareas de limpieza en buckets S3, procesamiento de archivos | Ejemplos: transformaciones de datos para análisis |
| No incluye catálogo de datos | Incluye Data Catalog integrado |

### 12.4. Casos de Uso

#### 12.4.1. Escenarios Ideales para AWS Batch
- Tareas de limpieza periódicas en recursos AWS
- Procesamiento por lotes de cualquier tipo no relacionado con ETL
- Trabajos de computación intensiva ejecutados periódicamente
- Cargas de trabajo que pueden beneficiarse de instancias Spot para reducir costos

#### 12.4.2. Consideraciones de Implementación
- Requiere imágenes Docker que encapsulen todo el entorno y dependencias
- Permite flexibilidad en lenguajes de programación y entornos
- Optimizado para procesamiento asíncrono no interactivo

---

## Resumen para el Examen: AWS Batch

AWS Batch es un servicio serverless diseñado para ejecutar trabajos por lotes basados en imágenes Docker, sin necesidad de aprovisionar o gestionar infraestructura. El servicio determina automáticamente la cantidad y tipo óptimos de instancias EC2 o Spot basándose en los requisitos y volumen de los trabajos. A diferencia de AWS Glue, que está especializado en ETL con Apache Spark, Batch es más versátil y permite ejecutar cualquier tipo de trabajo que pueda contenerse en una imagen Docker. Los trabajos pueden programarse mediante CloudWatch Events o orquestarse con Step Functions, siendo ideal para tareas de computación no interactivas como limpieza de recursos, procesamiento periódico o cualquier tarea que no sea específicamente de transformación de datos.

AWS Batch crea las instancias EC2 dentro de tu cuenta AWS, lo que proporciona acceso completo a los recursos de tu entorno, pero sin la carga de gestionar la infraestructura. Mientras Glue proporciona un catálogo de datos y está optimizado para manejar fuentes de datos estructuradas y semiestructuradas, Batch es más generalista y apropiado para cualquier carga de trabajo por lotes que pueda beneficiarse de la escalabilidad automática y la optimización de recursos que ofrece AWS.

### Consejo para el Examen
Para el examen AWS Certified Machine Learning - Associate, es fundamental entender cuándo usar AWS Batch en lugar de otros servicios como Glue o ECS. Si encuentras escenarios de procesamiento por lotes que no estén relacionados con ETL, pero requieran ejecución periódica o bajo demanda de tareas computacionales intensivas, AWS Batch será probablemente la respuesta correcta. También recuerda que Batch utiliza imágenes Docker, lo que proporciona mayor flexibilidad en términos de entornos de ejecución y lenguajes de programación en comparación con servicios más específicos como Glue.


## 13. AWS CloudFormation

### 13.1. Concepto y Propósito

AWS CloudFormation es una tecnología fundamental que permite declarar y gestionar la infraestructura AWS como código, automatizando la creación y configuración de recursos.

#### 13.1.1. Definición
- Método declarativo para definir infraestructura AWS
- Permite especificar recursos y sus configuraciones en un archivo de texto (template)
- Crea y configura automáticamente todos los recursos especificados en el orden correcto

#### 13.1.2. Ejemplo de Uso
En un template de CloudFormation se puede declarar:
- Un grupo de seguridad
- Varias instancias EC2 utilizando ese grupo de seguridad
- Un bucket S3
- Un balanceador de carga frente a las instancias

### 13.2. Beneficios de CloudFormation

#### 13.2.1. Infraestructura como Código (IaC)
- Elimina la necesidad de crear recursos manualmente
- Proporciona control y consistencia
- Facilita la revisión de cambios mediante revisión de código

#### 13.2.2. Ventajas de Costos
- Etiquetado automático de recursos dentro del mismo stack
- Facilita la estimación de costos de recursos mediante templates
- Permite estrategias de ahorro automatizadas

#### 13.2.3. Automatización de Entornos
- Posibilidad de programar eliminación de recursos en horas no laborables
- Ejemplo: eliminar automáticamente templates a las 5:00 PM y recrearlos a las 8:00 AM
- Genera ahorros significativos al no mantener recursos activos 24/7

#### 13.2.4. Productividad
- Creación y eliminación rápida de recursos
- Generación automática de diagramas para visualizar templates
- Programación declarativa que determina automáticamente el orden de creación

#### 13.2.5. Reutilización
- Aprovechamiento de templates existentes
- Amplia documentación disponible
- Soporte para casi todos los recursos AWS
- Opción de recursos personalizados para casos no soportados directamente

### 13.3. Visualización de Infraestructura

#### 13.3.1. Infrastructure Composer
- Servicio que permite visualizar templates de CloudFormation
- Muestra todos los recursos definidos en el template
- Visualiza las relaciones entre componentes
- Facilita la comprensión de arquitecturas complejas

### 13.4. Casos de Uso para el Examen

#### 13.4.1. Escenarios Ideales
- Implementación de infraestructura como código
- Replicación de arquitecturas en diferentes entornos
- Despliegue consistente en diferentes regiones
- Replicación de arquitecturas en diferentes cuentas AWS

#### 13.4.2. Ventajas Clave
- Consistencia en la implementación
- Control sobre cambios de infraestructura
- Automatización de procesos de despliegue y eliminación
- Documentación implícita de la arquitectura

---

## Resumen para el Examen: AWS CloudFormation

AWS CloudFormation es un servicio fundamental que implementa el concepto de Infraestructura como Código (IaC) en AWS, permitiendo definir recursos de forma declarativa en templates y automatizando su creación en el orden correcto. Este enfoque elimina la necesidad de configuración manual, mejora el control mediante revisión de código, facilita la estimación de costos y permite implementar estrategias de ahorro como la eliminación programada de recursos en horas no laborables.

CloudFormation ofrece importantes ventajas de productividad al permitir crear y eliminar recursos rápidamente, generar diagramas automáticos de la infraestructura, y determinar automáticamente el orden de creación de recursos. El servicio soporta prácticamente todos los recursos AWS y, para casos excepcionales, ofrece la opción de recursos personalizados.

Una herramienta complementaria, Infrastructure Composer, permite visualizar los templates de CloudFormation mostrando todos los recursos y las relaciones entre ellos, lo que facilita la comprensión de arquitecturas complejas. En contextos de examen, CloudFormation es la respuesta adecuada para escenarios que requieren implementación consistente de arquitecturas en diferentes entornos, regiones o cuentas AWS, especialmente cuando se busca control, consistencia y automatización en la gestión de infraestructura.

### Consejo para el Examen
Para el examen AWS Certified Machine Learning - Associate, recuerda que CloudFormation es la solución preferida cuando se requiere desplegar infraestructura como código, especialmente para arquitecturas de machine learning que necesitan ser replicadas en diferentes entornos (desarrollo, prueba, producción) o regiones. Presta atención a escenarios que mencionen estrategias de ahorro de costos mediante la eliminación y recreación programada de recursos, pues es una ventaja distintiva de CloudFormation. También ten presente que CloudFormation se integra con otros servicios AWS relevantes para MLOps, permitiendo desplegar toda la infraestructura necesaria para pipelines de machine learning de manera consistente y automatizada.

## 14. AWS Cloud Development Kit (CDK)

### 14.1. Concepto y Propósito

AWS Cloud Development Kit (CDK) es una evolución en la forma de definir infraestructura como código, permitiendo utilizar lenguajes de programación familiares en lugar de formatos declarativos.

#### 14.1.1. Definición
- Framework para definir infraestructura AWS usando lenguajes de programación
- Soporta lenguajes como JavaScript, TypeScript, Python, Java y .NET
- Genera plantillas de CloudFormation a partir de código programático

#### 14.1.2. Relación con CloudFormation
- CDK funciona como una capa superior a CloudFormation
- El código CDK se compila y sintetiza en plantillas CloudFormation (JSON o YAML)
- CloudFormation ejecuta la implementación real de la infraestructura

### 14.2. Componentes Principales del CDK

#### 14.2.1. Constructs (Constructos)
- Componentes de alto nivel que representan recursos o grupos de recursos
- Encapsulan configuraciones predefinidas y mejores prácticas
- Permiten crear infraestructura con menos código y mayor abstracción

#### 14.2.2. Ejemplo de Código
En TypeScript, se puede definir:
```typescript
// Creación de una VPC con 3 zonas de disponibilidad
const vpc = new ec2.Vpc(this, 'MyVpc', {
  maxAzs: 3
});

// Creación de un clúster ECS en la VPC
const cluster = new ecs.Cluster(this, 'MyCluster', {
  vpc: vpc
});

// Creación de un servicio Fargate con Application Load Balancer
const fargateService = new ecs_patterns.ApplicationLoadBalancedFargateService(this, 'MyService', {
  cluster: cluster,
  cpu: 512,
  desiredCount: 6,
  taskImageOptions: { /* opciones de imagen */ },
  memoryLimitMiB: 1024,
  publicLoadBalancer: true
});
```

### 14.3. Ventajas del CDK

#### 14.3.1. Seguridad de Tipos
- El código es verificado en tiempo de compilación
- Errores detectados antes de la implementación
- Mayor confiabilidad en la definición de infraestructura

#### 14.3.2. Flexibilidad de Programación
- Uso de características de lenguajes como funciones, clases y herencia
- Posibilidad de crear lógica compleja en la definición de infraestructura
- Reutilización de código más efectiva

#### 14.3.3. Integración Código-Infraestructura
- Permite desplegar infraestructura y código de aplicación juntos
- Ideal para funciones Lambda y contenedores Docker
- Facilita la implementación de patrones DevOps

### 14.4. Flujo de Trabajo del CDK

#### 14.4.1. Proceso de Desarrollo e Implementación
1. Escribir código en el lenguaje de programación preferido
2. Ejecutar `cdk synth` para generar la plantilla CloudFormation
3. Usar `cdk deploy` para implementar la infraestructura
4. CloudFormation procesa la plantilla y crea/actualiza los recursos

#### 14.4.2. Visualización
- El código CDK se sintetiza en una plantilla CloudFormation
- La plantilla puede visualizarse con herramientas como Infrastructure Composer
- Facilita la comprensión de la arquitectura resultante

### 14.5. Diferencias con AWS SAM

| AWS CDK | AWS SAM |
|---------|---------|
| Soporte completo de servicios AWS | Enfocado en aplicaciones serverless |
| Usa lenguajes de programación (TypeScript, Python, Java, .NET) | Usa formato declarativo (JSON/YAML) |
| Genera plantillas CloudFormation | Se basa directamente en CloudFormation |
| Ideal para infraestructuras complejas | Excelente para comenzar rápidamente con Lambda |
| Mayor flexibilidad y capacidad de abstracción | Mayor simplicidad para casos de uso específicos |

### 14.6. Integración CDK y SAM

#### 14.6.1. Uso Combinado
- Se puede usar SAM CLI para probar localmente aplicaciones CDK
- Proceso:
  1. Ejecutar `cdk synth` para generar plantilla CloudFormation
  2. Usar `sam local invoke` referenciando la plantilla generada
  3. Probar funciones Lambda localmente antes de desplegar

#### 14.6.2. Caso de Uso Práctico
Ejemplo de aplicación combinando CDK y SAM:
- Bucket S3 que recibe imágenes
- Funciones Lambda activadas por eventos S3
- Integración con Amazon Rekognition para análisis de imágenes
- Almacenamiento de resultados en DynamoDB
- Todo definido y desplegado mediante un script CDK

---

## Resumen para el Examen: AWS Cloud Development Kit (CDK)

AWS CDK representa una evolución significativa en la gestión de infraestructura como código, permitiendo definir recursos AWS mediante lenguajes de programación como TypeScript, Python, Java o .NET. A diferencia de CloudFormation tradicional, que utiliza YAML o JSON, CDK aprovecha las ventajas de los lenguajes de programación (verificación de tipos, reutilización de código, abstracción) para generar plantillas CloudFormation más robustas y con menos errores.

El CDK se basa en el concepto de "constructs" (constructos), componentes de alto nivel que encapsulan recursos AWS con configuraciones predefinidas, permitiendo crear infraestructuras complejas con menos código. Por ejemplo, con unas pocas líneas se puede definir una VPC completa, un clúster ECS y un servicio Fargate con balanceador de carga, lo que en CloudFormation tradicional requeriría cientos de líneas de YAML.

El flujo de trabajo del CDK incluye escribir código en el lenguaje preferido, usar `cdk synth` para generar la plantilla CloudFormation y `cdk deploy` para implementarla. Esta aproximación ofrece ventajas significativas como detección temprana de errores, mayor reutilización de código y la posibilidad de implementar juntos infraestructura y código de aplicación.

A diferencia de AWS SAM, que está enfocado en aplicaciones serverless y usa formato declarativo, CDK soporta todos los servicios AWS y ofrece mayor flexibilidad a través de lenguajes de programación. Sin embargo, ambas herramientas pueden complementarse: es posible usar SAM CLI para probar localmente funciones Lambda definidas con CDK, aprovechando las fortalezas de cada framework.

### Consejo para el Examen
Para el examen AWS Certified Machine Learning - Associate, es importante entender que CDK es la opción preferida cuando necesitas definir infraestructuras complejas para pipelines de ML con código programático, especialmente si ya tienes experiencia con lenguajes como Python o TypeScript. Recuerda que CDK genera plantillas CloudFormation, por lo que combina la potencia y alcance de CloudFormation con la flexibilidad de los lenguajes de programación. En escenarios de examen donde se requiera implementar tanto infraestructura como código de aplicación para machine learning (por ejemplo, funciones Lambda que procesan datos o modelos en contenedores), CDK suele ser la respuesta más adecuada por su capacidad de integrar ambos aspectos en un único framework de desarrollo.

## 15. AWS CI/CD Services: CodeDeploy, CodeBuild y CodePipeline

### 15.1. AWS CodeDeploy

#### 15.1.1. Concepto y Funcionalidad
- Servicio de automatización de despliegue de aplicaciones
- Permite actualizar aplicaciones de versión 1 a versión 2 automáticamente
- Funciona independientemente de CloudFormation o Elastic Beanstalk

#### 15.1.2. Compatibilidad de Plataformas
- **Servicio híbrido**: funciona tanto con instancias AWS como con servidores on-premises
- Compatible con:
  - Instancias EC2
  - Servidores on-premises
  - Otros servicios AWS (Lambda, ECS)

#### 15.1.3. Requisitos y Funcionamiento
- Los servidores deben aprovisionarse con anticipación
- Requiere la instalación del agente CodeDeploy en cada servidor
- Facilita la transición de entornos on-premises a AWS

#### 15.1.4. Ventajas
- Interfaz única para gestionar despliegues en diferentes entornos
- Automatización del proceso de actualización
- Facilita estrategias de migración híbridas

### 15.2. AWS CodeBuild

#### 15.2.1. Concepto y Propósito
- Servicio para compilar código en la nube
- Ejecuta procesos de compilación, pruebas y empaquetado
- Produce artefactos listos para despliegue

#### 15.2.2. Flujo de Trabajo
1. Recupera código fuente de repositorios (ej. CodeCommit)
2. Ejecuta scripts definidos por el usuario
3. Compila el código y ejecuta pruebas
4. Genera artefactos listos para despliegue

#### 15.2.3. Características Principales
- **Completamente gestionado y serverless**
- Escalabilidad continua y alta disponibilidad
- Modelo de precios de pago por uso (solo pagas por el tiempo de compilación)
- Sin servidores que administrar

#### 15.2.4. Ventajas
- Permite enfocarse solo en el código, no en la infraestructura
- Integración nativa con otros servicios AWS
- Automatización del proceso de compilación

### 15.3. AWS CodePipeline

#### 15.3.1. Concepto y Función
- Servicio de orquestación para canalización de CI/CD (Integración Continua/Entrega Continua)
- Conecta e integra diferentes etapas del proceso de desarrollo y despliegue
- Automatiza el flujo desde el repositorio hasta la producción

#### 15.3.2. Flujo de Trabajo Típico
1. Obtener código (CodeCommit, GitHub)
2. Compilar código (CodeBuild)
3. Desplegar aplicación (CodeDeploy)
4. Aprovisionar infraestructura según necesidad (CloudFormation, Elastic Beanstalk)

#### 15.3.3. Características Principales
- **Completamente gestionado**
- Alta compatibilidad con servicios AWS y terceros:
  - CodeCommit, CodeBuild, CodeDeploy
  - Elastic Beanstalk, CloudFormation
  - GitHub
  - Plugins personalizados y servicios de terceros

#### 15.3.4. Beneficios
- Entrega rápida y actualizaciones continuas
- Automatización completa del proceso de despliegue
- Flexibilidad para diseñar flujos de trabajo personalizados

### 15.4. Integración entre Servicios CI/CD

#### 15.4.1. Ejemplo de Flujo Completo
- Desarrollador envía código a CodeCommit
- CodePipeline detecta cambios y desencadena el flujo
- CodeBuild compila, prueba y empaqueta el código
- CodeDeploy implementa la aplicación en servidores
- Todo orquestado y automatizado por CodePipeline

#### 15.4.2. Personalización del Flujo
- Posibilidad de añadir etapas de aprobación manual
- Integración con servicios de notificación
- Capacidad para ramificar flujos según condiciones

---

## Resumen para el Examen: AWS CI/CD Services

AWS ofrece un conjunto integrado de servicios para implementar CI/CD (Integración Continua/Entrega Continua) que automatizan y optimizan el proceso de desarrollo y despliegue de aplicaciones. 

**CodeDeploy** es un servicio híbrido que automatiza el despliegue de aplicaciones tanto en instancias EC2 como en servidores on-premises, facilitando la actualización de aplicaciones y la transición entre entornos. Requiere la instalación previa del agente CodeDeploy en los servidores y su aprovisionamiento anticipado.

**CodeBuild** es un servicio serverless para compilar código en la nube, ejecutando procesos de compilación, pruebas y empaquetado para producir artefactos listos para despliegue. Funciona con un modelo de pago por uso, eliminando la necesidad de gestionar servidores de compilación y permitiendo a los desarrolladores enfocarse exclusivamente en el código.

**CodePipeline** actúa como orquestador, conectando e integrando los diferentes servicios en un flujo automatizado de CI/CD. Permite diseñar canalizaciones personalizadas que automatizan todo el proceso desde la recepción del código hasta su despliegue en producción, integrándose con múltiples servicios AWS y de terceros.

Estos tres servicios, junto con otros como CodeCommit y servicios complementarios como Elastic Beanstalk o CloudFormation, conforman la suite de CI/CD de AWS, permitiendo implementar flujos de trabajo completamente automatizados que aceleran el desarrollo, mejoran la calidad y facilitan las actualizaciones continuas de aplicaciones.

### Consejo para el Examen
Para el examen AWS Certified Machine Learning - Associate, es crucial entender que en escenarios de implementación de modelos de ML, estos servicios CI/CD juegan un papel fundamental. Recuerda que **CodeDeploy** es el único servicio híbrido que funciona tanto con instancias AWS como con servidores on-premises, lo que lo hace ideal para estrategias de migración gradual. **CodeBuild** resulta especialmente útil para la compilación y prueba de código de ML, incluyendo notebooks y scripts de procesamiento de datos. **CodePipeline** es la respuesta correcta siempre que veas preguntas sobre orquestación o automatización del flujo completo de CI/CD. Esta combinación de servicios permite implementar MLOps efectivos, automatizando el ciclo de vida completo desde el desarrollo hasta el despliegue de modelos de machine learning.

## 16. Git y Flujos de Trabajo en Control de Versiones

### 16.1. Fundamentos de Git

Git es un sistema de control de versiones distribuido ampliamente adoptado que permite a múltiples desarrolladores trabajar simultáneamente en proyectos sin interferir entre sí.

#### 16.1.1. Arquitectura Básica
- **Repositorios remotos**: Almacenados en servidores (GitHub, GitLab, etc.)
- **Repositorios locales**: Copias completas del repositorio en máquinas locales
- **Ramas (branches)**: Líneas de desarrollo independientes para trabajar en paralelo

#### 16.1.2. Flujo de Trabajo Estándar
1. Clonar o inicializar un repositorio
2. Crear/cambiar a una rama para trabajo específico
3. Realizar cambios en archivos
4. Añadir cambios al área de preparación (staging)
5. Confirmar (commit) los cambios
6. Enviar (push) los cambios al repositorio remoto

### 16.2. Comandos Esenciales de Git

#### 16.2.1. Configuración Inicial
- `git init`: Inicializa un nuevo repositorio
- `git config`: Configura usuario, email y otras opciones

#### 16.2.2. Comandos Básicos
- `git clone [URL]`: Clona un repositorio remoto
- `git status`: Muestra el estado de los archivos modificados
- `git add [file]` o `git add *`: Añade cambios al área de preparación
- `git commit -m "mensaje"`: Confirma los cambios preparados
- `git log`: Muestra el historial de commits

#### 16.2.3. Gestión de Ramas
- `git branch`: Lista todas las ramas locales
- `git branch [nombre]`: Crea una nueva rama
- `git checkout [rama]`: Cambia a la rama especificada
- `git checkout -b [rama]`: Crea y cambia a una nueva rama
- `git merge [rama]`: Fusiona otra rama con la rama actual
- `git branch -d [rama]`: Elimina una rama

#### 16.2.4. Trabajo con Repositorios Remotos
- `git remote add [nombre] [URL]`: Añade un repositorio remoto
- `git remote`: Lista los repositorios remotos configurados
- `git push [remoto] [rama]`: Envía los cambios locales al repositorio remoto
- `git pull [remoto] [rama]`: Obtiene cambios del repositorio remoto

#### 16.2.5. Deshacer Cambios
- `git reset`: Restablece el área de preparación sin afectar los archivos
- `git reset --hard`: Restablece tanto el área de preparación como los archivos
- `git revert [commit]`: Revierte los cambios de un commit específico

#### 16.2.6. Comandos Avanzados
- `git stash` y `git stash pop`: Almacena temporalmente cambios no confirmados
- `git rebase`: Reaplica cambios de una rama a otra
- `git cherry-pick`: Aplica cambios de un commit específico
- `git blame`: Muestra quién modificó cada línea de un archivo
- `git diff`: Muestra diferencias entre commits

### 16.3. Flujos de Trabajo en Git

#### 16.3.1. Git Flow
Flujo de trabajo tradicional con múltiples ramas especializadas:

- **master/main**: Código en producción
- **develop**: Cambios desde la última versión
- **feature branches**: Desarrollo de funcionalidades específicas
- **release branches**: Preparación para lanzamientos
- **hotfix branches**: Correcciones urgentes para producción

**Ventajas**:
- Permite trabajo paralelo en múltiples funcionalidades
- Separación clara entre desarrollo y producción
- Gestión estructurada de versiones

**Desventajas**:
- Mayor complejidad de gestión
- Requiere sincronización entre múltiples ramas
- Puede ralentizar el proceso de lanzamiento

#### 16.3.2. GitHub Flow
Flujo de trabajo simplificado con solo dos tipos de ramas:

- **master/main**: Código principal siempre desplegable
- **feature branches**: Ramas temporales para funcionalidades o fixes

**Ventajas**:
- Simplicidad extrema
- Facilita la integración continua
- Menos conflictos de fusión

**Desventajas**:
- Requiere capacidad para desplegar rápidamente
- Necesita pruebas automatizadas robustas
- Puede no ser adecuado para todos los tipos de proyectos

### 16.4. Comparativa de Flujos de Trabajo

| Aspecto | Git Flow | GitHub Flow |
|---------|----------|-------------|
| Complejidad | Alta | Baja |
| Cantidad de ramas | Múltiples tipos especializados | Solo main y feature branches |
| Velocidad de despliegue | Más lenta, planificada | Rápida, continua |
| Adecuado para | Proyectos grandes, releases programadas | Desarrollo ágil, deployments frecuentes |
| Requisitos | Gestión de ramas disciplinada | Automatización de pruebas y despliegues |

---

## Resumen para el Examen: Git y Flujos de Trabajo en Control de Versiones

Git es un sistema de control de versiones distribuido que permite a múltiples desarrolladores trabajar en paralelo mediante repositorios locales y remotos. Los comandos esenciales que debes conocer incluyen configuración (`git init`, `git config`), operaciones básicas (`git clone`, `git add`, `git commit`, `git status`), gestión de ramas (`git branch`, `git checkout`, `git merge`), interacción con repositorios remotos (`git push`, `git pull`), y técnicas para deshacer cambios (`git reset`, `git revert`).

Existen principalmente dos enfoques de flujo de trabajo en Git: Git Flow y GitHub Flow. Git Flow es más estructurado, con múltiples tipos de ramas (master, develop, feature, release, hotfix) que permiten trabajo paralelo pero aumentan la complejidad. GitHub Flow es más simple, utilizando solo ramas main y feature, pero requiere la capacidad de desplegar frecuentemente y contar con pruebas automatizadas sólidas.

La elección entre estos flujos depende de las necesidades específicas del proyecto: Git Flow es adecuado para proyectos grandes con liberaciones programadas, mientras que GitHub Flow es ideal para desarrollo ágil con despliegues continuos. Ambos enfoques tienen sus ventajas y desventajas, y la decisión debe basarse en factores como la velocidad de desarrollo necesaria, la estabilidad requerida y la madurez de los procesos de CI/CD.

### Consejo para el Examen
Para el examen AWS Certified Machine Learning - Associate, recuerda que Git es fundamental en los flujos de trabajo de MLOps. Cuando veas preguntas sobre gestión de código para proyectos de ML, considera que los comandos básicos (`clone`, `add`, `commit`, `push`, `pull`) serán relevantes para cualquier escenario, mientras que la elección entre Git Flow y GitHub Flow dependerá del contexto: para modelos que requieren pruebas exhaustivas antes de su despliegue en producción, Git Flow proporciona más control y aislamiento; para entornos de experimentación rápida y desarrollo iterativo de modelos, GitHub Flow ofrece mayor agilidad. También ten presente la integración de Git con servicios AWS como CodeCommit, que proporciona repositorios Git privados totalmente gestionados e integrados con otros servicios de AWS.

## 17. Amazon EventBridge

### 17.1. Concepto y Evolución

Amazon EventBridge es un servicio de bus de eventos sin servidor que facilita la conexión de aplicaciones con datos de diversas fuentes, anteriormente conocido como CloudWatch Events.

#### 17.1.1. Transición de Nombre
- Anteriormente conocido como CloudWatch Events
- Mantiene las funcionalidades básicas pero con capacidades expandidas
- Importante recordar ambos nombres para el examen

### 17.2. Funcionalidades Principales

#### 17.2.1. Programación de Tareas
- Permite configurar cron jobs en la nube
- Programación de scripts mediante expresiones cron
- Ejemplo: "Cada hora activar una función Lambda"
- Soporte para patrones como "Cada lunes a las 8:00 AM" o "El primer lunes del mes"

#### 17.2.2. Reacción a Patrones de Eventos
- Reglas que responden a eventos específicos en servicios AWS
- Ejemplo: Detección de inicio de sesión del usuario root de IAM
- Envío de notificaciones basadas en eventos de seguridad

#### 17.2.3. Estructura de Eventos
- Genera documentos JSON que contienen detalles del evento
- Incluye información como ID de instancia, hora, IP, etc.
- Permite filtrado por atributos específicos

### 17.3. Arquitectura de EventBridge

#### 17.3.1. Fuentes de Eventos
Múltiples servicios AWS pueden enviar eventos:
- Instancias EC2 (inicio, detención, terminación)
- CodeBuild (fallos de compilación)
- S3 (carga de objetos)
- Trusted Advisor (nuevos hallazgos de seguridad)
- CloudTrail (interceptación de cualquier llamada API)
- Programaciones y patrones cron

#### 17.3.2. Tipos de Event Bus (Buses de Eventos)
EventBridge ofrece tres tipos de buses de eventos:

1. **Default Event Bus (Bus de Eventos Predeterminado)**
   - Recibe eventos de servicios AWS
   - Integración automática con servicios AWS

2. **Partner Event Bus (Bus de Eventos de Socios)**
   - Recibe eventos de servicios SaaS integrados
   - Socios como Zendesk, Datadog, Auth0, etc.
   - Permite reaccionar a cambios fuera del ecosistema AWS

3. **Custom Event Bus (Bus de Eventos Personalizado)**
   - Creado por el usuario para aplicaciones propias
   - Permite que aplicaciones propias envíen eventos
   - Facilita arquitecturas orientadas a eventos

#### 17.3.3. Destinos de Eventos
Los eventos pueden desencadenar múltiples tipos de acciones:
- Invocación de funciones Lambda
- Programación de trabajos en AWS Batch
- Lanzamiento de tareas ECS
- Envío de mensajes a SQS, SNS o Kinesis Data Stream
- Inicio de flujos de trabajo en Step Functions
- Activación de pipelines en CodePipeline
- Ejecución de compilaciones en CodeBuild
- Inicialización de automatizaciones de SSM
- Acciones específicas de EC2 (inicio, detención, reinicio)

### 17.4. Características Avanzadas

#### 17.4.1. Acceso Entre Cuentas
- Permite acceder a buses de eventos entre diferentes cuentas AWS
- Utiliza políticas basadas en recursos para control de acceso
- Facilita arquitecturas centralizadas de eventos

#### 17.4.2. Archivado y Reproducción de Eventos
- Capacidad para archivar eventos (todos o un subconjunto filtrado)
- Opciones de retención indefinida o por período específico
- Posibilidad de reproducir eventos archivados
- Útil para depuración, resolución de problemas y pruebas de funciones corregidas

#### 17.4.3. Schema Registry (Registro de Esquemas)
- Analiza eventos en el bus y deduce su esquema
- Genera código para aplicaciones basado en los esquemas
- Permite saber de antemano cómo está estructurada la información
- Soporte para versionado de esquemas

#### 17.4.4. Políticas Basadas en Recursos
- Gestión de permisos para buses de eventos específicos
- Control de permiso para eventos de otras regiones o cuentas
- Permite crear un bus de eventos central para toda una organización AWS
- Facilita la agregación de eventos de múltiples cuentas

### 17.5. Caso de Uso: Eventos Centralizados

#### 17.5.1. Arquitectura
- Bus de eventos central en una cuenta específica
- Política basada en recursos que permite a otras cuentas enviar eventos
- Otras cuentas utilizan operación PutEvents para enviar eventos al bus central
- Consolidación de la gestión de eventos para toda la organización

---

## Resumen para el Examen: Amazon EventBridge

Amazon EventBridge (anteriormente CloudWatch Events) es un servicio de bus de eventos sin servidor que permite conectar aplicaciones con datos provenientes de diversas fuentes. Ofrece dos funcionalidades principales: programación de tareas mediante expresiones cron y reacción a patrones de eventos específicos generados por servicios AWS.

EventBridge utiliza tres tipos de buses de eventos: el bus predeterminado para servicios AWS, buses de socios para servicios SaaS integrados (como Zendesk o Datadog), y buses personalizados para aplicaciones propias. Los eventos, estructurados como documentos JSON con información detallada, pueden desencadenar una amplia variedad de acciones, desde invocar funciones Lambda hasta iniciar flujos de trabajo en Step Functions o enviar mensajes a SQS o SNS.

Entre sus características avanzadas destacan: acceso entre cuentas mediante políticas basadas en recursos, archivado y reproducción de eventos para depuración, Schema Registry para inferir y versionar esquemas de eventos, y la capacidad de centralizar eventos de toda una organización AWS en un bus central. Estas capacidades hacen de EventBridge una herramienta fundamental para crear arquitecturas orientadas a eventos, facilitando la integración entre servicios AWS y aplicaciones externas.

### Consejo para el Examen
Para el examen AWS Certified Machine Learning - Associate, recuerda que EventBridge es esencial para automatizar flujos de trabajo de MLOps. Cuando veas escenarios que requieran automatización basada en eventos (como iniciar entrenamientos de modelos cuando se cargan nuevos datos a S3, o desplegar automáticamente modelos actualizados), EventBridge será probablemente la solución. Presta especial atención a la diferencia entre los tres tipos de buses de eventos y su capacidad para archivar y reproducir eventos, lo cual es crucial para depuración en pipelines de ML. También ten presente que EventBridge permite integrar servicios de partners SaaS, lo que puede ser relevante para escenarios que combinen herramientas de ML externas con servicios AWS.


## 18. AWS Step Functions

### 18.1. Concepto y Propósito

AWS Step Functions es un servicio de orquestación que permite diseñar y ejecutar flujos de trabajo complejos en AWS. Esencialmente, funciona como un director de orquesta que coordina la ejecución de diferentes servicios AWS en un orden específico, creando así aplicaciones complejas a partir de componentes más simples.

A diferencia de ejecutar scripts o programas que se encargan de toda la lógica, Step Functions separa la lógica de coordinación (qué ocurre y cuándo) de la lógica de implementación (cómo se realiza cada tarea). Esto permite crear flujos de trabajo visualmente comprensibles y fáciles de modificar sin tocar el código subyacente.

Por ejemplo, un científico de datos podría usar Step Functions para crear un pipeline de machine learning que automáticamente extraiga datos, los prepare, entrene un modelo, lo evalúe y finalmente lo despliegue, todo sin necesidad de supervisión humana constante.

### 18.2. Terminología y Estructura

En Step Functions, cada flujo de trabajo completo se denomina "máquina de estados" (state machine). Este nombre proviene de la teoría de computación, donde una máquina de estados es un modelo matemático que describe un sistema que puede estar en exactamente un estado de un conjunto finito de estados en cualquier momento dado.

Cada paso individual dentro de este flujo se llama "estado" (state). Los estados pueden realizar tareas, tomar decisiones, esperar, procesar datos en paralelo, o simplemente pasar información al siguiente paso. Es importante entender que un estado no necesariamente corresponde a una acción—puede ser una decisión, una espera, o incluso simplemente un punto de transición.

### 18.3. Tipos de Estados Explicados

#### Estado Task (Tarea)
El estado Task es el caballo de batalla de Step Functions. Cuando necesitas que algo suceda—una computación, un procesamiento, una transformación—utilizas un estado Task. Este estado puede invocar:

- Una función Lambda para ejecutar código personalizado
- Un trabajo de SageMaker para entrenar o implementar un modelo
- Un trabajo de Glue para transformar datos
- Prácticamente cualquier acción en casi cualquier servicio AWS

Por ejemplo, podrías tener un estado Task que invoca una función Lambda para limpiar un conjunto de datos, seguido de otro estado Task que inicia un trabajo de entrenamiento en SageMaker.

#### Estado Choice (Elección)
El estado Choice introduce ramificaciones en tu flujo de trabajo basadas en condiciones. Funciona similar a una declaración "if-then-else" en programación. Por ejemplo:

- Si el tamaño del dataset supera los 10GB, utiliza un método de procesamiento distribuido
- Si la precisión del modelo es inferior al 85%, vuelve a entrenar con diferentes parámetros
- Si se detectan datos personales, aplica un proceso de anonimización adicional

Esta capacidad permite crear flujos de trabajo inteligentes que responden dinámicamente a las condiciones reales de los datos o resultados intermedios.

#### Estado Wait (Espera)
A veces, necesitas pausa entre acciones. El estado Wait simplemente hace que el flujo de trabajo se detenga durante un tiempo específico o hasta un momento determinado antes de continuar con el siguiente paso. Esto puede ser útil cuando:

- Necesitas esperar a que se completen procesos asíncronos externos
- Quieres programar acciones para que ocurran en momentos específicos
- Deseas implementar reintentos con retrasos exponenciales para operaciones propensas a fallos

#### Estado Parallel (Paralelo)
El estado Parallel divide el flujo de trabajo en múltiples ramas que se ejecutan simultáneamente. Imagina que tienes que procesar imágenes y texto de un mismo dataset—puedes crear un estado Parallel con una rama que procesa las imágenes mientras otra procesa el texto simultáneamente.

Cada rama contiene su propia secuencia de estados, y el flujo de trabajo no continúa hasta que todas las ramas se completan (o fallan). Esta capacidad permite optimizar el tiempo de ejecución realizando tareas independientes en paralelo.

#### Estado Map (Mapeo)
El estado Map es particularmente poderoso para procesamiento de datos. A diferencia del estado Parallel que crea un número fijo de ramas, Map toma una colección de elementos (como registros en un archivo JSON, objetos en un bucket S3, o filas en una tabla) y ejecuta el mismo conjunto de pasos para cada elemento de forma paralela.

Por ejemplo, si tienes 1000 imágenes para procesar, un estado Map aplicará la misma secuencia de procesamiento a cada imagen simultáneamente (dentro de los límites de concurrencia configurados).

La diferencia clave con Parallel es que Map trabaja sobre una colección de datos dinámicamente, mientras que Parallel crea un número fijo y predeterminado de ramas.

#### Estados de Control (Pass, Succeed, Fail)
Estos estados son más simples pero igualmente útiles:

- **Pass**: Simplemente pasa datos sin realizar ninguna acción. Es útil para transformar la entrada o para depuración.
- **Succeed**: Termina la ejecución como exitosa. Puedes usarlo para finalizar ramas específicas en flujos complejos.
- **Fail**: Termina la ejecución como fallida, opcionalmente con un mensaje de error personalizado.

### 18.4. Casos de Uso Prácticos en Machine Learning

#### Entrenamiento Automatizado de Modelos
Un flujo de trabajo típico podría ser:

1. Un estado Task invoca Lambda para preparar y validar datos de entrenamiento
2. Otro estado Task inicia un trabajo de entrenamiento en SageMaker
3. Un estado Choice evalúa la calidad del modelo resultante
4. Si la calidad es suficiente, un estado Task implementa el modelo
5. Si no, se puede volver a un estado anterior con parámetros ajustados

Este flujo automatiza completamente el ciclo de entrenamiento, validación e implementación, liberando tiempo valioso para los científicos de datos.

#### Ajuste de Hiperparámetros
El ajuste de hiperparámetros se beneficia especialmente de Step Functions:

1. Un estado Task genera o prepara el dataset de entrenamiento
2. Otro estado Task inicia un trabajo de ajuste de hiperparámetros en SageMaker
3. El sistema prueba automáticamente diferentes configuraciones
4. Un estado Task extrae y guarda el modelo con los mejores parámetros
5. Finalmente, otro estado Task puede implementar este modelo óptimo

Este proceso, que podría tomar días de trabajo manual, se ejecuta automáticamente mientras el equipo se concentra en otras tareas.

#### Procesamiento Masivo de Datos
Para procesamiento masivo y transformación de datos:

1. Un estado Map toma un conjunto grande de archivos desde S3
2. Para cada archivo, se ejecuta un proceso de limpieza y transformación
3. Los resultados se agregan y se almacenan para análisis posterior

La capacidad de procesar miles de archivos en paralelo reduce drásticamente el tiempo de procesamiento.

### 18.5. Ventajas de Step Functions en Pipelines de Datos

#### Visualización y Monitoreo Intuitivos
A diferencia de codificar todo el flujo en scripts, Step Functions proporciona una visualización gráfica clara de todo el proceso. Puedes ver exactamente qué paso está ejecutándose, cuáles se han completado y si alguno ha fallado. Esta visibilidad es invaluable para diagnosticar problemas en pipelines complejos.

#### Manejo de Errores Robusto
Step Functions ofrece capacidades integradas para manejar errores:
- Reintentos configurables con backoff exponencial
- Estados de captura para manejar fallos específicos
- Alternativas para rutas fallidas

Esto significa que no necesitas programar toda esta lógica en tu código, lo que resulta en aplicaciones más limpias y resilientes.

#### Integración Nativa con Servicios AWS
Step Functions se integra directamente con prácticamente todos los servicios AWS relevantes para machine learning y procesamiento de datos. Esto elimina la necesidad de escribir código personalizado para gestionar estas integraciones y reduce significativamente el tiempo de desarrollo.

#### Ejecuciones de Larga Duración
Con soporte para flujos de trabajo que pueden ejecutarse hasta por un año, Step Functions puede manejar procesos muy largos como entrenamientos extensos, procesamiento de datasets masivos o flujos de trabajo que incluyen interacción humana intermitente.

---

## Resumen para el Examen: AWS Step Functions

AWS Step Functions es un servicio de orquestación poderoso que permite crear flujos de trabajo complejos y automatizados para aplicaciones de machine learning y procesamiento de datos. Utilizando el concepto de máquinas de estados, Step Functions coordina la ejecución de diferentes servicios AWS, proporcionando visualización, manejo de errores y registro de auditoría integrados.

El servicio ofrece varios tipos de estados que definen el comportamiento del flujo de trabajo: Task para ejecutar acciones, Choice para implementar lógica condicional, Wait para introducir pausas, Parallel para crear ramas de ejecución simultáneas, y Map para procesar colecciones de datos en paralelo. También incluye estados de control como Pass, Succeed y Fail para facilitar la depuración y finalización de flujos.

Un aspecto crucial para el examen es entender la diferencia entre los estados Parallel y Map. Mientras que Parallel crea un número fijo de ramas independientes, Map opera dinámicamente sobre cada elemento de un conjunto de datos, aplicando los mismos pasos a cada elemento en paralelo, similar a operaciones distribuidas en sistemas como Apache Spark. Esta distinción es fundamental para implementar procesamiento eficiente de datos a gran escala.

Step Functions resulta especialmente valioso para casos de uso de machine learning como entrenamiento automatizado de modelos, ajuste de hiperparámetros y procesamiento masivo de datos. Su capacidad para integrar servicios como Lambda, SageMaker, Glue y muchos otros lo convierte en el componente central para construir pipelines de ML robustos y completamente automatizados que requieren mínima intervención humana.

### Consejo para el Examen
Para el examen AWS Certified Machine Learning - Associate, enfócate en comprender los diferentes tipos de estados y cuándo aplicar cada uno. Presta especial atención a la distinción entre estados Parallel (ramas fijas) y Map (procesamiento dinámico de colecciones). Recuerda que Step Functions puede orquestar flujos completos de machine learning, desde la preparación de datos hasta el despliegue de modelos, integrándose con prácticamente cualquier servicio AWS relevante. Los escenarios que requieran automatización, manejo de errores robusto y procesamiento paralelo probablemente apunten a Step Functions como la solución óptima.

## 19. Amazon Managed Workflows for Apache Airflow (MWAA)

### 19.1. Introducción a Apache Airflow y MWAA

Amazon Managed Workflows for Apache Airflow (MWAA) es un servicio gestionado que proporciona un entorno hospedado para Apache Airflow, una plataforma popular de código abierto para la gestión de flujos de trabajo.

#### 19.1.1. Concepto de Apache Airflow
- Herramienta de gestión de flujos de trabajo orientada a procesamiento por lotes
- Permite desarrollar, programar y monitorizar flujos de trabajo
- Controla el flujo de datos en pipelines de ingeniería de datos
- Definición de flujos mediante código Python (no mediante interfaces gráficas)

#### 19.1.2. Características de MWAA
- Servicio completamente gestionado para Apache Airflow
- Elimina la necesidad de instalar y mantener la infraestructura
- Facilita la ejecución de Airflow sin preocuparse por la gestión del servicio

### 19.2. Directed Acyclic Graphs (DAGs)

El concepto fundamental en Apache Airflow es el DAG (Directed Acyclic Graph o Grafo Acíclico Dirigido).

#### 19.2.1. Definición y Propósito
- Representa una secuencia de tareas con dependencias específicas
- "Directed" porque el flujo va en una dirección
- "Acyclic" porque no hay ciclos (una tarea no puede depender de sí misma)
- Definido mediante código Python

#### 19.2.2. Ejemplo de DAG
```python
# Creación de tareas simples
hello = BashOperator(task_id='hello', bash_command='echo hello')
airflow = PythonOperator(task_id='airflow', python_callable=print_airflow)

# Definición de dependencias entre tareas
hello >> airflow  # hello debe ejecutarse antes que airflow
```

#### 19.2.3. Visualización
- Airflow proporciona una interfaz web para visualizar los DAGs
- Muestra gráficamente las dependencias entre tareas
- Ofrece información sobre ejecuciones, programación y estado

### 19.3. Implementación de MWAA en AWS

#### 19.3.1. Flujo de Trabajo
1. Los DAGs (código Python) se cargan en un bucket S3
2. MWAA carga estos DAGs desde S3
3. El servicio orquesta y programa la ejecución según lo definido

#### 19.3.2. Empaquetado de Código
- Posibilidad de comprimir (zip) el código Python junto con plugins y dependencias
- Mantiene todo el código necesario autocontenido en S3

### 19.4. Arquitectura y Consideraciones Técnicas

#### 19.4.1. Infraestructura
- MWAA se ejecuta dentro de una VPC
- Recomendación de despliegue en al menos dos zonas de disponibilidad para redundancia
- Soporta endpoints públicos y privados gestionados mediante IAM
- Los endpoints públicos permiten acceso a la interfaz web desde fuera de la VPC

#### 19.4.2. Escalado Automático
- Utiliza trabajadores (workers) de Airflow bajo el capó
- Escala automáticamente estos trabajadores según la demanda
- Permite definir límites para controlar costos

#### 19.4.3. Implementación Técnica
- Programadores (schedulers) y trabajadores implementados en contenedores AWS Fargate
- Estructura de dos VPCs:
  - VPC del cliente: contiene schedulers y workers que se comunican con servicios AWS
  - VPC de servicio: aloja la base de datos de metadatos y el servidor web de Airflow
  - Comunicación entre VPCs mediante VPC endpoints

### 19.5. Integración con Servicios AWS

MWAA se integra con numerosos servicios AWS a través de integraciones de código abierto, incluyendo:
- Servicios de análisis: Amazon Athena, Amazon Redshift
- Servicios de procesamiento: AWS Batch, EMR, Fargate, ECS, Lambda
- Servicios de datos: DynamoDB, Kinesis, S3
- Servicios de orquestación: AWS Glue
- Servicios de mensajería: SQS, SNS
- Servicios de IA/ML: SageMaker
- Servicios de monitoreo: CloudWatch
- Servicios de seguridad: AWS Secrets Manager

### 19.6. Casos de Uso

#### 19.6.1. Flujos de Trabajo Complejos
- Ideal para orquestación de procesos ETL complejos
- Útil cuando se requiere lógica personalizada mediante Python

#### 19.6.2. Preparación de Datos para ML
- Coordinación de pipelines de preparación de datos para machine learning
- Integración con servicios como SageMaker para entrenamiento automatizado

#### 19.6.3. Migración desde Airflow On-Premises
- Facilita la transición de implementaciones existentes de Airflow a la nube
- Mantiene compatibilidad con DAGs existentes

---

## Resumen para el Examen: Amazon Managed Workflows for Apache Airflow (MWAA)

Amazon Managed Workflows for Apache Airflow (MWAA) proporciona un entorno gestionado para Apache Airflow, una plataforma popular para la orquestación de flujos de trabajo basados en procesamiento por lotes. A diferencia de otros servicios de AWS que ofrecen interfaces visuales para definir flujos, Airflow requiere escribir código Python para crear Directed Acyclic Graphs (DAGs) que definen la secuencia y dependencias de tareas.

MWAA simplifica el uso de Airflow eliminando la necesidad de gestionar la infraestructura subyacente. El flujo de implementación consiste en cargar los DAGs (como código Python) en un bucket S3, desde donde MWAA los recoge, orquesta y ejecuta según lo programado. Los códigos pueden empaquetarse junto con plugins y dependencias para mantener todo autocontenido.

Arquitectónicamente, MWAA opera en una VPC con despliegue recomendado en múltiples zonas de disponibilidad. Utiliza contenedores Fargate para los programadores y trabajadores de Airflow, y escala automáticamente según la demanda. El servicio se estructura con dos VPCs: una para los componentes operativos (schedulers y workers) y otra para los componentes de infraestructura (base de datos de metadatos y servidor web).

MWAA se integra con una amplia gama de servicios AWS, desde herramientas de análisis como Athena y Redshift, hasta servicios de procesamiento como Lambda y EMR, y plataformas de IA/ML como SageMaker. Es particularmente adecuado para flujos de trabajo ETL complejos que requieren lógica personalizada en Python, preparación de datos para machine learning, y para organizaciones que migran desde implementaciones on-premises de Airflow.

### Consejo para el Examen
Para el examen AWS Certified Machine Learning - Associate, recuerda que MWAA es la opción adecuada cuando se necesita definir flujos de trabajo complejos mediante código Python, especialmente si ya se tiene experiencia con Apache Airflow. A diferencia de servicios como Step Functions (que ofrece una definición más declarativa) o Glue (que se enfoca específicamente en ETL), MWAA proporciona mayor flexibilidad programática pero requiere escribir código Python para definir los DAGs. Ten presente que MWAA carga los DAGs desde S3 y utiliza contenedores Fargate para ejecutarlos, por lo que es una solución completamente gestionada pero con la potencia y flexibilidad de Apache Airflow de código abierto.


## 20. AWS Lake Formation

### 20.1. Introducción a AWS Lake Formation

AWS Lake Formation es un servicio diseñado para simplificar la creación, gestión y protección de lagos de datos en AWS, construido sobre la base de AWS Glue.

#### 20.1.1. Evolución y Propósito
- Anunciado en 2018 con refinamientos significativos en 2020
- Facilita la configuración de lagos de datos seguros en días
- Construido sobre AWS Glue para aprovechar sus capacidades ETL

#### 20.1.2. Funcionalidades Principales
- Carga de datos desde fuentes externas
- Monitorización de flujos de datos
- Gestión de particiones
- Administración de cifrado y claves
- Configuración de transformaciones de datos
- Control de acceso centralizado
- Auditoría de acceso

### 20.2. Arquitectura y Componentes

#### 20.2.1. Estructura Conceptual
- Lake Formation se integra con un lago de datos S3
- Admite múltiples fuentes de datos:
  - Datos existentes en S3
  - Bases de datos relacionales
  - Fuentes NoSQL
  - Sistemas on-premises

#### 20.2.2. Componentes Principales
- Crawlers para descubrimiento de datos
- Trabajos ETL para transformación
- Catálogo de datos para metadatos
- Sistema de seguridad y control de acceso
- Integración con servicios de análisis

#### 20.2.3. Servicios Integrados
- Amazon Athena para consultas SQL
- Amazon Redshift y Redshift Spectrum
- Amazon EMR para procesamiento
- AWS Glue para trabajos ETL

### 20.3. Proceso de Implementación

#### 20.3.1. Pasos para Crear un Lago de Datos
1. Crear un usuario IAM con rol de analista de datos
2. Establecer conexión Glue a las fuentes de datos
3. Crear bucket S3 para el lago
4. Registrar la ruta S3 en Lake Formation
5. Configurar permisos iniciales
6. Crear bases de datos en el catálogo de datos
7. Configurar blueprints y workflows para transformaciones
8. Ejecutar workflows
9. Conceder permisos de consulta a usuarios

### 20.4. Gestión de Permisos y Seguridad

#### 20.4.1. Permisos entre Cuentas (Cross-Account)
- Configuración del destinatario como administrador del lago de datos
- Uso de AWS Resource Access Manager (RAM) para gestionar acceso externo
- Configuración de permisos IAM para acceso entre cuentas

#### 20.4.2. Control de Acceso Granular
- Control de acceso a nivel de columna mediante la interfaz de usuario
- Posibilidad de asignar permisos a:
  - Usuarios o roles IAM
  - Usuarios y grupos SAML
  - Cuentas AWS externas
- Uso de etiquetas de política vinculadas a bases de datos, tablas o columnas
- Configuración explícita de permisos (select, insert, delete, etc.)

#### 20.4.3. Data Filters (Filtros de Datos)
- Mecanismo para implementar seguridad a nivel de fila, columna o celda
- Configuración mediante interfaz de consola o API (create-data-cells-filter)
- Opciones de configuración:
  - Acceso a nivel de columna: incluir/excluir columnas específicas
  - Filtros a nivel de fila: expresiones para filtrar filas
  - Seguridad a nivel de celda: combinación de ambos

### 20.5. Características Avanzadas

#### 20.5.1. Governed Tables (Tablas Gobernadas)
- Soporte para transacciones ACID en múltiples tablas
- Permite acceso, actualización y eliminación a nivel de fila de forma segura
- Basado en un nuevo tipo de tabla S3
- No se puede cambiar el tipo de tabla una vez creada
- Compatible con datos de streaming (como Kinesis)
- Integración con Athena para consultas con garantías ACID

#### 20.5.2. Compactación Automática
- Optimización automática del almacenamiento
- Reduce la sobrecarga que se acumula con el tiempo en tablas con soporte ACID
- Mejora el rendimiento de acceso a largo plazo

#### 20.5.3. Limitaciones a Considerar
- No soporta manifiestos en consultas desde Athena o Redshift
- Requiere permisos IAM en claves KMS para catálogos de datos cifrados
- Necesita permisos IAM específicos para crear blueprints y workflows

### 20.6. Modelo de Precios

- Lake Formation en sí no tiene costo adicional
- Se paga por los servicios subyacentes utilizados:
  - AWS Glue
  - Amazon S3
  - Amazon EMR
  - Amazon Athena
  - Amazon Redshift
- Las características avanzadas (tablas gobernadas, seguridad granular) se facturan según uso

---

## Resumen para el Examen: AWS Lake Formation

AWS Lake Formation es un servicio que simplifica la creación y gestión de lagos de datos seguros, construido sobre AWS Glue. Facilita la carga de datos desde diversas fuentes (S3, bases de datos relacionales, NoSQL, sistemas on-premises), gestiona transformaciones, configura particiones, administra cifrado y establece un sistema centralizado de control de acceso y auditoría.

El proceso de implementación implica crear usuarios IAM, establecer conexiones a fuentes de datos, configurar un bucket S3, registrarlo en Lake Formation, crear bases de datos en el catálogo, definir flujos de trabajo mediante blueprints y conceder permisos adecuados. Lake Formation se integra con servicios de análisis como Athena, Redshift y EMR para explotar los datos almacenados.

Una característica destacada es su sistema de gestión de permisos, que permite control granular a nivel de columna, fila y celda mediante data filters. Estos filtros se pueden configurar visualmente o mediante API, ofreciendo flexibilidad para implementar políticas de seguridad detalladas.

Las características avanzadas incluyen tablas gobernadas con soporte para transacciones ACID, compatibilidad con datos de streaming, compactación automática para optimizar el rendimiento y seguridad a nivel de celda. Lake Formation en sí no tiene costo adicional, pero se paga por los servicios AWS subyacentes utilizados y por las características avanzadas según el uso.

### Consejo para el Examen
Para el examen AWS Certified Machine Learning - Associate, enfócate en entender que Lake Formation es la solución ideal cuando necesitas crear un lago de datos seguro con control de acceso granular para proyectos de ML. Recuerda las limitaciones clave: no soporta manifiestos en consultas desde Athena/Redshift, requiere permisos IAM específicos para claves KMS en catálogos cifrados, y necesita configuración especial para acceso entre cuentas (utilizando AWS RAM). Presta especial atención a los data filters para implementar seguridad a nivel de columna, fila y celda, ya que este tema aparece frecuentemente en el examen. También ten presente que las tablas gobernadas ofrecen soporte ACID, lo que facilita la integridad de datos en entornos colaborativos de ML donde múltiples usuarios pueden modificar datos simultáneamente.