# Machine Learning Operations (MLOps) with AWS

## 1. Deployment Guardrails y Shadow Tests

### 1.1 Introducción a los Features de Seguridad en Despliegue

AWS SageMaker ofrece características más recientes para salvaguardas de despliegue que aseguran que cuando despliegas un nuevo modelo:

- No ocurran problemas graves
- Si ocurren, los detectes rápidamente

### 1.2 Deployment Guardrails

#### Características principales:
- Disponible para endpoints de inferencia **asíncronos** y **en tiempo real**
- Control de cambio de tráfico hacia un nuevo modelo
- Evita despliegues "esperando lo mejor"

#### Blue-Green Deployment

| Fleet | Descripción |
|--------|-------------|
| **Blue Fleet** | Modelo anterior en producción |
| **Green Fleet** | Nuevo modelo que se va a desplegar |

#### Modos de Despliegue

1. **All at Once**
   - Cambia todo el tráfico al Green Fleet inmediatamente
   - Monitorea automáticamente
   - Termina el Blue Fleet solo si todo está bien

2. **Canary Mode**
   - Cambia solo una porción pequeña del tráfico al Green Fleet
   - Monitorea por un período
   - Si el "canary" está bien, cambia todo el tráfico
   - Luego termina el Blue Fleet

3. **Linear Mode**
   - Cambia el tráfico en pasos linealmente espaciados
   - Permite múltiples pasos
   - Útil para monitorear escalado
   - Permite control de rampa gradual de tráfico

#### Features adicionales:
- **Rollbacks automáticos**: Si algo sale mal, regresa automáticamente al Blue Fleet

### 1.3 Shadow Tests

#### Funcionalidad:
- Compara el rendimiento de un **shadow variant** con producción
- El shadow variant recibe un porcentaje del tráfico

#### Características:
- Monitoreo manual en la consola de SageMaker
- El usuario decide cuándo promover a producción
- Similar a deployment guardrails pero con control manual

## 2. SageMaker's Inner Details y Production Variants

### 2.1 SageMaker e Interacción con Docker

#### Fundamentos:
- Todo modelo ML y despliegue en SageMaker debe estar en un **Docker container** registrado en ECR
- Flexibilidad total en el runtime o lenguaje usado

#### Tipos de contenedores:
- Modelos pre-construidos: DL, scikit-learn, Spark ML
- Código personalizado construido sobre: TensorFlow, MXNet, Chainer, PyTorch
- Docker images custom desde cero
- Extensiones de imágenes pre-construidas

#### Distribución de TensorFlow:
- **Horovod**: Distribución de TensorFlow en múltiples máquinas
- **Parameter servers**: Alternativa para distribución

### 2.2 Estructura de Contenedores Docker

#### Training Container:
```
/opt/ml/
├── input/          # Configuración, hyperparameters, datos
├── code/           # Scripts de entrenamiento (Python)
├── output/         # Salida, errores
└── model/          # Para deployment
```

#### Deployment Container:
- `/opt/ml/model/` - Archivos de inferencia

### 2.3 Arquitectura Completa

| Componente | Descripción |
|------------|-------------|
| **Docker File** | Define cómo construir la imagen |
| **Docker Image** | Construido desde Dockerfile |
| **ECR** | Repositorio para imágenes |
| **S3** | Almacenamiento de datos/modelos |

### 2.4 Flujo de Despliegue

1. Imágenes Docker en ECR (training/inference)
2. Training jobs acceden datos desde S3
3. Modelos entrenados se guardan en S3
4. Inference code accede modelos desde S3
5. Despliegue en fleet de servidores
6. Endpoints para acceso runtime

### 2.5 Ejemplo de Dockerfile

```dockerfile
FROM tensorflow
RUN pip install sagemaker-containers
COPY train.py /opt/ml/code/train.py
ENV SAGEMAKER_PROGRAM train.py
```

### 2.6 Variables de Entorno Importantes

| Variable | Propósito |
|----------|-----------|
| `SAGEMAKER_PROGRAM` | Script entrypoint |
| `TRAINING_MODULE` | Módulo TensorFlow/MXNet |
| `SERVICE_MODULE` | Módulo de servicio |
| `SM_CHANNELS` | Canales train/test/validation |
| `SM_HPS` | Hiperparámetros |

### 2.7 Production Variants

#### Concepto:
- Prueba de múltiples modelos en tráfico real
- Ideal para sistemas de recomendación
- Distribución controlada de tráfico

#### Variant Weights:
- Controla distribución de tráfico
- Ejemplo: 90% modelo antiguo, 10% modelo nuevo
- Aumento gradual basado en confianza
- Rollback rápido en caso de problemas

#### Flujo de A/B Testing:
1. Despliegue de nuevo modelo con variant weight bajo (10%)
2. Monitoreo de performance
3. Incremento gradual de peso
4. Eventual reemplazo del modelo anterior

### Resumen para el examen

- **Docker containers** son obligatorios en SageMaker, registrados en ECR
- Estructura de directorios define ubicación de código, datos y modelos
- **Production Variants** permiten A/B testing con control de tráfico
- **Variant weights** permiten distribución gradual y rollback rápido
- Variables de entorno clave: `SAGEMAKER_PROGRAM`, `SM_CHANNELS`, `SM_HPS`

## 3. SageMaker On the Edge: SageMaker Neo y IoT Greengrass

### 3.1 Conceptos Fundamentales del Edge

#### Definición:
- Despliegue de modelos entrenados a **edge devices**
- Ejemplos: computadoras embebidas, autos autónomos, cámaras inteligentes (AWS DeepLens)

### 3.2 SageMaker Neo

#### Función principal:
- Compila código de inferencia para edge devices
- **"Train once, run anywhere"**

#### Arquitecturas soportadas:
- ARM processors
- Intel processors  
- Nvidia processors

#### Ventajas:
- Reduce latencia (crítico para aplicaciones como autos autónomos)
- Procesamiento local sin internet

#### Componentes:
1. **Compiler**: Recompila código a bytecode específico
2. **Runtime library**: Ejecuta el código compilado en edge devices

#### Frameworks compatibles:
- TensorFlow
- MXNet
- PyTorch
- ONNX
- XGBoost

#### Métodos de despliegue:
- HTTP endpoints (C5, M5, M4, P3, P2 instances)  
- Edge devices vía IoT Greengrass

### 3.3 Integración AWS IoT Greengrass

#### Función:
- Mecanismo para desplegar código Neo a edge devices
- Habilita inferencia local usando modelos entrenados en la nube

#### Características:
- Usa Lambda functions para aplicaciones de inferencia
- Combina Neo + Greengrass + Lambda para soluciones end-to-end

### 3.4 Flujo de Trabajo Completo

1. Entrena modelo en SageMaker (instancias de training)
2. Compila modelo con Neo para arquitectura específica
3. Despliega a edge devices usando IoT Greengrass
4. Ejecuta inferencia local sin conexión a internet

### Resumen para el examen:
- **Neo** = compilador para optimizar modelos para edge devices específicos
- **Greengrass** = mecanismo de despliegue a edge devices
- Combinación crucial: Neo + Greengrass + Lambda
- Ventaja principal: inferencia local de baja latencia sin internet

## 4. SageMaker Resource Management: Instance Types y Spot Training

### 4.1 Selección de Tipos de Instancia

#### Deep Learning:
- **Training**: P3 o G4 (GPU instances)
- **Inference**: C5 (Compute) o M5 (General purpose)

#### Non-Deep Learning:
- **Algoritmos no GPU-acelerables**: M5 (General purpose)

### 4.2 Consideraciones de Costo-Eficiencia

| Escenario | Recomendación |
|-----------|---------------|
| **Algoritmos DL** | Una máquina multi-GPU > Múltiples instancias CPU |
| **Algoritmos non-DL** | M5 o C5 instancias |

### 4.3 Managed Spot Training

#### Beneficios:
- Ahorro hasta **90%** vs On Demand instances
- Ideal para trabajos de training largos y costosos

#### Requisitos:
- Uso obligatorio de **checkpoints a S3**
- Capacidad de recuperación ante interrupciones

#### Trade-offs:
| Ventaja | Desventaja |
|---------|------------|
| **Mayor ahorro** | Mayor complejidad de setup |
| - | Aumento del tiempo de training |
| - | Espera por disponibilidad de instancias |

### 4.4 Best Practices

1. Usar GPU solo cuando sea necesario (DL)
2. Implementar checkpoints para spot instances
3. Considerar trade-off entre costo y tiempo
4. Evaluar eficiencia de múltiples GPUs vs múltiples instancias

### Resumen para el examen:
- **Deep Learning**: P3/G4 para training, C5/M5 para inference
- **Spot Training**: Puede ahorrar hasta 90% con checkpoints
- Considerar costo total: instancias multi-GPU pueden ser más eficientes
- Siempre implementar checkpoints con spot instances

## 5. SageMaker Resource Management: Automatic Scaling

### 5.1 Fundamentos de Automatic Scaling

#### Concepto:
- Sistema de escalado automático para modelos de inferencia en producción
- Ajusta dinámicamente el número de instancias según la demanda
- Trabaja en conjunto con **CloudWatch** para monitorear rendimiento

#### Beneficios:
- Ahorro significativo de costos
- Mejor gestión de recursos
- Mayor resiliencia del servicio

### 5.2 Componentes de una Política de Escalado

| Componente | Descripción |
|------------|-------------|
| **Target metrics** | Métricas objetivo que activan el escalado |
| **Min/max capacity** | Límites de capacidad mínima y máxima |
| **Cool down periods** | Períodos de estabilización entre eventos de escalado |

### 5.3 Funcionamiento con Production Variants

#### Características:
- Funciona a nivel de **production variant** individual
- Ajusta cada variante según su tráfico específico
- Se integra con los cambios de distribución de tráfico en tiempo real

#### Ejemplo:
Si tienes dos variantes A y B con distribución 80/20:
- Variante A escalará más agresivamente
- Variante B mantendrá capacidad proporcional a su tráfico

### 5.4 Best Practices para Automatic Scaling

1. **Load testing obligatorio**:
   - Probar la configuración en ambiente de prueba
   - Verificar que escale según lo esperado
   - Evitar problemas de capacidad insuficiente o excesiva

2. **Configuración gradual**:
   - Comenzar con políticas conservadoras
   - Ajustar basado en datos de rendimiento real
   - Monitorear costos y efectividad continuamente

### 5.5 Resiliencia con Availability Zones

#### Comportamiento por defecto:
- SageMaker intenta distribuir instancias en múltiples **Availability Zones** (AZs)
- Requiere al menos 2 instancias para funcionar efectivamente

#### Recomendaciones:
1. **Múltiples instancias**:
   - Desplegar más de una instancia por endpoint
   - Permite distribución entre AZs
   - Aumenta resiliencia ante fallos

2. **Configuración de VPC**:
   - Si usas VPCs personalizadas, configura al menos 2 subnets
   - Cada subnet debe estar en una AZ diferente
   - Asegura continuidad del servicio ante fallos catastróficos

### Resumen para el examen:

- **Automatic scaling** se integra con CloudWatch para monitorear y ajustar capacidad
- Funciona a nivel de **production variant** individual, no solo del modelo completo
- Es **crítico realizar load testing** antes de usar en producción
- Para máxima resiliencia:
  - Usar múltiples instancias
  - Configurar VPCs con subnets en diferentes AZs
  - SageMaker distribuirá automáticamente las instancias para mayor disponibilidad

Este sistema permite optimizar costos mientras se mantiene el rendimiento y la disponibilidad del servicio, especialmente importante para cargas de trabajo variables.

## 6. SageMaker: Deploying Models for Inference

### 6.1 Fundamentos del Despliegue de Modelos

#### Importancia:
- Un modelo solo aporta valor cuando está en producción realizando predicciones
- SageMaker ofrece múltiples opciones para desplegar modelos entrenados
- El despliegue habilita la **inference** (predicciones con nuevos datos)

### 6.2 Métodos de Despliegue

#### 6.2.1 SageMaker Jumpstart
- **Nivel de complejidad**: Bajo
- **Características**:
  - Solución "one-click" para modelos pre-entrenados
  - Proporciona templates y notebooks pre-configurados
  - Ideal para casos de uso comunes ya resueltos
  - No requiere conocimientos avanzados de despliegue

#### 6.2.2 SageMaker Python SDK Model Builder
- **Nivel de complejidad**: Medio
- **Características**:
  - Mayor control sobre la configuración del despliegue
  - Configurable mediante código Python
  - Permite personalización de parámetros de despliegue
  - Equilibrio entre facilidad y control

#### 6.2.3 AWS CloudFormation
- **Nivel de complejidad**: Alto
- **Características**:
  - Control robusto y completo del despliegue
  - Despliegues consistentes y repetibles
  - Integración con sistemas CI/CD
  - Recurso específico: **AWS::SageMaker::Model**
  - Ideal para entornos de producción empresariales

### 6.3 Tipos de Inference

#### 6.3.1 Real-Time Inference
- **Casos de uso**:
  - Cargas de trabajo interactivas
  - Requisitos de latencia muy bajos
  - Predicciones individuales inmediatas
- **Ejemplo práctico**: Detección de fraude en tiempo real durante transacciones

#### 6.3.2 SageMaker Serverless Inference
- **Ventajas**:
  - Eliminación de gestión de infraestructura
  - Ahorro de costos durante períodos de inactividad
  - Escalado automático según demanda
- **Consideraciones**:
  - Ideal para tráfico irregular con períodos de inactividad
  - Requiere tolerancia a "cold starts"
  - Menos adecuado para requisitos estrictos de latencia
  - Poco beneficioso para tráfico constante

#### 6.3.3 Asynchronous Inference
- **Casos de uso**:
  - Payloads de gran tamaño (hasta 1GB)
  - Tiempos de procesamiento prolongados
  - Aplicaciones que no necesitan respuesta inmediata
- **Funcionamiento**:
  - Envío rápido de datos
  - Procesamiento en background
  - Entrega de resultados cuando estén listos
  - Latencia "near real-time" para la recepción de datos

### 6.4 Optimización de Recursos y Rendimiento

#### 6.4.1 Auto Scaling para Inference
- Ajuste dinámico de recursos computacionales
- Basado en patrones de tráfico
- Punto intermedio entre serverless y gestión manual
- Evita intervenciones manuales para manejar picos de tráfico

#### 6.4.2 SageMaker Neo para Optimización
- Optimiza automáticamente modelos para entornos específicos
- Especialmente útil para **AWS Inferentia chips**
- Mejora significativa del rendimiento de inferencia
- Herramienta clave para maximizar velocidad y eficiencia

### Resumen para el examen:

- **Opciones de despliegue**: Jumpstart (simple), Python SDK (control medio), CloudFormation (control total)
- **Tipos de inference**:
  - **Real-time**: Baja latencia, predicciones individuales
  - **Serverless**: Ideal para tráfico irregular, ahorro en períodos de inactividad
  - **Asynchronous**: Para datos grandes o procesamiento extenso
- **Optimización**:
  - **Auto Scaling**: Ajusta recursos según tráfico
  - **SageMaker Neo**: Optimiza modelos para hardware específico

Al elegir método de despliegue, considerar:
1. Complejidad de implementación
2. Tamaño de datos a procesar
3. Requisitos de latencia
4. Patrones de tráfico
5. Necesidades de optimización

## 7. SageMaker Serverless Inference and Inference Recommender

### 7.1 SageMaker Serverless Inference

#### 7.1.1 Concepto y Fundamentos
- Introducido en 2022
- Parte de la tendencia hacia soluciones serverless en AWS
- Aprovisiona y escala automáticamente la capacidad de inferencia
- **Eliminación completa** de la gestión de infraestructura

#### 7.1.2 Funcionamiento
- Usuario especifica:
  - Container
  - Requisitos de memoria
  - Requisitos de concurrencia
- AWS gestiona automáticamente la infraestructura subyacente
- Capacidad escala hasta cero cuando no hay tráfico

#### 7.1.3 Casos de Uso Ideales
- Tráfico infrecuente
- Patrones de tráfico impredecibles
- Optimización de costos
- Modelo de facturación "pay-as-you-go"

#### 7.1.4 Monitoreo con CloudWatch
| Métrica | Descripción |
|---------|-------------|
| **Model setup time** | Tiempo necesario para desplegar nuevos modelos |
| **Invocations** | Número total de solicitudes de inferencia |
| **Invocation errors** | Solicitudes que generaron errores |
| **Memory utilization** | Uso de memoria a lo largo del tiempo |

#### 7.1.5 Consideraciones
- Precisión en la especificación de requisitos de memoria y concurrencia
- Posible impacto de cold starts
- Rendimiento óptimo para cargas variables

### 7.2 Amazon SageMaker Inference Recommender

#### 7.2.1 Propósito
- Herramienta para identificar configuraciones óptimas de inferencia
- Automatiza la prueba de carga y el ajuste de modelos
- **No es** un sistema de recomendación para usuarios, sino para infraestructura

#### 7.2.2 Proceso de Funcionamiento
1. Registro del modelo en el **Model Registry** de SageMaker
2. Benchmarking automático de diferentes configuraciones
3. Recolección y visualización de métricas
4. Recomendaciones basadas en resultados

#### 7.2.3 Modos de Operación

| Modo | Descripción | Duración | Uso Recomendado |
|------|-------------|----------|-----------------|
| **Instance Recommendations** | Pruebas de carga en tipos de instancias recomendadas | ~45 minutos | Evaluación rápida de opciones |
| **Endpoint Recommendations** | Pruebas personalizadas con parámetros específicos | ~2 horas | Optimización para SLAs específicos |

#### 7.2.4 Métricas Evaluadas
- Costo por hora
- Costo por inferencia
- Latencia
- Rendimiento (throughput)

#### 7.2.5 Personalización en Endpoint Recommendations
- Selección explícita de tipos de instancia a probar
- Definición de patrones de tráfico esperados
- Especificación de requisitos de latencia y throughput
- Resultados adaptados a SLAs específicos

### 7.3 Comparativa de Estrategias de Despliegue

| Estrategia | Nivel de Control | Gestión de Infraestructura | Caso de Uso Ideal |
|------------|------------------|---------------------------|-------------------|
| **Inference Recommender + Configuración Manual** | Alto | Manual con recomendaciones | SLAs estrictos, optimización precisa |
| **Auto Scaling** | Medio | Semi-automatizada | Tráfico predecible con variaciones |
| **Serverless Inference** | Bajo | Completamente automatizada | Tráfico impredecible, optimización de costos |

### Resumen para el examen:

- **Serverless Inference**:
  - Escalado automático completo (hasta cero)
  - Sin gestión de infraestructura
  - Pago por uso exacto
  - Ideal para tráfico variable o impredecible
  - Monitoreo vía CloudWatch (model setup time, invocations, memory)

- **Inference Recommender**:
  - **No** es un sistema de recomendación para usuarios finales
  - Herramienta para identificar configuraciones óptimas de instancias
  - Dos modos:
    - Instance Recommendations (rápido, ~45 min)
    - Endpoint Recommendations (personalizado, ~2 horas)
  - Ayuda a optimizar para costo vs. latencia vs. throughput

- **Estrategia de elección** depende de:
  - Necesidad de control manual
  - Previsibilidad del tráfico
  - Requisitos de optimización
  - Consideraciones de costo

Al elegir entre estas opciones, considerar el balance entre control, automatización y optimización según las necesidades específicas del proyecto y los patrones de tráfico esperados.

## 8. SageMaker Inference Pipelines

### 8.1 Concepto y Arquitectura

#### 8.1.1 Definición
- Secuencia lineal de múltiples contenedores Docker para inferencia
- Permite combinar diferentes algoritmos y etapas de procesamiento
- Alternativa avanzada al despliegue de un único contenedor

#### 8.1.2 Estructura
- Cadena de 2 a 15 contenedores Docker
- Procesamiento secuencial: salida de un contenedor = entrada del siguiente
- Configurable como un solo endpoint de inferencia

#### 8.1.3 Componentes Típicos
1. **Pre-procesamiento**: Transformación y preparación de datos
2. **Predicción**: Modelo principal de inferencia
3. **Post-procesamiento**: Formateo de resultados y lógica adicional

### 8.2 Tipos de Contenedores Compatibles

#### 8.2.1 Orígenes de Contenedores
- Algoritmos integrados (built-in) de SageMaker
- Algoritmos personalizados en contenedores Docker
- Contenedores de **Spark ML**
- Contenedores de **scikit-learn**

#### 8.2.2 Integración con Spark ML
- Compatible con Spark ML desde AWS Glue o Amazon EMR
- Modelos Spark ML serializados en formato **MLeap**
- Permite incorporar transformaciones y modelos de Spark en el flujo de inferencia

### 8.3 Modos de Operación

#### 8.3.1 Real-Time Inference
- Despliegue como endpoint web para predicciones en tiempo real
- Procesamiento secuencial de cada solicitud a través de la cadena de contenedores
- Respuesta sincrónica al cliente

#### 8.3.2 Batch Transform
- Procesamiento por lotes de grandes conjuntos de datos
- Ideal para predicciones masivas no interactivas
- Misma cadena de contenedores pero en modo asíncrono

### 8.4 Implementación y Gestión

#### 8.4.1 Configuración
```python
# Ejemplo conceptual de configuración
pipeline_model = PipelineModel(
    name="my-inference-pipeline",
    models=[preprocessing_model, prediction_model, postprocessing_model]
)
```

#### 8.4.2 Despliegue
- Se despliega como un único endpoint de SageMaker
- Internamente gestiona el flujo entre contenedores
- Cliente interactúa solo con un punto de entrada y salida

#### 8.4.3 Monitoreo
- CloudWatch permite observar métricas de cada contenedor
- Facilita la identificación de cuellos de botella en el pipeline

### 8.5 Casos de Uso

#### 8.5.1 Procesamiento de Datos Complejos
- Transformación de datos antes de la predicción
- Normalización, codificación, etc.

#### 8.5.2 Enriquecimiento de Resultados
- Post-procesamiento de predicciones con datos adicionales
- Formateo específico para sistemas consumidores

#### 8.5.3 Modelos en Cascada
- Modelos que dependen de salidas de modelos anteriores
- Flujos de decisión condicionales

### Resumen para el examen:

- **Inference Pipelines** permiten encadenar 2-15 contenedores Docker en secuencia lineal
- Compatibles con algoritmos built-in, contenedores personalizados, Spark ML y scikit-learn
- Modelos Spark ML se serializan en formato **MLeap** para uso en pipelines
- Funcionan tanto para **inferencia en tiempo real** como para **batch transform**
- Proporcionan una forma estructurada de combinar:
  - Pre-procesamiento de datos
  - Predicción con modelos entrenados
  - Post-procesamiento de resultados
- Se despliegan y gestionan como un único endpoint de SageMaker
- Ofrecen mayor flexibilidad y modularidad en comparación con despliegues de contenedor único

Este enfoque permite diseñar flujos de inferencia más sofisticados y mantenibles, separando claramente las distintas etapas del proceso de predicción mientras se mantiene la simplicidad en la interfaz externa.

## 10. Model Monitor Data Capture

### 10.1 Concepto y Funcionalidad Básica

#### 10.1.1 Definición
- Componente de SageMaker Model Monitor
- Sistema de registro (logging) para endpoints de inferencia
- Captura tanto entradas como salidas de las predicciones

#### 10.1.2 Proceso Principal
- Registra automáticamente todas las solicitudes de inferencia
- Captura respuestas generadas por el modelo
- Almacena los datos en formato JSON en Amazon S3
- Preserva el historial completo de actividad del endpoint

### 10.2 Utilidad y Casos de Uso

#### 10.2.1 Mejora Continua del Modelo
- Fuente de datos para reentrenamiento iterativo
- Facilita aprendizaje de patrones nuevos en datos reales
- Cierra el bucle de retroalimentación para la mejora continua

#### 10.2.2 Depuración y Troubleshooting
- Registro histórico para investigar problemas
- Permite recrear escenarios específicos
- Facilita análisis post-mortem de fallos

#### 10.2.3 Monitoreo y Comparación
- Compara métricas actuales con baseline establecido
- Detecta desviaciones en patrones de datos
- Proporciona visibilidad de cambios graduales

### 10.3 Compatibilidad y Modos de Operación

#### 10.3.1 Tipos de Inferencia Soportados
- **Inferencia en tiempo real**: Captura de predicciones interactivas
- **Inferencia por lotes (batch)**: Registro de trabajos de transformación masiva

#### 10.3.2 Interfaces de Programación
| Interfaz | Descripción |
|----------|-------------|
| **Boto3** | API de bajo nivel para Python |
| **SageMaker Python SDK** | Interfaz de alto nivel para Python |

### 10.4 Configuración y Seguridad

#### 10.4.1 Habilitación del Data Capture
```python
# Ejemplo conceptual
data_capture_config = DataCaptureConfig(
    enable_capture=True,
    sampling_percentage=100,
    destination_s3_uri='s3://bucket-name/prefix/'
)
```

#### 10.4.2 Opciones de Seguridad
- Encriptación opcional de datos capturados
- Integración con AWS KMS para gestión de claves
- Políticas de acceso y retención configurables

#### 10.4.3 Opciones de Muestreo
- Configuración de porcentaje de muestreo
- Captura selectiva para reducir volumen de datos
- Balance entre exhaustividad y eficiencia de almacenamiento

### 10.5 Flujo de Trabajo con Data Capture

#### 10.5.1 Activación y Configuración
1. Definir configuración de data capture
2. Especificar destino en S3
3. Configurar frecuencia de muestreo
4. Aplicar configuración al endpoint

#### 10.5.2 Procesamiento Post-Captura
1. Almacenamiento automático de datos en S3
2. Análisis periódico con trabajos de monitoreo
3. Comparación con baseline establecido
4. Generación de alertas en caso de desviaciones

### 10.6 Integración con el Ecosistema de SageMaker

#### 10.6.1 Model Monitor
- Proporciona datos para todas las funciones de Model Monitor
- Alimenta análisis de data drift, model quality y bias

#### 10.6.2 Pipelines de MLOps
- Componente esencial en flujos de trabajo automatizados
- Facilita la implementación de bucles de retroalimentación
- Habilita pipelines de CI/CD para modelos ML

### Resumen para el examen:

- **Model Monitor Data Capture** registra automáticamente todos los inputs y outputs de inferencia
- Los datos se almacenan en formato **JSON** en **Amazon S3**
- Principales usos:
  - Reentrenamiento y mejora continua del modelo
  - Depuración y análisis de problemas
  - Monitoreo de desviaciones respecto a baseline
- Compatible con inferencia en **tiempo real** y **batch**
- Implementable mediante **Boto3** o **SageMaker Python SDK**
- Incluye opciones de **encriptación** para datos sensibles
- Configurable con diferentes porcentajes de **muestreo**
- Componente fundamental para cerrar el ciclo de retroalimentación en sistemas ML

Este mecanismo proporciona una capa de observabilidad completa para modelos en producción, permitiendo no solo la detección de problemas sino también la mejora continua a través del aprendizaje de datos reales del entorno de producción.

## 11. MLOps with SageMaker, Kubernetes, SageMaker Projects, and SageMaker Pipelines

### 11.1 Introducción a MLOps

#### 11.1.1 Concepto y Evolución
- **MLOps**: Machine Learning Operations - gestión de workflows completos de ML
- Campo que evolucionó significativamente después de la primera versión del AWS ML Certification
- Enfoque en la automatización del ciclo de vida completo de los modelos ML
- Soluciona problemas de integración, despliegue, monitoreo y actualización

### 11.2 Integración entre SageMaker y Kubernetes

#### 11.2.1 Amazon SageMaker Operators for Kubernetes
- Permite ejecutar trabajos de SageMaker dentro del ecosistema Kubernetes
- Instala operadores SageMaker en **Amazon EKS**
- Habilita el uso de la API de Kubernetes para gestionar trabajos SageMaker
- Facilita el uso de herramientas como **kubectl** para interactuar con SageMaker

#### 11.2.2 Arquitectura de Integración
- Operadores SageMaker se ejecutan junto al kubelet en nodos EC2
- Actúan como controladores de aplicaciones para SageMaker dentro de Kubernetes
- Permiten ejecutar tareas SageMaker sin salir del entorno Kubernetes

### 11.3 Componentes para Kubeflow Pipelines

#### 11.3.1 Definición y Propósito
- **Kubeflow**: Solución MLOps basada en Kubernetes
- Integración entre SageMaker y los pipelines de Kubeflow
- Facilita la transición gradual a la nube para sistemas on-premises

#### 11.3.2 Componentes Disponibles
| Componente | Funcionalidad |
|------------|---------------|
| **Processing** | Contenedores para procesamiento de datos (ej. Spark) |
| **HPO** | Hyperparameter tuning y optimización |
| **Training** | Entrenamiento de modelos en SageMaker |
| **Inference** | Hosting y despliegue de modelos |

#### 11.3.3 Casos de Uso
- Workflows híbridos (on-premises + nube)
- Migración gradual a SageMaker
- Procesamiento local de datos sensibles con entrenamiento en la nube
- Integración con infraestructuras existentes

### 11.4 SageMaker Projects

#### 11.4.1 Concepto
- Solución nativa de MLOps dentro de SageMaker Studio
- Implementación de CI/CD (Integración Continua/Despliegue Continuo) para ML
- Alternativa a Kubernetes/Kubeflow para proyectos "all-in" con SageMaker

#### 11.4.2 Componentes
- Repositorios de código integrados
- Workflows automatizados
- Gestión de versiones de modelos
- Mecanismos de despliegue

#### 11.4.3 Flujo de Trabajo
- Construcción de imágenes
- Preparación de datos
- Ingeniería de características
- Entrenamiento de modelos
- Evaluación
- Despliegue
- Monitoreo y actualización

### 11.5 SageMaker Pipelines

#### 11.5.1 Definición
- Sistema para definir workflows dentro de SageMaker
- Componente central dentro de SageMaker Projects
- Automatización de pasos secuenciales en el proceso ML

#### 11.5.2 Etapas Típicas
1. **Processing**: Preparación y transformación de datos
2. **Training**: Entrenamiento del modelo
3. **Evaluation**: Validación de resultados
4. **Registration**: Registro en el Model Registry
5. **Deployment**: Implementación en endpoints

### 11.6 Ejemplo de MLOps Completo con SageMaker Projects

#### 11.6.1 Flujo de Integración
1. **Código**: Commit de cambios en el modelo
2. **Eventos**: Notificación mediante Amazon EventBridge
3. **CI**: AWS CodePipeline inicia proceso de integración
4. **Build**: AWS CodeBuild construye y ejecuta SageMaker Pipeline
5. **Pipeline**: Procesa datos, entrena, evalúa y registra modelo
6. **Eventos**: Notificación de modelo registrado
7. **CD**: Segunda CodePipeline para despliegue
8. **Staging**: Despliegue a entorno de pruebas
9. **Testing**: Pruebas automatizadas
10. **Aprobación**: Revisión manual
11. **Production**: Despliegue a producción

#### 11.6.2 Servicios AWS Involucrados
- **EventBridge**: Orquestación de eventos
- **CodePipeline**: Gestión de flujos CI/CD
- **CodeBuild**: Ejecución de construcciones
- **CloudFormation**: Infraestructura como código
- **S3**: Almacenamiento de artefactos
- **SageMaker Hosting**: Despliegue de endpoints

### Resumen para el examen:

- **MLOps** es el enfoque para gestionar el ciclo de vida completo de modelos ML
- Para integración con **Kubernetes** existen:
  - **SageMaker Operators for Kubernetes**: Ejecuta trabajos SageMaker desde kubectl
  - **Components for Kubeflow Pipelines**: Integra SageMaker en workflows Kubeflow
- Para soluciones nativas de SageMaker:
  - **SageMaker Projects**: Sistema MLOps completo con CI/CD
  - **SageMaker Pipelines**: Define secuencias de pasos en workflows ML
- Casos de uso clave:
  - Integración con infraestructuras existentes (Kubernetes/Kubeflow)
  - Workflows híbridos on-premises/nube
  - Automatización completa del ciclo de vida ML
  - Despliegues en múltiples entornos (staging/production)

Este ecosistema permite implementar soluciones MLOps completas, ya sea integradas con sistemas existentes o completamente basadas en SageMaker, abarcando todo el ciclo desde el desarrollo hasta la operación continua de modelos ML.

## 12. Contenedores en AWS: Docker, ECS, ECR y EKS

### 12.1 Fundamentos de Docker

#### 12.1.1 ¿Qué es Docker?
- Plataforma de desarrollo de software para desplegar aplicaciones
- Tecnología de contenedores que empaqueta aplicaciones de forma estandarizada
- Permite que las aplicaciones se ejecuten de forma consistente en cualquier entorno
- Elimina problemas de compatibilidad entre sistemas operativos

#### 12.1.2 Ventajas de Docker
- Comportamiento predecible independiente del entorno
- Menor mantenimiento y facilidad de despliegue
- Compatible con cualquier lenguaje, sistema operativo y tecnología
- Permite compartir recursos con el host (más eficiente que VMs)

#### 12.1.3 Casos de Uso
- Arquitecturas de microservicios
- Migración de aplicaciones on-premises a la nube
- Cualquier escenario que requiera contenedores

#### 12.1.4 Arquitectura Docker vs. Virtual Machines
| Docker | Máquinas Virtuales |
|--------|-------------------|
| Comparte recursos con el host | Recursos aislados |
| Múltiples contenedores por servidor | Una VM por sistema operativo |
| Usa Docker Daemon | Usa Hypervisor |
| Ligero y eficiente | Mayor overhead |
| Menor aislamiento pero mayor densidad | Mayor aislamiento pero menor densidad |

#### 12.1.5 Proceso de Trabajo con Docker
1. Escribir **Dockerfile** definiendo la imagen
2. **Build**: Construir la imagen Docker
3. **Push**: Enviar la imagen a un repositorio
4. **Pull**: Descargar la imagen desde el repositorio
5. **Run**: Ejecutar la imagen como contenedor

### 12.2 Amazon ECR (Elastic Container Registry)

#### 12.2.1 Concepto y Funcionalidad
- Servicio para almacenar y gestionar imágenes Docker en AWS
- Alternativa a Docker Hub pero integrada con servicios AWS
- Opciones disponibles:
  - **Repositorio privado**: Solo para cuentas específicas
  - **Repositorio público**: Disponible en ECR Public Gallery

#### 12.2.2 Características Principales
- Integración completa con Amazon ECS y EKS
- Almacenamiento basado en Amazon S3
- Control de acceso mediante IAM
- Escaneo de vulnerabilidades en imágenes
- Versionado y etiquetado de imágenes
- Gestión de ciclo de vida

#### 12.2.3 Flujo de Funcionamiento
1. Las imágenes Docker se almacenan en ECR
2. Las instancias EC2 en un clúster ECS obtienen permisos mediante roles IAM
3. Las instancias EC2 extraen (pull) las imágenes de ECR
4. Los contenedores se inician en las instancias EC2

### 12.3 Amazon ECS (Elastic Container Service)

#### 12.3.1 Tipos de Lanzamiento (Launch Types)

##### EC2 Launch Type
- Clúster ECS compuesto por instancias EC2
- Requiere aprovisionar y mantener la infraestructura
- Cada instancia debe ejecutar el **ECS Agent**
- El Agent registra la instancia en el clúster ECS
- AWS inicia y detiene contenedores automáticamente
- Los contenedores se colocan automáticamente en las instancias

##### Fargate Launch Type
- Modelo serverless para ejecutar contenedores
- No requiere provisionar infraestructura (sin instancias EC2 para gestionar)
- Se define la tarea con los requisitos de CPU y RAM
- AWS ejecuta las tareas ECS sin que conozcamos la ubicación
- Escalado simple aumentando el número de tareas
- Recomendado en el examen por su simplicidad

#### 12.3.2 Roles IAM para Tareas ECS

| Rol | Aplicabilidad | Propósito |
|-----|---------------|-----------|
| **EC2 Instance Profile** | Solo EC2 Launch Type | Usado por el ECS Agent para: <br>- API calls a ECS <br>- Enviar logs a CloudWatch <br>- Pull de imágenes de ECR <br>- Acceder a datos sensibles |
| **ECS Task Role** | EC2 y Fargate | Rol específico por tarea para: <br>- Permisos específicos de la aplicación <br>- Acceso a servicios AWS (S3, DynamoDB, etc.) <br>- Definido en la task definition |

#### 12.3.3 Integración con Load Balancers
- **Application Load Balancer**: Recomendado para la mayoría de casos, compatible con Fargate
- **Network Load Balancer**: Solo para alto throughput o uso con AWS PrivateLink
- **Classic Load Balancer**: No recomendado, funcionalidad limitada, incompatible con Fargate

#### 12.3.4 Persistencia de Datos
- Amazon EFS es la solución recomendada para persistencia
- Compatible con ambos launch types (EC2 y Fargate)
- Permite compartir datos entre tareas en múltiples AZs
- La combinación Fargate + EFS proporciona una solución completamente serverless

### 12.4 Amazon EKS (Elastic Kubernetes Service)

#### 12.4.1 Concepto y Diferencias con ECS
- Servicio para lanzar y gestionar clústeres Kubernetes en AWS
- Kubernetes: Sistema open-source para automatizar despliegue, escalado y gestión de contenedores
- **Diferencia principal con ECS**: Kubernetes es open-source y cloud-agnostic
- Soporta dos modos de lanzamiento: EC2 y Fargate

#### 12.4.2 Casos de Uso
- Empresas que ya utilizan Kubernetes on-premises o en otras nubes
- Preferencia por la API de Kubernetes
- Necesidad de portabilidad entre nubes
- Migraciones entre entornos cloud

#### 12.4.3 Tipos de Nodos
1. **Managed Node Groups**:
   - AWS crea y gestiona nodos (instancias EC2)
   - Parte de un Auto Scaling Group gestionado por EKS
   - Soporte para instancias On-Demand y Spot

2. **Self-Managed Nodes**:
   - Mayor control y personalización
   - Creación y registro manual en el clúster EKS
   - Requiere gestión propia como parte de un ASG
   - Puede usar Amazon EKS Optimized AMI o AMI personalizada

3. **Fargate Mode**:
   - Sin mantenimiento ni gestión de nodos
   - Modelo completamente serverless

#### 12.4.4 Volúmenes de Datos
- Requiere especificar un **StorageClass manifest** en el clúster
- Utiliza **Container Storage Interface (CSI)** compliant driver
- Opciones soportadas:
  - Amazon EBS
  - Amazon EFS (única opción compatible con Fargate)
  - Amazon FSx for Lustre
  - Amazon FSx for NetApp ONTAP

### 12.5 Comparativa de Servicios de Contenedores en AWS

| Característica | Docker | Amazon ECR | Amazon ECS | Amazon EKS |
|----------------|--------|------------|------------|------------|
| **Propósito** | Plataforma de contenedores | Repositorio de imágenes | Orquestación de contenedores (AWS-native) | Orquestación de contenedores (Kubernetes) |
| **Tipo de Servicio** | Software | Servicio AWS gestionado | Servicio AWS gestionado | Servicio AWS gestionado |
| **Control** | Alto | N/A | Medio | Alto |
| **Complejidad** | Media | Baja | Baja | Alta |
| **Portabilidad** | Alta | Baja | Baja | Alta (cloud-agnostic) |
| **Integración AWS** | Media | Alta | Alta | Alta |
| **Opciones Serverless** | No | N/A | Sí (Fargate) | Sí (Fargate) |

### Resumen para el examen:

- **Docker** es una plataforma para contenedores que permite ejecutar aplicaciones de forma consistente
- **ECR** es el servicio AWS para almacenar y gestionar imágenes Docker
- **ECS** es el servicio nativo de AWS para orquestación de contenedores con dos launch types:
  - **EC2 Launch Type**: Instancias EC2 gestionadas por el usuario
  - **Fargate Launch Type**: Modelo serverless sin gestión de infraestructura
- **EKS** es el servicio AWS para gestionar Kubernetes:
  - Opción ideal cuando ya se utiliza Kubernetes
  - Permite portabilidad entre clouds
  - Soporta nodos gestionados, auto-gestionados o Fargate
- **Integración entre servicios**:
  - ECR almacena imágenes para ECS/EKS
  - EFS proporciona almacenamiento persistente para ECS/EKS
  - IAM controla permisos entre servicios

Recordar que el examen suele favorecer soluciones serverless (Fargate) por su facilidad de gestión y que EKS es recomendable cuando se requiere portabilidad o se tiene experiencia previa con Kubernetes.

## 13. AWS Batch

### 13.1 Fundamentos de AWS Batch

#### 13.1.1 Definición y Propósito
- Servicio para ejecutar trabajos por lotes (batch jobs)
- Basado en imágenes Docker
- Modelo serverless para procesamiento por lotes
- Enfocado en cualquier tipo de trabajo computacional por lotes

#### 13.1.2 Aprovisionamiento de Recursos
- Aprovisionamiento dinámico de instancias
- No requiere configurar ni gestionar clústeres
- AWS determina automáticamente:
  - Cantidad óptima de instancias
  - Tipo adecuado de instancias
  - Escala según volumen y requisitos de los trabajos

#### 13.1.3 Infraestructura Subyacente
- Utiliza instancias EC2 o Spot
- Los recursos se crean en tu cuenta AWS
- Gestionados completamente por el servicio Batch
- Solo pagas por los recursos EC2 subyacentes utilizados

### 13.2 Programación y Orquestación

#### 13.2.1 Opciones de Programación
- **CloudWatch Events**: Para ejecución según cronograma
- **Step Functions**: Para orquestación de flujos complejos

#### 13.2.2 Integración con Servicios AWS
- Acceso a recursos dentro de tu cuenta AWS
- Compatibilidad con políticas IAM
- Integración con servicios de monitoreo y logging

### 13.3 Diferencias con AWS Glue

| Característica | AWS Batch | AWS Glue |
|----------------|-----------|----------|
| **Propósito** | Cualquier trabajo por lotes | Específicamente ETL |
| **Tecnología** | Cualquier imagen Docker | Apache Spark (Scala/Python) |
| **Caso de uso** | Procesamiento general por lotes | Transformación de datos |
| **Ejemplo** | Tareas de limpieza en S3, procesamiento de imágenes | Cargar datos desde S3 a Redshift |
| **Catálogo de datos** | No incluido | Incluye Data Catalog |
| **Configuración** | Más flexible (cualquier Docker) | Más específico (solo Spark) |

### 13.4 Casos de Uso

#### 13.4.1 Escenarios Ideales
- Tareas de limpieza programadas (ej. limpieza de buckets S3)
- Procesamiento de imágenes o vídeos en lotes
- Análisis científicos o de big data que no requieren ETL
- Cálculos financieros o actuariales periódicos
- Cualquier proceso computacional que se ejecute periódicamente

#### 13.4.2 Cuándo Elegir Batch vs. Otras Alternativas
- **Batch vs. Glue**: Cuando el trabajo no es específicamente ETL
- **Batch vs. Lambda**: Para trabajos de larga duración (>15 minutos)
- **Batch vs. ECS/EKS**: Cuando no quieres gestionar la infraestructura

### 13.5 Implementación de Trabajos Batch

#### 13.5.1 Componentes Principales
- **Definiciones de Trabajo (Job Definitions)**: Especifican cómo se ejecutan los trabajos
- **Colas de Trabajos (Job Queues)**: Priorizan y organizan los trabajos pendientes
- **Entornos de Computación (Compute Environments)**: Definen los recursos de computación

#### 13.5.2 Flujo de Trabajo Típico
1. Crear imagen Docker con la aplicación/script
2. Subir imagen a ECR
3. Crear definición de trabajo en Batch referenciando la imagen
4. Configurar entorno de computación (gestionado por AWS)
5. Crear cola de trabajos
6. Enviar trabajos a la cola
7. AWS Batch aprovisiona recursos y ejecuta trabajos automáticamente

### Resumen para el examen:

- **AWS Batch** es un servicio serverless para ejecutar trabajos por lotes basados en Docker
- No requiere provisionar ni gestionar infraestructura: **aprovisionamiento dinámico**
- Determina automáticamente la cantidad y tipo óptimos de instancias según necesidades
- Se puede programar mediante **CloudWatch Events** o orquestar con **Step Functions**
- Diferencias clave con AWS Glue:
  - Batch: cualquier trabajo por lotes (imágenes Docker)
  - Glue: específicamente para ETL (Apache Spark)
- Ideal para tareas de procesamiento computacional periódicas que no son ETL
- Los recursos se crean en tu cuenta AWS pero son gestionados por Batch
- Solo pagas por los recursos EC2/Spot subyacentes que utiliza el servicio

AWS Batch es la opción ideal cuando necesitas ejecutar procesos por lotes de cualquier tipo sin preocuparte por la gestión de infraestructura, especialmente para cargas de trabajo que no encajan en el modelo ETL de Glue o que exceden las limitaciones de tiempo de Lambda.

## 14. AWS CloudFormation

### 14.1 Concepto y Fundamentos

#### 14.1.1 Definición
- Servicio para definir y desplegar infraestructura AWS de forma declarativa
- Permite describir toda la infraestructura como código (IaC)
- Automatiza la creación de recursos en el orden correcto y con la configuración exacta

#### 14.1.2 Características Principales
- Soporte para la mayoría de recursos AWS
- Gestión de dependencias entre recursos
- Creación y eliminación de recursos en bloque (stacks)
- Visualización de arquitectura mediante Infrastructure Composer

#### 14.1.3 Ejemplo Práctico
Un template puede definir una arquitectura completa:
- Security groups
- Instancias EC2
- Buckets S3
- Load balancers
- Bases de datos
- Relaciones entre todos estos componentes

### 14.2 Beneficios Clave

#### 14.2.1 Infraestructura como Código
- Eliminación de creación manual de recursos
- Control y revisión de cambios a través de revisión de código
- Reproducibilidad garantizada
- Documentación implícita de la infraestructura

#### 14.2.2 Ventajas de Costos
- Etiquetado automático de recursos dentro del stack
- Facilita la estimación de costos de recursos
- Permite estrategias de ahorro:
  - Eliminación automática de entornos (ej. a las 5:00 PM)
  - Recreación programada (ej. a las 8:00 AM)
  - Ahorro durante períodos de inactividad

#### 14.2.3 Productividad
- Creación y eliminación rápida de infraestructura completa
- Generación automática de diagramas de arquitectura
- Programación declarativa (sin preocuparse por el orden)
- Reutilización de templates existentes
- Documentación extensiva disponible

### 14.3 Componentes y Funcionamiento

#### 14.3.1 Templates
- Archivos JSON o YAML que definen los recursos
- Pueden incluir parámetros, condiciones y salidas
- Permiten personalización para diferentes entornos

#### 14.3.2 Stacks
- Conjunto de recursos AWS creados a partir de un template
- Gestionados como una unidad única
- Todas las operaciones (crear, actualizar, eliminar) se aplican al stack completo

#### 14.3.3 Infrastructure Composer
- Servicio para visualizar templates de CloudFormation
- Muestra recursos y relaciones en formato gráfico
- Facilita la comprensión de arquitecturas complejas

### 14.4 Casos de Uso

#### 14.4.1 Escenarios Ideales
- Despliegue de infraestructura consistente en múltiples entornos
- Replicación de arquitecturas en diferentes regiones
- Implementación en múltiples cuentas AWS
- Entornos de desarrollo, pruebas y producción idénticos
- Despliegues temporales para pruebas o desarrollo

#### 14.4.2 Integración con MLOps
- Puede utilizarse para desplegar:
  - Clústeres ECS/EKS para contenerización
  - Endpoints de SageMaker
  - Infraestructura de procesamiento para ML
  - Pipelines completos de datos y ML

### 14.5 Consideraciones Avanzadas

#### 14.5.1 Custom Resources
- Permite extender CloudFormation para recursos no soportados nativamente
- Integración con funciones Lambda para funcionalidad personalizada

#### 14.5.2 Drift Detection
- Detecta cambios realizados fuera de CloudFormation
- Mantiene la integridad de la infraestructura como código

#### 14.5.3 Nested Stacks
- Organiza infraestructuras complejas en componentes reutilizables
- Permite modularidad y mejor gestión

### Resumen para el examen:

- **CloudFormation** es un servicio para definir infraestructura AWS como código
- **Principales beneficios**:
  - Infraestructura como código (IaC)
  - Control y revisión de cambios
  - Ahorro de costos mediante etiquetado y programación
  - Aumento de productividad con despliegues automatizados
  - Soporte para casi todos los recursos AWS
- **Infrastructure Composer** permite visualizar templates como diagramas
- **Casos de uso clave**: replicación de arquitecturas en diferentes entornos, regiones o cuentas AWS
- **CloudFormation vs. creación manual**: siempre preferir CloudFormation para entornos profesionales
- **Custom Resources** permiten extender funcionalidad a recursos no soportados nativamente

CloudFormation es fundamental para implementar infraestructura como código en AWS, permitiendo despliegues consistentes, reproducibles y controlados de arquitecturas completas, incluyendo aplicaciones de machine learning y sus componentes asociados.

## 15. AWS CDK (Cloud Development Kit)

### 15.1 Concepto y Fundamentos

#### 15.1.1 Definición
- Framework para definir infraestructura AWS usando lenguajes de programación convencionales
- Alternativa a CloudFormation tradicional (YAML/JSON)
- Genera templates de CloudFormation automáticamente

#### 15.1.2 Lenguajes Soportados
- TypeScript/JavaScript
- Python
- Java
- .NET (C#)

#### 15.1.3 Relación con CloudFormation
- Se sitúa como una capa superior a CloudFormation
- Compila código en lenguaje de programación a templates CloudFormation (JSON/YAML)
- Mantiene todas las capacidades de CloudFormation con mayor flexibilidad

### 15.2 Características Principales

#### 15.2.1 Constructs
- Componentes de alto nivel que encapsulan recursos AWS
- Proporcionan configuraciones predefinidas y mejores prácticas
- Permiten definir complejas infraestructuras con pocas líneas de código

#### 15.2.2 Ejemplo de Constructs
```typescript
// Definición de VPC con 3 zonas de disponibilidad
const vpc = new ec2.Vpc(this, "MyVpc", {
  maxAzs: 3
});

// Definición de cluster ECS en la VPC
const cluster = new ecs.Cluster(this, "MyCluster", {
  vpc: vpc
});

// Servicio Fargate con Application Load Balancer
new ecs_patterns.ApplicationLoadBalancedFargateService(this, "MyService", {
  cluster: cluster,
  cpu: 512,
  desiredCount: 6,
  taskImageOptions: {
    image: ecs.ContainerImage.fromRegistry("amazon/amazon-ecs-sample")
  },
  memoryLimitMiB: 2048,
  publicLoadBalancer: true
});
```

#### 15.2.3 Ventajas sobre CloudFormation Tradicional
- **Type safety**: Detección temprana de errores mediante tipado estático
- **Inteligencia de IDE**: Autocompletado, documentación en línea
- **Abstracciones**: Constructs que simplifican recursos complejos
- **Programación Orientada a Objetos**: Herencia, encapsulación, reutilización
- **Uso de lógica**: Bucles, condicionales, funciones

### 15.3 Flujo de Trabajo con CDK

#### 15.3.1 Proceso de Desarrollo y Despliegue
1. Escribir código en lenguaje de programación preferido
2. Utilizar CDK CLI para sintetizar en CloudFormation (`cdk synth`)
3. Revisar el template CloudFormation generado
4. Desplegar infraestructura (`cdk deploy`)

#### 15.3.2 Ciclo de Vida
- **cdk init**: Inicializar nuevo proyecto
- **cdk diff**: Ver cambios antes de implementar
- **cdk synth**: Generar template CloudFormation
- **cdk deploy**: Desplegar infraestructura
- **cdk destroy**: Eliminar infraestructura

### 15.4 Integración con Aplicaciones

#### 15.4.1 Despliegue Unificado
- Permite desplegar infraestructura y código de aplicación juntos
- Ideal para:
  - Funciones Lambda
  - Contenedores Docker en ECS/EKS
  - Aplicaciones serverless

#### 15.4.2 Integración con SAM
- Combinación de CDK con Serverless Application Model
- Flujo de trabajo:
  1. Usar `cdk synth` para generar CloudFormation
  2. Utilizar SAM CLI para probar localmente: `sam local invoke`
- Permite desarrollo y pruebas locales de aplicaciones serverless

### 15.5 Comparativa: CDK vs SAM

| Característica | AWS CDK | AWS SAM |
|----------------|---------|---------|
| **Enfoque** | Infraestructura completa | Aplicaciones serverless |
| **Lenguaje** | TypeScript, Python, Java, .NET | YAML/JSON |
| **Alcance** | Todos los servicios AWS | Principalmente Lambda, API Gateway, DynamoDB |
| **Backend** | Genera CloudFormation | Extiende CloudFormation |
| **Paradigma** | Programático | Declarativo |
| **Complejidad** | Mayor curva de aprendizaje, mayor flexibilidad | Más simple, menos flexible |
| **Testing local** | A través de integración con SAM | Nativo |

### 15.6 Caso de Uso: Aplicación de Reconocimiento de Imágenes

#### 15.6.1 Arquitectura
- S3 Bucket para almacenar imágenes
- Lambda Function activada por eventos S3
- Amazon Rekognition para análisis de imágenes
- DynamoDB para almacenar resultados

#### 15.6.2 Implementación con CDK
- Definición de todos los componentes en un solo script CDK
- Configuración automática de permisos y relaciones
- Despliegue unificado de infraestructura y código Lambda

### Resumen para el examen:

- **AWS CDK** permite definir infraestructura AWS usando lenguajes de programación convencionales
- **Ventajas principales**:
  - Type safety y detección temprana de errores
  - Uso de paradigmas OOP y lógica programática
  - Constructs de alto nivel para simplificar definiciones
  - Generación automática de CloudFormation
- **Diferencias con SAM**:
  - CDK: programático, cualquier servicio AWS, mayor flexibilidad
  - SAM: declarativo, enfocado en serverless, más simple
- **Integración con AWS SAM** permite pruebas locales de aplicaciones CDK
- **Flujo de trabajo**: escribir código → sintetizar a CloudFormation → desplegar
- **Ideal para**: equipos de desarrollo que prefieren código sobre YAML, aplicaciones serverless complejas, infraestructuras que requieren lógica condicional

AWS CDK representa una evolución natural para equipos que ya están familiarizados con CloudFormation pero necesitan mayor productividad y control mediante lenguajes de programación, especialmente para infraestructuras complejas o dinámicas.

## 16. AWS CI/CD Services: CodeDeploy, CodeBuild y CodePipeline

### 16.1 Fundamentos de CI/CD en AWS

#### 16.1.1 Definición de CI/CD
- **CI (Continuous Integration)**: Integración automática de código en un repositorio compartido
- **CD (Continuous Delivery/Deployment)**: Entrega o implementación automática de aplicaciones
- Automatización del ciclo de vida completo de desarrollo de software
- Conjunto de servicios AWS que facilitan este proceso

### 16.2 AWS CodeDeploy

#### 16.2.1 Concepto y Funcionalidad
- Servicio para automatizar despliegues de aplicaciones
- Permite actualizar aplicaciones de versión 1 a versión 2
- Funciona independientemente de CloudFormation o Beanstalk

#### 16.2.2 Compatibilidad
- **Servicio híbrido**: Funciona tanto con infraestructura AWS como on-premises
- Soporta:
  - Instancias EC2
  - Servidores on-premises
  - Funciones Lambda
  - Aplicaciones ECS

#### 16.2.3 Requisitos de Implementación
- Servidores deben aprovisionarse previamente
- Instalación del agente CodeDeploy en los servidores objetivo
- Configuración adecuada para comunicación con el servicio

#### 16.2.4 Casos de Uso
- Transición de on-premises a AWS
- Despliegues automatizados en entornos mixtos
- Actualización uniforme de aplicaciones en múltiples servidores

### 16.3 AWS CodeBuild

#### 16.3.1 Concepto y Funcionalidad
- Servicio para compilar y probar código en la nube
- Transformación de código fuente en paquetes desplegables
- Ejecución de pruebas automatizadas

#### 16.3.2 Proceso
1. Recuperación del código fuente (ej. desde CodeCommit)
2. Ejecución de scripts de compilación definidos por el usuario
3. Generación de artefactos listos para implementación

#### 16.3.3 Características
- **Totalmente gestionado y serverless**
- Escalado automático según necesidades
- Alta disponibilidad y seguridad
- Modelo de precios pay-as-you-go (pago por uso)
- No requiere gestión de servidores

#### 16.3.4 Ventajas
- Enfoque en desarrollo, no en infraestructura
- Compilación automática ante cambios en repositorio
- Integración con otros servicios AWS

### 16.4 AWS CodePipeline

#### 16.4.1 Concepto y Funcionalidad
- Servicio de orquestación para flujos de trabajo CI/CD
- Conecta diferentes etapas del proceso de desarrollo
- Automatiza el flujo desde código hasta producción

#### 16.4.2 Estructura de Pipeline
- Series de etapas conectadas secuencialmente
- Cada etapa puede contener una o más acciones
- Flujo típico: origen → compilación → pruebas → despliegue

#### 16.4.3 Ejemplo de Flujo
1. Origen: Obtener código de CodeCommit
2. Compilación: Construir con CodeBuild
3. Despliegue: Implementar con CodeDeploy
4. Entorno: Desplegar en Elastic Beanstalk

#### 16.4.4 Compatibilidad
- **Integración nativa con servicios AWS**:
  - CodeCommit, CodeBuild, CodeDeploy
  - Elastic Beanstalk, CloudFormation
  - Lambda, ECS
- **Servicios de terceros**:
  - GitHub, BitBucket
  - Jenkins
- **Plugins personalizados** para escenarios específicos

### 16.5 Interrelación entre Servicios CI/CD de AWS

#### 16.5.1 Arquitectura Integrada
- **CodeCommit**: Almacenamiento del código fuente
- **CodeBuild**: Compilación y pruebas
- **CodeDeploy**: Implementación en servidores o servicios
- **CodePipeline**: Orquestación del flujo completo

#### 16.5.2 Diagrama de Flujo Completo
```
[Desarrollador] → [CodeCommit] → [CodeBuild] → [CodeDeploy] → [Producción]
                       ↑               ↑              ↑
                       └───────[CodePipeline]────────┘
```

#### 16.5.3 Ventajas del Ecosistema Completo
- Automatización de principio a fin
- Entrega rápida y actualizaciones continuas
- Detección temprana de errores
- Despliegues consistentes y confiables
- Totalmente gestionado por AWS

### 16.6 Integración con ML/AI Workflows

#### 16.6.1 Aplicaciones en Machine Learning
- Compilación de modelos de ML
- Pruebas automatizadas de precisión
- Despliegue de modelos a endpoints de SageMaker
- Orquestación de pipelines de ML

#### 16.6.2 Ejemplo de Pipeline ML
1. Obtener código y datos de entrada
2. Compilar y entrenar modelo con CodeBuild
3. Evaluar calidad del modelo
4. Desplegar a endpoints de SageMaker con CodeDeploy
5. Monitorear rendimiento del modelo

### Resumen para el examen:

- **AWS CodeDeploy**: 
  - Servicio híbrido para desplegar aplicaciones en EC2 y servidores on-premises
  - Requiere agente instalado en servidores objetivo
  - Permite actualización automática de aplicaciones

- **AWS CodeBuild**:
  - Servicio serverless para compilación y pruebas
  - Transforma código fuente en artefactos desplegables
  - Modelo de precios pay-as-you-go
  - No requiere gestión de servidores

- **AWS CodePipeline**:
  - Orquestación de flujos de trabajo CI/CD
  - Conecta diferentes etapas del proceso
  - Compatible con servicios AWS y herramientas de terceros
  - Núcleo del ecosistema CI/CD en AWS

- **Ecosistema CI/CD AWS**:
  - Automatización completa desde código hasta producción
  - Todos los servicios son totalmente gestionados
  - Facilita entrega rápida y actualizaciones continuas
  - Se integra con workflows de ML/AI

Recordar que siempre que se mencione "orquestación de pipeline" en el examen, se refiere a AWS CodePipeline, mientras que CodeDeploy se centra en la implementación y CodeBuild en la compilación/pruebas.

## 17. Git Review: Architecture and Commands

### 17.1 Arquitectura de Git

#### 17.1.1 Estructura Básica
- Sistema de control de versiones distribuido
- Permite que múltiples desarrolladores trabajen simultáneamente
- Compuesto por repositorios remotos y locales

#### 17.1.2 Componentes Principales
- **Repositorio remoto**: Ubicado en un servidor (GitHub, AWS CodeCommit, etc.)
- **Repositorio local**: Copia completa del repositorio en la máquina del desarrollador
- **Working directory**: Archivos actuales en los que se trabaja
- **Staging area**: Área intermedia para preparar cambios antes de confirmarlos
- **Branches (ramas)**: Líneas de desarrollo paralelas e independientes

#### 17.1.3 Flujo de Trabajo Típico
```
[Repositorio Remoto] ← git pull/push → [Repositorio Local] ← git commit → [Staging Area] ← git add → [Working Directory]
```

### 17.2 Comandos Git Esenciales

#### 17.2.1 Configuración de Repositorios
| Comando | Descripción |
|---------|-------------|
| `git init` | Inicializa un nuevo repositorio |
| `git config` | Configura usuario, email y otras opciones |
| `git clone [URL]` | Clona un repositorio remoto |

#### 17.2.2 Comandos Básicos
| Comando | Descripción |
|---------|-------------|
| `git status` | Muestra el estado de los archivos en el working directory |
| `git add [archivo]` | Añade cambios al staging area |
| `git add *` | Añade todos los cambios al staging area |
| `git commit -m "mensaje"` | Confirma los cambios en el staging area |
| `git log` | Muestra el historial de commits |

#### 17.2.3 Gestión de Ramas (Branches)
| Comando | Descripción |
|---------|-------------|
| `git branch` | Lista todas las ramas locales |
| `git branch [nombre]` | Crea una nueva rama |
| `git checkout [rama]` | Cambia a la rama especificada |
| `git checkout -b [rama]` | Crea una nueva rama y cambia a ella |
| `git merge [rama]` | Fusiona la rama especificada con la rama actual |
| `git branch -d [rama]` | Elimina una rama |

#### 17.2.4 Trabajo con Repositorios Remotos
| Comando | Descripción |
|---------|-------------|
| `git remote add [nombre] [URL]` | Añade un repositorio remoto |
| `git remote` | Lista los repositorios remotos configurados |
| `git push [remoto] [rama]` | Envía cambios locales al repositorio remoto |
| `git pull [remoto] [rama]` | Obtiene y fusiona cambios del repositorio remoto |

#### 17.2.5 Deshacer Cambios
| Comando | Descripción |
|---------|-------------|
| `git reset` | Restablece el staging area sin afectar el working directory |
| `git reset --hard` | Restablece staging area y working directory al último commit |
| `git revert [commit]` | Revierte los cambios de un commit específico |

### 17.3 Comandos Avanzados

#### 17.3.1 Gestión Temporal de Cambios
| Comando | Descripción |
|---------|-------------|
| `git stash` | Guarda temporalmente cambios no committeados |
| `git stash pop` | Restaura los cambios guardados en stash |

#### 17.3.2 Integración de Cambios
| Comando | Descripción |
|---------|-------------|
| `git rebase [rama]` | Reaplica cambios de una rama sobre otra |
| `git cherry-pick [commit]` | Aplica cambios de un commit específico a la rama actual |

#### 17.3.3 Análisis y Diagnóstico
| Comando | Descripción |
|---------|-------------|
| `git blame [archivo]` | Muestra quién modificó cada línea de un archivo y cuándo |
| `git diff` | Muestra diferencias entre working directory y staging area |
| `git fetch` | Obtiene cambios del repositorio remoto sin fusionarlos |

#### 17.3.4 Mantenimiento y Recuperación
| Comando | Descripción |
|---------|-------------|
| `git fsck` | Verifica la integridad del repositorio |
| `git gc` | Optimiza el repositorio local (garbage collection) |
| `git reflog` | Muestra historial de actualizaciones de referencias |

### 17.4 Aplicación en Flujos de ML/AI

#### 17.4.1 Casos de Uso en Machine Learning
- Versionado de modelos de ML
- Seguimiento de cambios en datasets
- Colaboración en desarrollo de algoritmos
- Documentación de experimentos

#### 17.4.2 Integración con AWS
- AWS CodeCommit como repositorio Git remoto
- Integración con CodePipeline para CI/CD
- Versionado de infraestructura como código
- Control de versiones para notebooks de SageMaker

### Resumen para el examen:

- **Arquitectura Git**: Sistema distribuido con repositorios remotos y locales, working directory, staging area y branches
- **Flujo básico**: 
  1. `git clone/pull` para obtener código
  2. `git branch` para trabajo en paralelo
  3. `git add` para staging
  4. `git commit` para confirmar cambios
  5. `git push` para enviar a repositorio remoto
- **Comandos esenciales** que debes conocer:
  - Configuración: `init`, `config`, `clone`
  - Básicos: `status`, `add`, `commit`, `log`
  - Branches: `branch`, `checkout`, `merge`
  - Remotos: `remote`, `push`, `pull`
  - Deshacer: `reset`, `revert`
  - Avanzados: `stash`, `rebase`, `cherry-pick`, `blame`, `diff`

Git es fundamental para el trabajo colaborativo en desarrollo de software, incluyendo aplicaciones de machine learning, y su conocimiento es relevante para la integración con servicios AWS como CodeCommit, CodeBuild, CodePipeline y SageMaker.

## 18. Metodologías de Control de Versiones: Gitflow y GitHub Flow

### 18.1 Gitflow

#### 18.1.1 Definición y Concepto
- Modelo de workflow para Git más tradicional y estructurado
- Diseñado para proyectos con ciclos de lanzamiento bien definidos
- Popularizado por Atlassian y herramientas de gestión ágil

#### 18.1.2 Estructura de Ramas
| Rama | Propósito |
|------|-----------|
| **master/main** | Código en producción, versiones estables |
| **develop** | Integración de cambios desde la última versión |
| **feature/** | Desarrollo de nuevas funcionalidades |
| **release/** | Preparación para lanzamiento a producción |
| **hotfix/** | Correcciones urgentes de bugs en producción |

#### 18.1.3 Flujo de Trabajo
1. La rama **develop** se crea a partir de **master**
2. Las ramas **feature/** se crean a partir de **develop**
3. Cuando una feature está completa, se fusiona con **develop**
4. Se crea una rama **release/** desde **develop**
5. Cuando la release está lista, se fusiona con **master** y **develop**
6. Si se detecta un problema en **master**, se crea un **hotfix/**
7. El **hotfix/** se fusiona tanto en **master** como en **develop**

#### 18.1.4 Ventajas
- Permite desarrollo paralelo de múltiples funcionalidades
- Proporciona estructura clara para gestión de versiones
- Ideal para software con ciclos de lanzamiento planificados
- Separa claramente el trabajo en desarrollo del código en producción

#### 18.1.5 Desventajas
- Complejidad significativa en gestión de ramas
- Requiere disciplina y conocimiento del flujo por parte del equipo
- La sincronización entre tantas ramas puede ser complicada
- Overhead administrativo considerable

### 18.2 GitHub Flow

#### 18.2.1 Definición y Concepto
- Workflow simplificado propuesto por GitHub
- Enfocado en despliegues continuos y alta frecuencia
- Diseñado para equipos ágiles con ciclos rápidos

#### 18.2.2 Estructura de Ramas
| Rama | Propósito |
|------|-----------|
| **main/master** | Código en producción, siempre desplegable |
| **feature/** | Desarrollo de cualquier cambio (nuevas funciones, fixes, etc.) |

#### 18.2.3 Flujo de Trabajo
1. Se crea una rama **feature/** a partir de **main**
2. Se desarrolla y prueba el cambio en la rama feature
3. Se crea un Pull Request para revisión de código
4. Después de la aprobación, se fusiona con **main**
5. El cambio se despliega inmediatamente a producción

#### 18.2.4 Ventajas
- Extremadamente simple y fácil de entender
- Reduce la complejidad de gestión de ramas
- Promueve integración continua y entrega continua (CI/CD)
- Revisión de código integrada en el proceso (Pull Requests)

#### 18.2.5 Desventajas
- Requiere infraestructura de CI/CD madura
- Depende de pruebas automatizadas confiables
- No ideal para software con ciclos de lanzamiento largos
- Posible "movimiento rápido y ruptura de cosas" (move fast and break things)

### 18.3 Comparativa de Metodologías

| Aspecto | Gitflow | GitHub Flow |
|---------|---------|-------------|
| **Complejidad** | Alta | Baja |
| **Número de ramas** | 5+ tipos | 2 tipos |
| **Ciclo de desarrollo** | Planificado, estructurado | Ágil, continuo |
| **Frecuencia de despliegue** | Baja-media | Alta (múltiples veces al día) |
| **Requisitos** | Disciplina de equipo | CI/CD automático |
| **Ideal para** | Software con releases planificadas | Aplicaciones web, servicios continuos |
| **Revisión de código** | Variable, no está integrada | Integrada vía Pull Requests |

### 18.4 Aplicación en Proyectos de ML/AI

#### 18.4.1 Consideraciones para Machine Learning
- Los modelos ML tienen componentes adicionales (datos, hiperparámetros)
- El entrenamiento puede ser computacionalmente costoso
- La evaluación requiere métricas específicas

#### 18.4.2 Recomendaciones por Tipo de Proyecto
- **Investigación ML**: Gitflow puede ser preferible para experimentación estructurada
- **Modelos en producción**: GitHub Flow con CI/CD para actualización continua
- **Sistemas híbridos**: Adaptación personalizada según componentes

### 18.5 Selección de Metodología Adecuada

#### 18.5.1 Factores a Considerar
- Tamaño y madurez del equipo
- Ciclo de vida del producto
- Infraestructura de CI/CD disponible
- Requisitos de calidad y estabilidad
- Velocidad de desarrollo necesaria

#### 18.5.2 Preguntas Clave
1. ¿Se requieren lanzamientos planificados o continuos?
2. ¿Existe una infraestructura de pruebas automatizadas confiable?
3. ¿Cuántos desarrolladores trabajarán simultáneamente?
4. ¿Qué tan críticos son los bugs en producción?
5. ¿Cuál es la velocidad de iteración requerida?

### Resumen para el examen:

- **Gitflow**:
  - Workflow estructurado con múltiples tipos de ramas
  - Ramas principales: **master**, **develop**, **feature/**, **release/**, **hotfix/**
  - Ideal para proyectos con ciclos de lanzamiento definidos
  - Mayor complejidad pero mejor organización para desarrollo paralelo

- **GitHub Flow**:
  - Workflow simplificado con solo dos tipos de ramas
  - Ramas: **main/master** y **feature/** (temporales)
  - Basado en Pull Requests y despliegues continuos
  - Requiere CI/CD robusto y pruebas automatizadas
  - Ideal para equipos ágiles con despliegue frecuente

- **Elección de metodología**:
  - Depende de la naturaleza del proyecto, tamaño del equipo y frecuencia de despliegue
  - Trade-off entre simplicidad y estructura
  - Para ML/AI: considerar complejidad adicional de datos y entrenamiento

Las metodologías de control de versiones son fundamentales para la colaboración efectiva en equipos de desarrollo, incluyendo proyectos de machine learning, donde la gestión adecuada del código, datos y modelos es crucial para el éxito.

## 19. Amazon EventBridge

### 19.1 Fundamentos y Evolución

#### 19.1.1 Definición
- Servicio de bus de eventos sin servidor
- Anteriormente conocido como CloudWatch Events
- Permite interceptar, filtrar y direccionar eventos en el ecosistema AWS

#### 19.1.2 Propósito Principal
- Conectar aplicaciones mediante eventos
- Orquestar flujos de trabajo automatizados
- Programar tareas y procesos en la nube

### 19.2 Tipos de Events Buses

#### 19.2.1 Default Event Bus
- Recibe eventos de servicios AWS
- Procesamiento automático de eventos internos
- Disponible por defecto en todas las cuentas AWS

#### 19.2.2 Partner Event Bus
- Recibe eventos de servicios externos integrados
- Conexión con proveedores SaaS (Zendesk, Datadog, Auth0, etc.)
- Permite reaccionar a cambios fuera del ecosistema AWS

#### 19.2.3 Custom Event Bus
- Creado para aplicaciones propias
- Permite integración de eventos personalizados
- Facilita arquitecturas orientadas a eventos

### 19.3 Fuentes de Eventos

#### 19.3.1 Servicios AWS
- EC2: inicio, parada, terminación de instancias
- S3: creación, eliminación, actualización de objetos
- CodeBuild: éxito o fallo de compilaciones
- CloudTrail: cualquier llamada a API en la cuenta
- Trusted Advisor: nuevos hallazgos de seguridad

#### 19.3.2 Eventos Programados
- Basados en cron para ejecución periódica
- Ejemplos:
  - Cada hora
  - Cada lunes a las 8:00 AM
  - Primer lunes del mes
  - Cada 4 horas

#### 19.3.3 Eventos Externos
- Integración con proveedores SaaS mediante Partner Event Bus
- Eventos personalizados de aplicaciones propias

### 19.4 Destinos y Acciones

#### 19.4.1 Computación
- AWS Lambda: ejecución de funciones
- ECS: lanzamiento de tareas
- AWS Batch: programación de trabajos

#### 19.4.2 Mensajería
- SNS: notificaciones
- SQS: colas de mensajes
- Kinesis Data Streams: streaming de datos

#### 19.4.3 Orquestación
- Step Functions: iniciar flujos de trabajo
- CodePipeline: disparar pipelines CI/CD
- CodeBuild: iniciar compilaciones

#### 19.4.4 Administración
- SSM: automatizaciones
- EC2 Actions: iniciar/detener/reiniciar instancias

### 19.5 Características Avanzadas

#### 19.5.1 Archivado y Reproducción
- Archivado de eventos con filtros selectivos
- Opciones de retención:
  - Indefinida
  - Periodo específico
- Reproducción (replay) de eventos archivados
- Casos de uso:
  - Depuración de errores
  - Pruebas de nuevas implementaciones
  - Recuperación tras fallos

#### 19.5.2 Schema Registry
- Inferencia automática de esquemas de eventos
- Generación de código para aplicaciones
- Versionado de esquemas
- Simplifica procesamiento de eventos estructurados

#### 19.5.3 Resource-Based Policies
- Control de permisos para buses de eventos específicos
- Gestión de acceso entre cuentas y regiones
- Permite arquitecturas multi-cuenta

### 19.6 Patrones Arquitectónicos

#### 19.6.1 Centralización de Eventos
- Event bus central en una cuenta dedicada
- Agregación de eventos de múltiples cuentas
- Política basada en recursos que permite eventos entre cuentas
- Monitoreo y auditoría centralizados

#### 19.6.2 Arquitectura de Microservicios
- Comunicación asíncrona entre servicios
- Desacoplamiento mediante eventos
- Escalabilidad y resiliencia mejoradas

#### 19.6.3 Integración con Machine Learning
- Activación de procesos de entrenamiento
- Monitoreo de drift en modelos
- Despliegue automatizado de nuevos modelos

### 19.7 Ejemplos Prácticos

#### 19.7.1 Seguridad Automatizada
- Detección de inicio de sesión con usuario root
- Envío de notificación SNS a administradores
- Ejecución automática de verificaciones de seguridad

#### 19.7.2 ETL Programado
- Ejecución periódica de Lambda para procesamiento ETL
- Coordinación de flujos de datos mediante eventos
- Notificación de completado o errores

#### 19.7.3 Gestión de Infraestructura
- Parada automática de entornos de desarrollo fuera de horario laboral
- Inicio de instancias bajo demanda
- Respuesta automática a eventos de escalado

### Resumen para el examen:

- **EventBridge** (anteriormente CloudWatch Events) orquesta eventos en AWS
- **Tres tipos de buses de eventos**:
  - **Default**: eventos de servicios AWS
  - **Partner**: eventos de servicios SaaS externos
  - **Custom**: eventos personalizados de aplicaciones propias
- **Fuentes** incluyen servicios AWS, programaciones cron y aplicaciones externas
- **Destinos** incluyen prácticamente cualquier servicio AWS (Lambda, SQS, SNS, Step Functions, etc.)
- **Características avanzadas**:
  - Archivado y reproducción de eventos
  - Schema Registry para inferencia y generación de código
  - Políticas basadas en recursos para control entre cuentas
- **Casos de uso clave**:
  - Automatización de respuestas a eventos
  - Programación de tareas (cron)
  - Integración entre servicios AWS y aplicaciones externas
  - Arquitecturas orientadas a eventos
  - Centralización de eventos en organizaciones multi-cuenta

EventBridge es un componente fundamental para crear arquitecturas orientadas a eventos en AWS, permitiendo automatizar flujos de trabajo y responder dinámicamente a cambios en el entorno cloud.

## 20. AWS Step Functions

### 20.1 Concepto y Fundamentos

#### 20.1.1 Definición
- Servicio completamente gestionado para coordinar flujos de trabajo en AWS
- Similar a Data Pipelines pero más visual y flexible
- Permite diseñar y automatizar procesos que involucran múltiples servicios AWS

#### 20.1.2 Características Principales
- Visualización gráfica de flujos de trabajo
- Gestión de errores y reintentos a nivel de sistema
- Registro de auditoría completo de ejecuciones
- Capacidad para introducir esperas entre pasos
- Duración máxima de ejecución: hasta un año

#### 20.1.3 Terminología Básica
- **State Machine**: El flujo de trabajo completo definido en Step Functions
- **State**: Cada paso individual dentro del flujo de trabajo
- **Transición**: Movimiento entre estados
- **Execution**: Una instancia específica de ejecución del state machine

### 20.2 Tipos de Estados (States)

#### 20.2.1 Task State
- Estado que realiza una acción específica
- Ejecuta una unidad de trabajo (Lambda, ECS, SageMaker, etc.)
- Puede invocar otras funciones o servicios AWS
- También puede comunicarse con APIs externas

#### 20.2.2 Choice State
- Implementa lógica condicional dentro del flujo
- Permite bifurcaciones basadas en comparaciones
- Dirige el flujo según el resultado de evaluaciones
- Ejemplos: verificar tamaño de datos, validar contenido

#### 20.2.3 Wait State
- Introduce pausas deliberadas en el flujo
- Puede esperar:
  - Un tiempo específico
  - Hasta una hora determinada
  - Hasta que se cumpla una condición

#### 20.2.4 Parallel State
- Crea múltiples ramas de ejecución simultáneas
- Ejecuta diferentes operaciones al mismo tiempo
- Continúa cuando todas las ramas se completan
- Útil para procesamiento independiente

#### 20.2.5 Map State
- Procesa colecciones de datos en paralelo
- Aplica un conjunto de pasos a cada elemento de un dataset
- Implícitamente paralelo, no requiere state parallel adicional
- Ideal para procesamiento distribuido de datos
- Compatible con JSON, S3 objects, CSV, etc.

#### 20.2.6 Estados de Control
- **Pass State**: Simplemente pasa datos al siguiente estado (útil para depuración)
- **Succeed State**: Finaliza la ejecución con éxito
- **Fail State**: Finaliza la ejecución con fallo

### 20.3 Implementación Técnica

#### 20.3.1 Amazon States Language (ASL)
- Lenguaje basado en JSON para definir state machines
- No es necesario conocerlo en detalle para el examen
- Define estados, transiciones y manejo de errores

#### 20.3.2 Integración con Servicios AWS
- Lambda Functions: procesamiento serverless
- SageMaker: entrenamiento e inferencia ML
- AWS Batch: procesamiento por lotes
- DynamoDB: operaciones en bases de datos
- SNS/SQS: mensajería
- Glue: procesamiento ETL
- ECS/Fargate: ejecución de contenedores

### 20.4 Casos de Uso en Machine Learning

#### 20.4.1 Entrenamiento de Modelos
1. Generación de datasets mediante Lambda
2. Entrenamiento de modelo con SageMaker
3. Guardado del modelo entrenado
4. Aplicación de batch transform para inferencia

#### 20.4.2 Ajuste de Hiperparámetros
1. Generación de datos de entrenamiento
2. Ejecución de trabajo de hyperparameter tuning en SageMaker
3. Extracción de la configuración óptima
4. Guardado del modelo ajustado
5. Aplicación de transformaciones por lotes

#### 20.4.3 Gestión de Trabajos Batch
1. Envío de trabajo batch
2. Monitoreo del estado del trabajo
3. Notificación en caso de éxito o fallo

### 20.5 Ventajas para Ingeniería de Datos

#### 20.5.1 Orquestación Centralizada
- Coordinación de múltiples servicios desde un solo punto
- Visualización de todo el flujo de procesamiento
- Monitoreo centralizado del progreso

#### 20.5.2 Manejo de Errores Robusto
- Sistema de reintento independiente del código interno
- Gestión de fallos a nivel de workflow
- Capacidad para definir estrategias personalizadas de error

#### 20.5.3 Escalabilidad
- Procesamiento paralelo automático con Map State
- Capacidad para manejar grandes volúmenes de datos
- Adaptación a cargas variables

### 20.6 Limitaciones y Consideraciones

#### 20.6.1 Límites de Servicio
- Duración máxima de ejecución: 1 año
- Tamaño máximo de entrada/salida: 256KB (estándar)
- Límites ampliados disponibles con Step Functions Express Workflows

#### 20.6.2 Escenarios de Uso Adecuados
- Procesos de larga duración
- Flujos con múltiples integraciones de servicios
- Operaciones que requieren visualización del estado

### Resumen para el examen:

- **AWS Step Functions** permite diseñar, visualizar y automatizar flujos de trabajo complejos
- Los workflows se definen como **state machines** utilizando Amazon States Language (JSON)
- **Tipos principales de estados**:
  - **Task**: Ejecuta una acción (Lambda, SageMaker, etc.)
  - **Choice**: Implementa lógica condicional
  - **Wait**: Introduce pausas
  - **Parallel**: Crea múltiples ramas simultáneas
  - **Map**: Procesa colecciones de datos en paralelo
  - **Pass/Succeed/Fail**: Estados de control
- **Casos de uso destacados**:
  - Entrenamiento y ajuste de modelos ML
  - Orquestación de pipelines de datos
  - Procesamiento paralelo de datos
  - Flujos de trabajo con manejo de errores robusto
- **Características clave**: 
  - Visualización gráfica
  - Manejo de errores externo al código
  - Registro de auditoría
  - Capacidad para flujos de hasta 1 año

AWS Step Functions es fundamental para orquestar pipelines de datos y flujos de ML en AWS, permitiendo la automatización compleja con manejo de errores robusto y procesamiento paralelo eficiente.

## 21. Amazon Managed Workflows for Apache Airflow (MWAA)

### 21.1 Fundamentos de Apache Airflow

#### 21.1.1 Definición
- Sistema de gestión de flujos de trabajo por lotes orientado a datos
- Plataforma de código abierto para desarrollar, programar y monitorear pipelines
- Enfoque basado en código para definición de flujos de trabajo
- Ampliamente utilizado en la industria para orquestación de datos

#### 21.1.2 Concepto de DAG
- **DAG**: Directed Acyclic Graph (Grafo Acíclico Dirigido)
- Representación de flujos de trabajo como secuencias de tareas
- "Directed": conexiones unidireccionales entre tareas
- "Acyclic": sin ciclos/bucles en la estructura
- Define dependencias y orden de ejecución

#### 21.1.3 Implementación en Python
- Flujos de trabajo definidos mediante código Python
- No utiliza interfaces gráficas drag-and-drop
- Mayor flexibilidad y control programático
- Permite integración con bibliotecas y herramientas Python

### 21.2 Amazon MWAA

#### 21.2.1 Concepto y Propósito
- Servicio gestionado de AWS para Apache Airflow
- Elimina la complejidad de instalación y mantenimiento
- Conserva todas las capacidades de Apache Airflow
- Integración nativa con servicios AWS

#### 21.2.2 Arquitectura
- **VPC Cliente**: Contiene schedulers y workers en contenedores Fargate
- **VPC de Servicio**: Contiene componentes de infraestructura MWAA
- **Base de datos de metadatos**: Almacena definiciones de workflows
- **Servidor web de Airflow**: Interfaz de usuario para gestión
- Implementación en al menos dos zonas de disponibilidad para redundancia

#### 21.2.3 Conexión con Servicios AWS
- Acceso a servicios AWS mediante integraciones de código abierto
- Compatible con numerosos servicios:
  - Athena
  - AWS Batch
  - CloudWatch
  - DynamoDB
  - EMR
  - Fargate/ECS
  - Glue
  - Kinesis
  - Lambda
  - Redshift
  - SageMaker
  - S3
  - SQS/SNS

### 21.3 Funcionamiento e Implementación

#### 21.3.1 Flujo de Despliegue
1. Desarrollo de DAGs en Python
2. Carga de DAGs y dependencias en S3
3. MWAA detecta automáticamente y carga los DAGs
4. Ejecución de workflows según programación definida

#### 21.3.2 Gestión de Código
- Código Python almacenado en bucket S3
- Posibilidad de empaquetar código con dependencias
- Soporte para plugins y extensiones personalizadas
- Versionado y control de cambios mediante S3

#### 21.3.3 Ejemplo de DAG Básico
```python
# Ejemplo conceptual
from airflow import DAG
from airflow.operators.bash_operator import BashOperator
from airflow.operators.python_operator import PythonOperator

def print_airflow():
    print("airflow")

dag = DAG('example_dag', schedule_interval='0 0 * * *')

task1 = BashOperator(
    task_id='hello',
    bash_command='echo hello',
    dag=dag)

task2 = PythonOperator(
    task_id='airflow',
    python_callable=print_airflow,
    dag=dag)

task1 >> task2  # Define dependencia: task1 debe completarse antes de task2
```

### 21.4 Características Principales

#### 21.4.1 Escalabilidad
- Escalado automático de workers
- Adaptación a cargas de trabajo variables
- Configuración de límites para control de costos

#### 21.4.2 Seguridad
- Acceso vía IAM
- Endpoints públicos o privados para el servidor web
- Secretos gestionados con AWS Secrets Manager
- Implementación dentro de VPC privada

#### 21.4.3 Monitoreo
- Integración con CloudWatch para métricas y logs
- Visualización de DAGs y su estado mediante interfaz web
- Alertas configurables para fallos y anomalías

#### 21.4.4 Alta Disponibilidad
- Despliegue en múltiples zonas de disponibilidad
- Recuperación automática ante fallos
- Escalado bajo demanda

### 21.5 Casos de Uso

#### 21.5.1 Procesamiento ETL
- Orquestación de pipelines de extracción, transformación y carga
- Coordinación de trabajos en Glue, EMR o Spark
- Gestión de dependencias entre procesos

#### 21.5.2 Machine Learning
- Preparación y procesamiento de datos para entrenamiento
- Coordinación de trabajos de entrenamiento en SageMaker
- Evaluación y despliegue de modelos

#### 21.5.3 Procesos de Negocio
- Automatización de reportes periódicos
- Gestión de procesamiento por lotes
- Integración entre múltiples servicios y fuentes de datos

### 21.6 Comparación con AWS Step Functions

| Aspecto | MWAA | Step Functions |
|---------|------|---------------|
| **Definición** | Código Python | JSON (Amazon States Language) |
| **Visualización** | UI web de Airflow | Consola AWS con diagrama de estados |
| **Flexibilidad** | Alta (código Python) | Media (estados predefinidos) |
| **Integración** | Mediante operadores de Airflow | Nativa con servicios AWS |
| **Curva de aprendizaje** | Mayor (requiere Python) | Menor (interfaz visual) |
| **Casos ideales** | Flujos complejos, equipos con experiencia en Airflow | Procesos centrados en AWS, workflows visuales |

### Resumen para el examen:

- **Amazon MWAA** es un servicio gestionado para **Apache Airflow**, una herramienta de orquestación de workflows
- Los flujos de trabajo se definen como **DAGs** (Directed Acyclic Graphs) mediante **código Python**
- Proceso de implementación:
  1. Escribir DAGs en Python
  2. Cargar código a bucket S3
  3. MWAA detecta y gestiona los DAGs
- **Arquitectura**: Desplegado en VPC con schedulers y workers en contenedores Fargate
- **Características clave**:
  - Escalado automático
  - Alta disponibilidad (múltiples AZs)
  - Seguridad mediante IAM
  - Endpoints públicos o privados
  - Integración nativa con servicios AWS
- **Casos de uso óptimos**: ETL complejos, pipelines de ML, orquestación de datos
- **Ventaja principal**: Elimina la carga operativa de mantener Apache Airflow

MWAA es ideal para equipos que ya utilizan Airflow o prefieren definir sus workflows mediante código Python, ofreciendo la potencia de Airflow con la simplicidad operativa de un servicio gestionado por AWS.

## 22. AWS Lake Formation

### 22.1 Fundamentos y Propósito

#### 22.1.1 Definición
- Servicio para simplificar la creación y gestión de data lakes seguros
- Construido sobre AWS Glue como base tecnológica
- Promesa de valor: "Establecer un data lake seguro en días"
- Servicio relativamente nuevo (anunciado en 2018, refinado en 2020)

#### 22.1.2 Funciones Principales
- Carga de datos desde fuentes externas
- Monitoreo de flujos de datos
- Configuración de particiones
- Gestión de encriptación y claves
- Transformaciones de datos mediante Glue
- Control de acceso centralizado
- Auditoría de uso y acceso

### 22.2 Arquitectura e Integración

#### 22.2.1 Componentes del Ecosistema
- **S3**: Almacenamiento subyacente del data lake
- **AWS Glue**: Motor para transformaciones, crawlers y catálogo de datos
- **Lake Formation**: Capa de gestión y seguridad
- **Athena/Redshift/EMR**: Servicios de consulta y análisis

#### 22.2.2 Fuentes de Datos Compatibles
- Datos en Amazon S3
- Bases de datos relacionales (RDS, on-premises)
- Fuentes NoSQL
- Datos on-premises
- Streaming a través de Kinesis

#### 22.2.3 Integración con Servicios de Análisis
- **Athena**: Consultas SQL interactivas
- **Redshift/Redshift Spectrum**: Data warehousing
- **EMR**: Procesamiento distribuido
- **QuickSight**: Visualización (a través de los servicios anteriores)

### 22.3 Proceso de Implementación

#### 22.3.1 Pasos para Crear un Data Lake
1. Crear usuario IAM con rol de analista de datos
2. Establecer conexión Glue a las fuentes de datos
3. Crear bucket S3 para el data lake
4. Registrar la ruta S3 en Lake Formation
5. Asignar permisos iniciales
6. Crear base de datos en el catálogo de datos
7. Configurar blueprints y workflows para transformaciones
8. Ejecutar workflows
9. Otorgar permisos de acceso a usuarios y servicios

#### 22.3.2 Blueprints y Workflows
- Templates predefinidos para casos de uso comunes
- Snapshots de bases de datos
- Transformaciones ETL
- Conversión a formatos columnar (Parquet, ORC)
- Limpieza y preparación de datos

### 22.4 Gestión de Permisos y Seguridad

#### 22.4.1 Modelo de Permisos
- Control centralizado desde Lake Formation
- Permisos a nivel de:
  - Base de datos
  - Tabla
  - Columna
  - Fila (nueva característica)
  - Celda (nueva característica)

#### 22.4.2 Acceso Entre Cuentas
- Requiere configurar al destinatario como administrador del data lake
- Utiliza AWS Resource Access Manager (RAM)
- Necesita permisos IAM apropiados
- Gestión de claves KMS para datos encriptados

#### 22.4.3 Interfaz de Gestión de Permisos
- Asignación a usuarios IAM, roles, usuarios SAML o cuentas externas
- Uso opcional de policy tags para clasificación
- Control granular por tabla o columna
- Configuración de permisos específicos (SELECT, INSERT, DELETE, etc.)

### 22.5 Características Avanzadas

#### 22.5.1 Governed Tables
- Soporte para transacciones ACID entre múltiples tablas
- Permite operaciones concurrentes seguras
- Basadas en un nuevo tipo de tabla S3
- La elección de "governed" vs. "estándar" es permanente
- Compatible con datos streaming (Kinesis)
- Integración con Athena para consultas con garantías ACID

#### 22.5.2 Compactación Automática
- Optimiza el almacenamiento automáticamente
- Reduce el overhead que se acumula con el tiempo
- Mejora el rendimiento de las consultas
- Funcionamiento transparente para el usuario

#### 22.5.3 Seguridad Granular
- Control de acceso a nivel de fila
- Control de acceso a nivel de celda
- Disponible tanto para tablas gobernadas como estándar
- Ideal para datos con requisitos estrictos de privacidad

### 22.6 Consideraciones de Costo y Rendimiento

#### 22.6.1 Modelo de Precios
- Lake Formation en sí no tiene costo adicional
- Se paga por servicios subyacentes:
  - AWS Glue
  - Amazon S3
  - EMR/Athena/Redshift según uso
- Características avanzadas con cargo basado en uso

#### 22.6.2 Limitaciones
- No soporta manifiestos en consultas desde Athena/Redshift
- Necesidad de permisos IAM para KMS en catálogos encriptados
- Permisos específicos para crear blueprints y workflows

### 22.7 Casos de Uso Ideales

#### 22.7.1 Organizaciones con Requisitos de Seguridad Estrictos
- Necesidad de controles granulares de acceso
- Auditoría detallada
- Encriptación y gestión de claves

#### 22.7.2 Entornos Multi-cuenta o Multi-equipo
- Centralización de permisos
- Compartición segura de datos
- Colaboración entre departamentos

#### 22.7.3 Proyectos con Necesidades de Transformación Complejas
- Limpieza y preparación de datos
- Conversión entre formatos
- Integración de múltiples fuentes de datos

### Resumen para el examen:

- **AWS Lake Formation** simplifica la creación de data lakes seguros basados en S3
- **Construido sobre AWS Glue** para ETL, crawlers y catálogo de datos
- **Proceso de implementación**:
  1. Configuración de IAM y S3
  2. Registro de rutas S3 en Lake Formation
  3. Creación de bases de datos y permisos
  4. Uso de blueprints para workflows
  5. Integración con servicios de análisis
- **Características clave de seguridad**:
  - Control centralizado de acceso
  - Permisos a nivel de columna, fila y celda
  - Acceso entre cuentas vía RAM
  - Integración con IAM y KMS
- **Características avanzadas**:
  - Governed Tables con soporte ACID
  - Compactación automática
  - Control de acceso granular
- **Solución de problemas comunes**:
  - Problemas de acceso entre cuentas → configurar RAM
  - Errores con blueprints/workflows → verificar permisos IAM
  - Problemas con consultas → verificar compatibilidad con manifiestos
  - Acceso a datos encriptados → verificar permisos KMS

AWS Lake Formation es ideal para organizaciones que necesitan un data lake seguro con controles de acceso granulares y capacidades de transformación robustas, simplificando considerablemente la implementación comparado con soluciones manuales.

## 22. AWS Lake Formation

### 22.1 Fundamentos y Propósito

#### 22.1.1 Definición
- Servicio para simplificar la creación y gestión de data lakes seguros
- Construido sobre AWS Glue como base tecnológica
- Promesa de valor: "Establecer un data lake seguro en días"
- Servicio relativamente nuevo (anunciado en 2018, refinado en 2020)

#### 22.1.2 Funciones Principales
- Carga de datos desde fuentes externas
- Monitoreo de flujos de datos
- Configuración de particiones
- Gestión de encriptación y claves
- Transformaciones de datos mediante Glue
- Control de acceso centralizado
- Auditoría de uso y acceso

### 22.2 Arquitectura e Integración

#### 22.2.1 Componentes del Ecosistema
- **S3**: Almacenamiento subyacente del data lake
- **AWS Glue**: Motor para transformaciones, crawlers y catálogo de datos
- **Lake Formation**: Capa de gestión y seguridad
- **Athena/Redshift/EMR**: Servicios de consulta y análisis

#### 22.2.2 Fuentes de Datos Compatibles
- Datos en Amazon S3
- Bases de datos relacionales (RDS, on-premises)
- Fuentes NoSQL
- Datos on-premises
- Streaming a través de Kinesis

#### 22.2.3 Integración con Servicios de Análisis
- **Athena**: Consultas SQL interactivas
- **Redshift/Redshift Spectrum**: Data warehousing
- **EMR**: Procesamiento distribuido
- **QuickSight**: Visualización (a través de los servicios anteriores)

### 22.3 Proceso de Implementación

#### 22.3.1 Pasos para Crear un Data Lake
1. Crear usuario IAM con rol de analista de datos
2. Establecer conexión Glue a las fuentes de datos
3. Crear bucket S3 para el data lake
4. Registrar la ruta S3 en Lake Formation
5. Asignar permisos iniciales
6. Crear base de datos en el catálogo de datos
7. Configurar blueprints y workflows para transformaciones
8. Ejecutar workflows
9. Otorgar permisos de acceso a usuarios y servicios

#### 22.3.2 Blueprints y Workflows
- Templates predefinidos para casos de uso comunes
- Snapshots de bases de datos
- Transformaciones ETL
- Conversión a formatos columnar (Parquet, ORC)
- Limpieza y preparación de datos

### 22.4 Gestión de Permisos y Seguridad

#### 22.4.1 Modelo de Permisos
- Control centralizado desde Lake Formation
- Permisos a nivel de:
  - Base de datos
  - Tabla
  - Columna
  - Fila (nueva característica)
  - Celda (nueva característica)

#### 22.4.2 Acceso Entre Cuentas
- Requiere configurar al destinatario como administrador del data lake
- Utiliza AWS Resource Access Manager (RAM)
- Necesita permisos IAM apropiados
- Gestión de claves KMS para datos encriptados

#### 22.4.3 Interfaz de Gestión de Permisos
- Asignación a usuarios IAM, roles, usuarios SAML o cuentas externas
- Uso opcional de policy tags para clasificación
- Control granular por tabla o columna
- Configuración de permisos específicos (SELECT, INSERT, DELETE, etc.)

### 22.5 Características Avanzadas

#### 22.5.1 Governed Tables
- Soporte para transacciones ACID entre múltiples tablas
- Permite operaciones concurrentes seguras
- Basadas en un nuevo tipo de tabla S3
- La elección de "governed" vs. "estándar" es permanente
- Compatible con datos streaming (Kinesis)
- Integración con Athena para consultas con garantías ACID

#### 22.5.2 Compactación Automática
- Optimiza el almacenamiento automáticamente
- Reduce el overhead que se acumula con el tiempo
- Mejora el rendimiento de las consultas
- Funcionamiento transparente para el usuario

#### 22.5.3 Seguridad Granular
- Control de acceso a nivel de fila
- Control de acceso a nivel de celda
- Disponible tanto para tablas gobernadas como estándar
- Ideal para datos con requisitos estrictos de privacidad

### 22.6 Consideraciones de Costo y Rendimiento

#### 22.6.1 Modelo de Precios
- Lake Formation en sí no tiene costo adicional
- Se paga por servicios subyacentes:
  - AWS Glue
  - Amazon S3
  - EMR/Athena/Redshift según uso
- Características avanzadas con cargo basado en uso

#### 22.6.2 Limitaciones
- No soporta manifiestos en consultas desde Athena/Redshift
- Necesidad de permisos IAM para KMS en catálogos encriptados
- Permisos específicos para crear blueprints y workflows

### 22.7 Casos de Uso Ideales

#### 22.7.1 Organizaciones con Requisitos de Seguridad Estrictos
- Necesidad de controles granulares de acceso
- Auditoría detallada
- Encriptación y gestión de claves

#### 22.7.2 Entornos Multi-cuenta o Multi-equipo
- Centralización de permisos
- Compartición segura de datos
- Colaboración entre departamentos

#### 22.7.3 Proyectos con Necesidades de Transformación Complejas
- Limpieza y preparación de datos
- Conversión entre formatos
- Integración de múltiples fuentes de datos

### Resumen para el examen:

- **AWS Lake Formation** simplifica la creación de data lakes seguros basados en S3
- **Construido sobre AWS Glue** para ETL, crawlers y catálogo de datos
- **Proceso de implementación**:
  1. Configuración de IAM y S3
  2. Registro de rutas S3 en Lake Formation
  3. Creación de bases de datos y permisos
  4. Uso de blueprints para workflows
  5. Integración con servicios de análisis
- **Características clave de seguridad**:
  - Control centralizado de acceso
  - Permisos a nivel de columna, fila y celda
  - Acceso entre cuentas vía RAM
  - Integración con IAM y KMS
- **Características avanzadas**:
  - Governed Tables con soporte ACID
  - Compactación automática
  - Control de acceso granular
- **Solución de problemas comunes**:
  - Problemas de acceso entre cuentas → configurar RAM
  - Errores con blueprints/workflows → verificar permisos IAM
  - Problemas con consultas → verificar compatibilidad con manifiestos
  - Acceso a datos encriptados → verificar permisos KMS

AWS Lake Formation es ideal para organizaciones que necesitan un data lake seguro con controles de acceso granulares y capacidades de transformación robustas, simplificando considerablemente la implementación comparado con soluciones manuales.