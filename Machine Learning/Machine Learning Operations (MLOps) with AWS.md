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

