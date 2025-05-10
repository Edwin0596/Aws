# Machine Learning Best Practices

## 1. Diseñando Sistemas ML con AWS: Responsible AI

El examen AWS Certified Machine Learning - Associate cubre la arquitectura de sistemas de machine learning, con énfasis en las mejores prácticas definidas por Amazon para el diseño de estos sistemas. Esta sección aborda específicamente el concepto de Responsible AI (IA Responsable).

### 1.1. Dimensiones Fundamentales del Responsible AI

Amazon ha definido varias dimensiones centrales para el desarrollo responsable de aplicaciones de IA:

- **Fairness (Equidad)**: Asegurar que el sistema no presente sesgos no intencionados en sus resultados.

- **Explainability (Explicabilidad)**: Capacidad para entender y explicar por qué el modelo genera determinados resultados, lo que permite identificar y corregir errores.

- **Privacy and Security (Privacidad y Seguridad)**: 
  - Evitar el entrenamiento con información personal identificable (PII) o datos sensibles
  - Implementar barreras de protección para evitar filtraciones de información
  - Garantizar la eliminación de datos sensibles que pudieran introducirse accidentalmente

- **Safety (Seguridad)**: Garantizar que los usuarios se sientan seguros al utilizar el sistema y que éste no presente comportamientos inesperados.

- **Controllability (Controlabilidad)**: Disponer de mecanismos de control sobre el output del sistema, lo que está estrechamente relacionado con la explicabilidad.

- **Veracity and Robustness (Veracidad y Robustez)**: 
  - Asegurar que el sistema proporcione información veraz
  - Medir y controlar la tendencia a "alucinaciones" en modelos generativos
  - Definir límites aceptables de precisión según la aplicación

- **Governance (Gobernanza)**: 
  - Monitorizar el funcionamiento del sistema
  - Comunicar eficazmente su funcionamiento
  - Garantizar el cumplimiento normativo (considerando las diferentes regulaciones por país)

- **Transparency (Transparencia)**: Ser transparente respecto a las capacidades, limitaciones y deficiencias del modelo.

### 1.2. Herramientas AWS para Responsible AI

AWS ofrece diversas herramientas para implementar y mantener prácticas de IA responsable:

#### 1.2.1. Amazon Bedrock

- Proporciona herramientas de evaluación de modelos
- Permite evaluar la precisión de modelos LLM (Large Language Models), generadores de imágenes y otros modelos basados en foundation models
- Facilita la evaluación de respuestas a prompts de prueba, incluso en modelos no determinísticos

#### 1.2.2. SageMaker Clarify

- **Detección de sesgos**:
  - Identifica automáticamente sesgos en datasets de entrenamiento
  - Detecta desequilibrios en características específicas
  - Analiza sesgos en los outputs generados

- **Corrección de sesgos**:
  - Herramientas para reequilibrar datos
  - Capacidad para introducir nuevos datos que corrijan desequilibrios

- **Evaluación continua de modelos**:
  - No solo evaluación puntual sino monitoreo constante
  - Retroalimentación para ajuste continuo del modelo

- **Explicabilidad**:
  - Análisis del impacto de cada característica en los resultados
  - Identificación de las características que más influyen en la decisión final
  - Soporte para optimizar la selección y la ingeniería de características

#### 1.2.3. SageMaker Model Monitor

- Monitorización automática del rendimiento del modelo en producción
- Alertas automáticas ante respuestas imprecisas
- Supervisión continua tras el despliegue

#### 1.2.4. Amazon Augmented AI (A2I)

- Permite integrar revisión humana en el proceso (human-in-the-loop)
- Soporta la verificación humana de resultados antes de su publicación
- Facilita la participación humana en el proceso de entrenamiento
- Compatible con técnicas como Reinforcement Learning from Human Feedback (RLHF)
- Opciones flexibles para equipos de revisión:
  - Equipos propios del cliente
  - Equipos gestionados por AWS

### 1.3. Herramientas para ML Governance en SageMaker

SageMaker ofrece un conjunto de capacidades específicas para la gobernanza de machine learning:

#### 1.3.1. SageMaker Role Manager

- Define roles para grupos de usuarios de SageMaker
- Configura permisos específicos para cada grupo
- Controla qué acciones pueden o no realizar los usuarios

#### 1.3.2. Model Cards

- Documentación de alto nivel sobre los modelos desarrollados
- Contribuyen a la transparencia del sistema
- Incluyen información sobre:
  - Uso previsto del modelo
  - Riesgos potenciales
  - Limitaciones conocidas
  - Información técnica relevante

#### 1.3.3. Model Dashboard

- Proporciona acceso rápido a todos los modelos utilizados en SageMaker
- Facilita la exploración detallada de cada modelo
- Centraliza la gestión y supervisión de modelos

---

## Resumen para el examen

### Responsible AI en AWS
- **Dimensiones clave**: Fairness, Explainability, Privacy/Security, Safety, Controllability, Veracity/Robustness, Governance y Transparency
- **Amazon Bedrock**: Ofrece herramientas para evaluar modelos generativos (LLMs, generadores de imágenes)
- **SageMaker Clarify**: 
  - Detecta y corrige sesgos en datos y outputs
  - Proporciona explicabilidad mostrando qué características influyen más en las decisiones
  - Permite evaluación continua de modelos
- **SageMaker Model Monitor**: Alerta automáticamente sobre respuestas imprecisas
- **Amazon Augmented AI (A2I)**: Integra revisión humana en procesos de ML (human-in-the-loop)
- **Gobernanza ML en SageMaker**:
  - Role Manager: Control de acceso y permisos
  - Model Cards: Documentación transparente sobre modelos (usos, riesgos, limitaciones)
  - Model Dashboard: Acceso centralizado a todos los modelos

Para el examen, es importante conocer estas herramientas y entender cómo contribuyen a las diferentes dimensiones del Responsible AI definidas por AWS, así como saber cuál es la herramienta adecuada para cada aspecto específico de la IA responsable.


## 2. ML Design Principles and Lifecycle

AWS ha definido principios de diseño de alto nivel para sistemas de machine learning. Estos principios se aplican a través de todas las fases del ciclo de vida del machine learning y se traducen en mejores prácticas para cada etapa del diseño.

### 2.1. Principios de Diseño de AWS para Machine Learning

#### 2.1.1. Assign Ownership (Asignar Propiedad)
- Asegurar que cada componente del sistema tenga un responsable designado
- Evitar que los sistemas queden desatendidos y se vuelvan obsoletos
- Garantizar supervisión continua para que los sistemas funcionen según lo previsto

#### 2.1.2. Provide Protection (Proporcionar Protección)
- Implementar controles de seguridad robustos
- Monitorizar quién interactúa con el modelo
- Controlar el flujo de datos de entrada y salida

#### 2.1.3. Enable Resiliency (Habilitar Resiliencia)
- Implementar tolerancia a fallos para todos los componentes
- Asegurar que el sistema se recupere automáticamente ante fallos
- Especialmente importante en procesos de entrenamiento para poder reanudar trabajos interrumpidos sin empezar desde cero

#### 2.1.4. Enable Reusability (Permitir Reutilización)
- Diseñar modelos que puedan reutilizarse en diferentes contextos
- Evitar "reinventar la rueda" debido al alto costo computacional
- Aprovechar modelos ya entrenados cuando sea posible

#### 2.1.5. Enable Reproducibility (Garantizar Reproducibilidad)
- Implementar control de versiones para modelos y datos de entrenamiento
- Permitir reproducir resultados específicos de versiones anteriores
- Documentar cambios en el comportamiento del modelo a lo largo del tiempo

#### 2.1.6. Optimize Resources (Optimizar Recursos)
- Seleccionar hardware eficiente para cada aplicación específica
- Elegir los modelos más eficientes para cada problema
- No utilizar modelos excesivamente grandes cuando uno más pequeño es suficiente

#### 2.1.7. Reduce Cost (Reducir Costos)
- Implementar técnicas para gestionar y reducir costos
- Considerar alternativas más económicas cuando sea posible
- Optimizar el uso de recursos computacionales costosos (como GPUs)

#### 2.1.8. Enable Automation (Habilitar Automatización)
- Implementar procesos de CI/CD (Integración y Despliegue Continuos)
- Desarrollar sistemas de CT (Continuous Training) para actualizar modelos automáticamente
- Automatizar la incorporación de nuevos datos al ciclo de entrenamiento

#### 2.1.9. Monitor Training (Monitorizar Entrenamiento)
- Supervisar el proceso de entrenamiento para detectar problemas
- Identificar posibles sesgos introducidos por nuevos datos
- Evaluar si el entrenamiento continuo produce efectos no deseados

#### 2.1.10. Enable Continuous Improvement (Permitir Mejora Continua)
- Monitorizar y analizar automáticamente el rendimiento del modelo
- Detectar model drift (desviación del modelo) a tiempo
- Identificar cambios negativos no intencionales

#### 2.1.11. Minimize Environmental Impact (Minimizar Impacto Ambiental)
- Considerar la sostenibilidad en el diseño de sistemas ML
- Utilizar servicios gestionados para evitar desperdiciar recursos
- Seleccionar hardware y software energéticamente eficientes
- Evaluar si realmente se necesita IA generativa o si existen enfoques más eficientes

### 2.2. Ciclo de Vida del Machine Learning en AWS

El ciclo de vida del machine learning en AWS comprende varias etapas interconectadas:

#### 2.2.1. Define Business Goal (Definir Objetivo de Negocio)
- Trabajar desde la perspectiva del cliente hacia atrás
- Identificar claramente el impacto deseado
- No comenzar con la tecnología, sino con el problema a resolver

#### 2.2.2. Frame as ML Problem (Formular como Problema de ML)
- Determinar cómo el machine learning puede resolver el objetivo de negocio
- Evaluar si ML es la mejor aproximación o si existen alternativas más eficientes
- Definir el enfoque general para abordar el problema

#### 2.2.3. Collect Data (Recopilar Datos)
- Identificar las fuentes de datos necesarias
- Recopilar datos relevantes para el problema
- Asegurar que los datos sean representativos y suficientes

#### 2.2.4. Process Data (Procesar Datos)
- Extraer la información relevante
- Transformar los datos al formato requerido por el modelo
- Preparar los datos para el entrenamiento

#### 2.2.5. Develop Model (Desarrollar Modelo)
- Proceso iterativo que puede requerir volver a la etapa de procesamiento de datos
- Ajustar el modelo según los resultados iniciales
- Abordar posibles sesgos o desequilibrios en los datos

#### 2.2.6. Deploy (Desplegar)
- Implementar el modelo en un entorno de producción
- Utilizar herramientas como SageMaker o Amazon Bedrock para crear endpoints
- Desarrollar servicios que permitan el uso del modelo en aplicaciones reales

#### 2.2.7. Monitor (Monitorizar)
- Supervisar no solo la disponibilidad del servicio sino también la calidad de los resultados
- Detectar model drift a lo largo del tiempo
- Evaluar si es necesario volver a las etapas de desarrollo o procesamiento de datos

El ciclo es iterativo, permitiendo mejoras continuas y adaptación a nuevos objetivos de negocio.

---

## Resumen para el examen

### Principios de Diseño de ML en AWS
- **Assign Ownership**: Designar responsables para cada componente del sistema ML
- **Provide Protection**: Implementar controles de seguridad para datos y modelos
- **Enable Resiliency**: Garantizar tolerancia a fallos y capacidad de recuperación
- **Enable Reusability**: Diseñar para reutilizar modelos y reducir costos
- **Enable Reproducibility**: Implementar control de versiones para modelos y datos
- **Optimize Resources**: Seleccionar hardware y modelos eficientes según las necesidades
- **Reduce Cost**: Tema explícito del examen - conocer técnicas para gestionar costos
- **Enable Automation**: Implementar CI/CD y Continuous Training (CT)
- **Monitor Training**: Supervisar para evitar sesgos y efectos no deseados
- **Enable Continuous Improvement**: Detectar y corregir model drift
- **Minimize Environmental Impact**: Considerar la sostenibilidad y eficiencia energética

### Ciclo de Vida ML en AWS
- **Define Business Goal** → **Frame as ML Problem** → **Collect Data** → **Process Data** → **Develop Model** → **Deploy** → **Monitor** → (ciclo iterativo)
- El proceso es iterativo: el monitoreo puede llevar a ajustes en el modelo o en los datos
- SageMaker y Amazon Bedrock ofrecen herramientas para implementar cada fase
- Es tan importante la implementación en producción como el desarrollo del modelo

Para el examen, asegúrate de comprender cómo estos principios se aplican a cada fase del ciclo de vida y las mejores prácticas asociadas a cada etapa.


## 3. ML Business Goal Identification

La primera etapa del ciclo de vida de machine learning es identificar el objetivo de negocio. AWS recomienda seguir varios principios fundamentales durante esta fase.

### 3.1. Principios para la Identificación de Objetivos de Negocio

#### 3.1.1. Desarrollar Habilidades con Responsabilidad y Empoderamiento
- Asegurar que el equipo tiene conocimientos suficientes sobre IA generativa
- Establecer responsabilidades claras en el diseño de sistemas
- Empoderar al equipo para tomar decisiones adecuadas

#### 3.1.2. Definir el Nivel de Explicabilidad del Modelo
- Reconocer el equilibrio entre explicabilidad y potencia del modelo
  - Los modelos más complejos son menos explicables pero generalmente ofrecen mejores resultados
  - Es necesario tomar decisiones conscientes sobre este equilibrio
- Utilizar SageMaker Clarify como herramienta para mejorar la explicabilidad
  - Trabaja junto con SageMaker Experiments para determinar la importancia de cada característica
  - Ejecuta experimentos eliminando una característica a la vez para evaluar su impacto en el modelo

#### 3.1.3. Monitorizar el Cumplimiento de Requisitos de Negocio
- Implementar sistemas para detectar desviaciones no deseadas del modelo a lo largo del tiempo
- Asegurar que las métricas de monitorización estén alineadas con los requisitos de negocio
- Ir más allá de métricas técnicas (disponibilidad, precisión) y evaluar si el sistema sigue aportando valor al negocio

#### 3.1.4. Evaluar Consideraciones de Datos, Software y Privacidad
- Identificar las necesidades de datos y software antes de comenzar
- Verificar permisos, licencias y consideraciones éticas:
  - ¿Se tiene permiso para usar los datos y software?
  - ¿Se están respetando derechos de autor?
  - ¿Hay riesgos de filtración de información privada?

#### 3.1.5. Establecer Indicadores Clave de Rendimiento (KPIs)
- Definir cómo se medirá el éxito del sistema
- Enfocarse en métricas de negocio, no solo técnicas
- Establecer un marco para evaluar resultados

#### 3.1.6. Calcular el Retorno de Inversión (ROI)
- Estimar costos de construcción, operación y entrenamiento del sistema a largo plazo
- Evaluar si el valor generado justifica la inversión
- Considerar el costo de oportunidad
  - ¿Qué otras iniciativas podrían aportar más valor con los mismos recursos?

#### 3.1.7. Considerar Servicios Gestionados
- Evaluar el uso de servicios gestionados para reducir el Costo Total de Propiedad (TCO)
  - Ventaja: no pagar por capacidad de cómputo no utilizada
  - Desventaja: posible dependencia del proveedor (vendor lock-in)
- Analizar caso por caso (algunos servicios gestionados pueden ser más costosos en ciertos escenarios)

#### 3.1.8. Evaluar el Impacto Ambiental
- Considerar el consumo energético de los servidores
- Evaluar el uso de recursos como agua para refrigeración
- Ser consciente de que el impacto existe aunque no sea visible directamente
- Intentar minimizar el impacto negativo en el planeta

---

## Resumen para el examen

### Identificación de Objetivos de Negocio en ML
- **Habilidades y responsabilidad**: Asegurar conocimientos adecuados sobre IA y definir responsabilidades claras
- **Explicabilidad vs. potencia**: Reconocer el compromiso entre modelos explicables y potentes; usar SageMaker Clarify para analizar la importancia de características
- **Monitorización alineada con negocio**: Ir más allá de métricas técnicas y evaluar si el sistema aporta valor comercial
- **Consideraciones previas**: Evaluar datos, software, licencias y privacidad antes de comenzar
- **KPIs de negocio**: Definir indicadores claros para medir el éxito desde la perspectiva comercial
- **ROI y costo de oportunidad**: Calcular si la inversión se justifica comparada con otras alternativas
- **Servicios gestionados**: Evaluar su uso para reducir TCO, considerando posibles compromisos
- **Impacto ambiental**: Considerar el consumo energético y de recursos del sistema

Para el examen, es importante entender que la identificación de objetivos de negocio es el primer paso fundamental en el ciclo de vida del machine learning y requiere una evaluación integral que va más allá de los aspectos puramente técnicos, incluyendo consideraciones éticas, financieras y ambientales.


## 4. Framing the ML Problem

Una vez identificado el objetivo de negocio, el siguiente paso en el ciclo de vida del machine learning es enmarcar el problema como un sistema de ML. Esto requiere entender el ciclo de vida completo del machine learning y cómo implementar las mejores prácticas en este contexto.

### 4.1. Ciclo de Vida del Machine Learning

El ciclo de vida del machine learning consiste en varias etapas interconectadas:

#### 4.1.1. Recolección de Datos
- Reunir los datos necesarios para entrenar el modelo
- Para IA generativa/LLMs: textos, imágenes u otros contenidos relevantes
- Centralizar los datos para su posterior procesamiento

#### 4.1.2. Preprocesamiento y Feature Engineering
- Transformar datos crudos en formato adecuado para entrenamiento
- Seleccionar características (features) más valiosas para el modelo
- Aplicar técnicas como one-hot encoding o normalización
- Alimentar datos a Feature Stores para uso posterior

#### 4.1.3. Feature Stores
- **SageMaker Feature Store**: Repositorio para almacenar características
- Dos tipos:
  - **Online Feature Store**: Para inferencia en tiempo real
  - **Offline Feature Store**: Para entrenamiento y evaluación

#### 4.1.4. Entrenamiento, Ajuste y Evaluación
- Entrenar el modelo con los datos preparados
- Ajustar (fine-tuning) para optimizar rendimiento
- Evaluar resultados y refinar iterativamente
- Alimentar modelos entrenados al Model Registry

#### 4.1.5. Model Registry
- Almacenar versiones de modelos
- Documentar evolución del modelo
- Facilitar reproducibilidad y versionado

#### 4.1.6. Despliegue
- Implementar modelo en producción
- Utilizar modelos del Model Registry
- Potencialmente integrar datos de Feature Stores

#### 4.1.7. Inferencia en Tiempo Real
- Servir predicciones a aplicaciones en producción
- Monitorizar resultados para calidad y rendimiento

#### 4.1.8. Monitorización y Feedback
- Supervisar calidad y comportamiento del modelo
- Configurar alarmas para detectar problemas
- Implementar bucles de retroalimentación:
  - **Performance Feedback**: Ajustar modelo según resultados
  - **Active Learning**: Incorporar nuevos datos generados por usuarios

### 4.2. Mejores Prácticas para Enmarcar Problemas ML

#### 4.2.1. Establecer Roles y Responsabilidades
- Utilizar **SageMaker Role Manager** para definir quién puede:
  - Entrenar modelos
  - Ajustar parámetros
  - Desplegar soluciones

#### 4.2.2. Preparar Templates de Perfiles ML
- Documentar recursos necesarios para construir el modelo

#### 4.2.3. Establecer Estrategias de Mejora de Modelos
- **SageMaker Experiments**: Probar diferentes modelos y medir impacto
- **Automatic Hyperparameter Optimization**: Automatizar búsqueda de parámetros óptimos
- **AutoML**: Selección y ajuste automático de modelos

#### 4.2.4. Implementar Sistemas de Seguimiento
- **SageMaker Lineage Tracking**: Seguimiento de etapas en pipeline ML
- **SageMaker Pipelines**: Definición de procesos en el sistema ML
- **SageMaker Studio**: IDE para experimentación y desarrollo
- **SageMaker Feature Store**: Seguimiento de características y su evolución
- **SageMaker Model Registry**: Repositorio de modelos entrenados

#### 4.2.5. Establecer Bucles de Retroalimentación
- **SageMaker Model Monitor**: Supervisar output y detectar drift
- **CloudWatch**: Monitorizar aspectos operacionales
- **Amazon Augmented AI (A2I)**: Incorporar evaluación humana continua

#### 4.2.6. Revisar Fairness y Explicabilidad
- **SageMaker Clarify**: Detectar y abordar sesgos en modelos

#### 4.2.7. Diseñar Encriptación y Ofuscación de Datos
- **AWS Glue DataBrew**: Preprocesar datos, detectar y enmascarar PII

#### 4.2.8. Usar APIs para Abstraer Cambios
- Combinar **SageMaker** con **API Gateway** para:
  - Crear interfaces estables para aplicaciones
  - Permitir cambios en modelos sin afectar aplicaciones consumidoras

#### 4.2.9. Adoptar Estrategia de Microservicios para ML
- **AWS Lambda** y **Fargate**: Desplegar servicios complementarios

#### 4.2.10. Utilizar Servicios ML Específicos
- **SageMaker Jumpstart**: Punto de partida para modelos y soluciones específicas
- **AWS Marketplace**: Soluciones prediseñadas

#### 4.2.11. Definir Métricas de Evaluación
- Establecer cómo medir éxito del sistema
- Evaluar si ML es la solución adecuada para el problema

#### 4.2.12. Control de Costos
- Analizar costo-beneficio de modelos personalizados vs. pre-entrenados
- Evaluar servicios existentes antes de construir desde cero

#### 4.2.13. Sostenibilidad
- Considerar servicios de IA y modelos pre-entrenados para reducir huella ambiental
- Seleccionar regiones con fuentes de energía más sostenibles:
  - Ejemplo: Costa Oeste de EE.UU. (más energía hidroeléctrica) vs. Costa Este

---

## Resumen para el examen

### Ciclo de Vida ML en AWS
- **Etapas clave**: Recolección de datos → Preprocesamiento → Entrenamiento/Ajuste/Evaluación → Despliegue → Inferencia → Monitorización → Retroalimentación
- **Feature Stores**: Online (inferencia en tiempo real) y Offline (entrenamiento)
- **Bucles de retroalimentación**: Performance Feedback y Active Learning

### Herramientas AWS para Framing ML
- **Gestión de roles**: SageMaker Role Manager
- **Experimentación y mejora**: SageMaker Experiments, Hyperparameter Optimization, AutoML
- **Seguimiento y versionado**: Lineage Tracking, Pipelines, Model Registry
- **Monitorización**: Model Monitor (calidad), CloudWatch (operaciones), A2I (evaluación humana)
- **Calidad y equidad**: SageMaker Clarify (sesgo y explicabilidad)
- **Protección de datos**: AWS Glue DataBrew (PII)
- **Implementación**: API Gateway + SageMaker, Lambda, Fargate

### Control de Costos y Sostenibilidad
- **Punto clave para el examen**: AWS evalúa conocimiento sobre control de costos en ML
- **Estrategias**:
  - Priorizar modelos pre-entrenados sobre entrenamiento desde cero
  - Utilizar servicios existentes (SageMaker Jumpstart, AWS Marketplace)
  - Seleccionar regiones con energía renovable para cargas de trabajo intensivas

Para el examen, es importante entender cómo las diferentes herramientas de AWS se aplican a cada fase del ciclo de vida ML y cómo contribuyen tanto a la eficiencia técnica como a la optimización de costos y sostenibilidad.


## 5. Data Processing

El procesamiento de datos es una etapa fundamental en el ciclo de vida del machine learning. Comprende tres fases principales: recolección de datos, preprocesamiento y feature engineering, siendo estas dos últimas parte de la preparación de datos.

### 5.1. Etapas del Procesamiento de Datos

#### 5.1.1. Data Collection (Recolección de Datos)
- **Labeling (Etiquetado)**: Identificar si los datos necesitan etiquetas y cómo obtenerlas
- **Ingestion (Ingestión)**: Determinar cómo los datos llegarán al sistema
  - Métodos de streaming vs. procesamiento por lotes
  - Almacenamiento en S3 u otros repositorios
- **Aggregation (Agregación)**: Combinar datos de múltiples fuentes
  - Decidir si mantener archivos separados o consolidarlos

#### 5.1.2. Data Preparation (Preparación de Datos)
- **Cleaning (Limpieza)**:
  - Manejar datos faltantes
  - Identificar y tratar outliers (valores atípicos)
- **Partitioning (Particionamiento)**:
  - Dividir datos voluminosos para acceso eficiente
  - Organizar por fecha, características u otras dimensiones
- **Scaling (Escalabilidad)**:
  - Evaluar necesidades de procesamiento distribuido
  - Considerar sistemas como Apache Spark para grandes volúmenes
- **Unbiasing and Balancing (Eliminación de sesgos y equilibrado)**:
  - Identificar sesgos ocultos en los datos
  - Equilibrar la distribución entre categorías relevantes
- **Augmentation (Aumento)**:
  - Incorporar datos adicionales cuando sea necesario
  - Complementar para corregir desequilibrios o mejorar representatividad

#### 5.1.3. Feature Engineering (Ingeniería de Características)
- **Feature Selection (Selección de características)**:
  - Identificar las características más relevantes para el modelo
- **Feature Transformation (Transformación de características)**:
  - Normalización
  - One-hot encoding
  - Adaptación al formato requerido por el modelo
- **Feature Creation (Creación de características)**:
  - Generar nuevas características a partir de las existentes
  - Transformar datos (ej. aplicar funciones matemáticas)
  - Extraer información específica de campos complejos

### 5.2. Mejores Prácticas para Procesamiento de Datos

#### 5.2.1. Perfilar Datos para Mejorar Calidad
- **Herramientas AWS**:
  - SageMaker Data Wrangler
  - AWS Glue
  - Amazon Athena
  - Amazon Redshift
  - Amazon QuickSight

#### 5.2.2. Crear Mecanismos de Seguimiento y Control de Versiones
- **SageMaker Model Registry**: Seguimiento de modelos que consumen los datos
- **Git**: Control de versiones para notebooks de procesamiento
- **SageMaker Experiments**: Registro de diferentes conjuntos de datos y su evolución

#### 5.2.3. Asegurar Acceso con Privilegio Mínimo
- Principio: comenzar sin permisos y agregar solo los necesarios
- No otorgar permisos innecesarios

#### 5.2.4. Garantizar Entornos de Procesamiento Seguros
- Utilizar entornos cloud para mayor control de seguridad:
  - SageMaker
  - EMR
  - Athena
- **Herramientas de seguridad**:
  - IAM
  - KMS
  - Secrets Manager
  - VPCs
  - PrivateLink (para comunicación segura entre servicios)

#### 5.2.5. Proteger Privacidad de Datos Sensibles
- **Amazon Macie**: Detección automática de datos sensibles

#### 5.2.6. Aplicar Linaje de Datos
- **SageMaker ML Lineage Tracker**: Seguimiento de la evolución de los datos

#### 5.2.7. Conservar Solo Datos Relevantes
- Eliminar datos innecesarios, especialmente información personal identificable (PII)
- **Herramientas adicionales**:
  - Amazon Comprehend
  - Amazon Transcribe
  - Amazon Athena (para consultar y eliminar columnas innecesarias)

#### 5.2.8. Utilizar Catálogo de Datos
- **AWS Glue**: Mantenimiento de esquemas para datos no estructurados

#### 5.2.9. Emplear Pipeline de Datos
- **SageMaker Pipelines**: Estructuración del procesamiento de datos

#### 5.2.10. Automatizar Gestión de Cambios en Datos
- Implementar MLOps para gestión de cambios

#### 5.2.11. Utilizar Arquitectura de Datos Moderna
- Considerar data lakes u otras arquitecturas según necesidades específicas
- Evaluar la arquitectura más adecuada para cada tipo de datos

#### 5.2.12. Usar Etiquetado de Datos Gestionado
- **Amazon SageMaker Ground Truth**: Automatización del proceso de etiquetado

#### 5.2.13. Emplear Data Wrangler para Análisis Interactivo
- **SageMaker Data Wrangler**: Transformación de datos con interfaz gráfica

#### 5.2.14. Utilizar Capacidades Gestionadas de Procesamiento
- **SageMaker**: Procesamiento de datos integrado

#### 5.2.15. Habilitar Reutilización de Características
- **SageMaker Feature Store**: Almacenamiento y accesibilidad de características para múltiples modelos y aplicaciones

### 5.3. Sostenibilidad en Procesamiento de Datos

#### 5.3.1. Minimizar Recursos Inactivos
- Utilizar servicios gestionados para optimizar uso de recursos

#### 5.3.2. Implementar Políticas de Ciclo de Vida
- Eliminar datos innecesarios u obsoletos
- Reducir costos de almacenamiento y consumo energético

#### 5.3.3. Adoptar Opciones de Almacenamiento Sostenibles
- Utilizar tecnologías de almacenamiento eficientes energéticamente
- Considerar servicios como Amazon S3 Glacier para datos de acceso infrecuente
- Eliminar datos que ya no son necesarios

---

## Resumen para el examen

### Etapas del Procesamiento de Datos en ML
- **Data Collection**: Etiquetado, ingestión y agregación de datos
- **Data Preparation**: Limpieza, particionamiento, escalabilidad, eliminación de sesgos y aumento de datos
- **Feature Engineering**: Selección, transformación y creación de características

### Herramientas AWS Clave para Procesamiento de Datos
- **Perfilado y análisis**: SageMaker Data Wrangler, AWS Glue, Athena, Redshift, QuickSight
- **Seguimiento y versionado**: SageMaker Model Registry, SageMaker Experiments
- **Seguridad**: IAM, KMS, VPC, PrivateLink
- **Detección de datos sensibles**: Amazon Macie, Comprehend, Transcribe
- **Etiquetado**: SageMaker Ground Truth
- **Linaje de datos**: SageMaker ML Lineage Tracker
- **Pipeline de datos**: SageMaker Pipelines
- **Almacenamiento de características**: SageMaker Feature Store

### Mejores Prácticas Fundamentales
- Aplicar acceso con privilegio mínimo
- Eliminar datos innecesarios, especialmente PII
- Utilizar catálogos y pipelines de datos estructurados
- Implementar seguimiento de linaje de datos
- Emplear Feature Store para reutilización de características

### Sostenibilidad
- Minimizar recursos inactivos mediante servicios gestionados
- Implementar políticas de ciclo de vida de datos
- Seleccionar opciones de almacenamiento energéticamente eficientes (ej. Glacier)

Para el examen, es importante recordar qué herramientas de AWS corresponden a cada aspecto del procesamiento de datos y cómo estas herramientas contribuyen tanto a la calidad y seguridad de los datos como a la sostenibilidad y eficiencia de costos.


## 6. Model Development

El desarrollo de modelos comprende las etapas de entrenamiento, ajuste y evaluación en el ciclo de vida del machine learning. Este proceso involucra múltiples subetapas y requiere aplicar mejores prácticas específicas para garantizar eficiencia, calidad y sostenibilidad.

### 6.1. Proceso de Desarrollo de Modelos

#### 6.1.1. Selección de Algoritmo
- Identificar el algoritmo más adecuado para el problema
- Evaluar algoritmos de acuerdo a la naturaleza de los datos y objetivo

#### 6.1.2. Entrenamiento
- Ingerir características identificadas durante feature engineering
- Utilizar bibliotecas especializadas para optimizar el proceso:
  - **SageMaker Data Parallel**: Procesamiento paralelo de datos para conjuntos grandes
  - **SageMaker Model Parallel**: Distribución de modelos demasiado grandes para un solo GPU

#### 6.1.3. Depuración y Perfilado
- Identificar problemas durante el entrenamiento
- Analizar métricas de rendimiento

#### 6.1.4. Ajuste de Hiperparámetros
- Optimizar configuraciones para mejorar resultados
- Validar métricas del modelo

#### 6.1.5. Visualización
- Utilizar herramientas como QuickSight para análisis visual
- Monitorizar progreso durante iteraciones

#### 6.1.6. Contenedor de Entrenamiento
- Encapsular código de entrenamiento
- Facilitar entrenamientos continuos con nuevos datos

### 6.2. Mejores Prácticas para Desarrollo de Modelos

#### 6.2.1. Automatizar Operaciones con MLOps y CI/CD
- **Herramientas AWS**:
  - CloudFormation
  - CDK (Cloud Development Kit)
  - SageMaker Pipelines
  - Amazon Step Functions

#### 6.2.2. Establecer Patrones de Empaquetado Confiables
- **Elastic Container Registry (ECR)**: Gestión de imágenes
- **CodeArtifact**: Control de acceso a bibliotecas

#### 6.2.3. Asegurar Entorno ML Gobernado
- Proteger comunicaciones entre nodos del clúster
- **Herramientas**:
  - Opción de encriptación internode de SageMaker
  - Encriptación en tránsito de Elastic MapReduce

#### 6.2.4. Proteger contra Amenazas de Data Poisoning
- **SageMaker Clarify**: Identificar sesgos que puedan indicar envenenamiento de datos
- **SageMaker Model Registry**: Capacidad de rollback rápido a versiones anteriores
- **SageMaker Feature Store**: Acceso a conjuntos de características previas no afectadas

#### 6.2.5. Habilitar CI/CD y Automatización de Entrenamiento con Trazabilidad
- Implementar frameworks MLOps
- **SageMaker Pipelines**: Automatización de procesos de entrenamiento

#### 6.2.6. Asegurar Consistencia de Características
- **SageMaker Feature Store**: Mantener consistencia entre entrenamiento e inferencia

#### 6.2.7. Validar Modelos con Datos Relevantes
- **SageMaker Experiments**: Realizar pruebas controladas
- **SageMaker Model Monitor**: Supervisar comportamiento del modelo

#### 6.2.8. Detectar y Mitigar Sesgos
- **SageMaker Clarify**: Identificación y mitigación de sesgos en datos

#### 6.2.9. Optimizar Tipos de Instancias
- Seleccionar tamaños adecuados para entrenamiento e inferencia
- Considerar enfoque paralelo solo cuando sea necesario
- Probar distintas alternativas para mejoras de rendimiento

#### 6.2.10. Establecer Pipeline de Evaluación de Rendimiento
- **SageMaker Pipelines**: Automatizar evaluación
- **Model Registry**: Gestionar versiones y resultados

#### 6.2.11. Establecer Estadísticas de Características
- **Model Monitor**: Seguimiento de métricas importantes
- **Data Wrangler**: Análisis de características
- **SageMaker Clarify**: Detección de sesgos
- **SageMaker Experiments**: Evaluar impacto de características

#### 6.2.12. Considerar Trade-offs
- **Precisión vs. Complejidad**: Balancear niveles de precisión con costos
- **Sesgo vs. Equidad**: Asegurar que el modelo sea justo
- **Precision vs. Recall**: Optimizar según necesidades específicas

#### 6.2.13. Detectar Problemas de Rendimiento en Transfer Learning
- **SageMaker Debugger**: Diagnosticar problemas en modelos adaptados

#### 6.2.14. Seleccionar Tamaño Óptimo de Instancia de Cómputo
- Evitar sobreaprovisionar recursos
- Utilizar solo la potencia necesaria

#### 6.2.15. Usar Entornos de Construcción Gestionados
- **Entrenamiento local**: Para experimentos a pequeña escala
- Minimizar uso de recursos cloud para pruebas iniciales

#### 6.2.16. Seleccionar Framework ML Óptimo
- Evaluar **PyTorch** vs **TensorFlow** según necesidades específicas
- Considerar **scikit-learn** para algoritmos clásicos más simples
- Valorar compatibilidad con bibliotecas de procesamiento paralelo

#### 6.2.17. Utilizar Automated Machine Learning
- **SageMaker Autopilot**: Optimización automática de modelos

#### 6.2.18. Usar Capacidades de Entrenamiento Gestionadas
- **SageMaker Managed Spot Instances**: Optimización de costos
- **Entrenamiento distribuido**: Solo cuando sea necesario

### 6.3. Gestión de Costos y Sostenibilidad

#### 6.3.1. Detener Recursos Cuando No Se Usan
- Configurar alarmas de facturación
- **SageMaker Lifecycle Configuration**: Automatizar ciclo de recursos
- **SageMaker Studio Auto-shutdown**: Evitar costos innecesarios

#### 6.3.2. Iniciar Entrenamiento con Conjuntos Pequeños
- Validar enfoques con datos reducidos antes de escalar

#### 6.3.3. Utilizar Warm Start y Checkpointing
- **SageMaker Warm Pools**: Reutilizar hardware entre entrenamientos
- **Checkpointing**: Guardar estados intermedios para recuperación

#### 6.3.4. Implementar Optimización Automática de Modelos
- **SageMaker Automatic Model Tuning**: Optimización eficiente de hiperparámetros

#### 6.3.5. Gestionar Costos
- **AWS Budgets**: Establecer límites de gasto
- **Cost Explorer**: Analizar y entender patrones de gasto

#### 6.3.6. Habilitar Proximidad entre Datos y Cómputo
- Minimizar latencia colocando datos cerca de recursos de procesamiento

#### 6.3.7. Seleccionar Algoritmos Óptimos
- Considerar alternativas más simples a deep learning cuando sea posible
- Evaluar si un algoritmo clásico puede ofrecer resultados satisfactorios

#### 6.3.8. Habilitar Depuración y Logging
- **SageMaker Debugger**: Identificar problemas de entrenamiento
- **CloudWatch**: Monitorizar rendimiento y errores

#### 6.3.9. Definir Criterios de Rendimiento Sostenibles
- Determinar nivel de precisión "suficientemente bueno"
- Evitar sobreentrenamiento con recursos excesivos

#### 6.3.10. Utilizar Early Stopping
- Detener entrenamiento cuando se alcancen criterios de éxito
- Evitar ciclos de entrenamiento innecesarios

#### 6.3.11. Seleccionar Algoritmos Energéticamente Eficientes
- Evaluar alternativas como XGBoost o regresión antes de recurrir a deep learning
- Priorizar modelos más pequeños cuando sea posible

#### 6.3.12. Archivar o Eliminar Artefactos Innecesarios
- Gestionar datos de entrenamiento procesados
- **SageMaker Experiments**: Organizar y gestionar datos de experimentos

#### 6.3.13. Usar Métodos Eficientes de Ajuste de Modelos
- Preferir métodos **Bayesianos** o **Hyperband** sobre búsqueda aleatoria o grid search
- Aprender de resultados previos

#### 6.3.14. Limitar Jobs de Entrenamiento Concurrentes
- Evitar sobrecarga por exceso de paralelismo
- Ajustar solo los hiperparámetros más importantes para reducir combinaciones

---

## Resumen para el examen

### Proceso de Desarrollo de Modelos
- **Etapas clave**: Selección de algoritmo → Entrenamiento → Depuración/Perfilado → Ajuste de hiperparámetros → Evaluación
- **SageMaker Parallel**: Data Parallel (datos grandes) vs Model Parallel (modelos grandes que no caben en un GPU)

### Herramientas AWS para Desarrollo de Modelos
- **Automatización**: SageMaker Pipelines, Step Functions, CloudFormation, CDK
- **Seguridad**: Encriptación internode, ECR, CodeArtifact
- **Protección contra data poisoning**: SageMaker Clarify, Model Registry, Feature Store
- **Consistencia**: Feature Store (mismo conjunto de características en entrenamiento e inferencia)
- **Validación**: SageMaker Experiments, Model Monitor
- **Detección de sesgos**: SageMaker Clarify
- **Diagnóstico**: SageMaker Debugger
- **Optimización automática**: SageMaker Autopilot, Automatic Model Tuning

### Prácticas Críticas para Gestión de Costos
- **¡IMPORTANTE PARA EL EXAMEN!**: AWS evalúa conocimiento sobre control de costos en ML
- **Estrategias clave**:
  - Configurar alarmas de facturación y AWS Budgets
  - Utilizar SageMaker Studio Auto-shutdown
  - Implementar warm start y checkpointing
  - Iniciar con conjuntos de datos pequeños
  - Seleccionar algoritmos eficientes (¿es necesario deep learning?)
  - Utilizar early stopping
  - Optimizar hiperparámetros con métodos Bayesianos/Hyperband

### Trade-offs a Considerar
- **Precisión vs Complejidad**: Mayor precisión suele requerir modelos más complejos y costosos
- **Precision vs Recall**: Optimizar según prioridades del caso de uso
- **Paralelismo**: Solo cuando es necesario, tiene overhead
- **Transfer Learning vs Entrenamiento desde cero**: Adapt modelos pre-entrenados cuando sea posible

Para el examen, es fundamental entender las herramientas de AWS para cada fase del desarrollo de modelos y especialmente las estrategias para optimización de costos y sostenibilidad, que son temas explícitamente evaluados.


## 7. Monitoring

La fase de monitorización es la etapa final del ciclo de vida del machine learning, pero no por ello menos importante. AWS proporciona numerosas herramientas y mejores prácticas para garantizar que los modelos se comporten según lo esperado una vez desplegados.

### 7.1. Herramientas Fundamentales para Monitorización

#### 7.1.1. Observabilidad y Seguimiento de Modelos
- **SageMaker Model Monitor**: Supervisión específica del comportamiento del modelo
- **CloudWatch**: Monitorización de los sistemas subyacentes
- **SageMaker Clarify**: Detección de sesgos y explicabilidad
- **Model Cards**: Documentación de gobernanza y visibilidad
- **SageMaker Lineage Tracking**: Seguimiento del origen de datos y modelos

### 7.2. Mejores Prácticas para Monitorización

#### 7.2.1. Sincronizar Arquitectura y Configuración
- Verificar coherencia entre entornos
- Detectar desviaciones (skew) en configuraciones
- **Herramientas AWS**:
  - CloudFormation: Definición de arquitectura
  - Model Monitor: Detección de desviaciones

#### 7.2.2. Asegurar Endpoints de Inferencia
- Garantizar que solo los usuarios autorizados accedan a los modelos
- Monitorizar interacciones humanas con los datos
- **Herramientas AWS**:
  - Logging: Registro de actividades
  - Amazon GuardDuty: Detección de actividades anómalas
  - Amazon Macie: Identificación de uso inadecuado de información sensible

#### 7.2.3. Permitir Escalado Automático
- Implementar ajuste dinámico según la demanda
- Reducir necesidad de intervención manual
- **Herramientas AWS**:
  - Automatic Scaling: Ajuste automático de capacidad
  - Elastic Inference: Optimización de recursos de inferencia

#### 7.2.4. Garantizar Recuperabilidad con Control de Versiones
- Implementar capacidad de rollback a versiones anteriores
- Mantener historial de versiones
- **Herramientas AWS**:
  - ECR (Elastic Container Registry): Almacenamiento de imágenes
  - CloudFormation: Definición de infraestructura
  - CodeCommit: Control de versiones de código
  - SageMaker Pipelines y Projects: Gestión de flujos de trabajo

#### 7.2.5. Evaluar Explicabilidad del Modelo
- Entender qué características influyen más en las decisiones
- Monitorizar cambios en la importancia de características
- **Herramientas AWS**:
  - SageMaker Clarify: Análisis de impacto de características

#### 7.2.6. Evaluar Data Drift
- Detectar y gestionar cambios en los patrones de datos
- Identificar sesgos emergentes
- **Herramientas AWS**:
  - SageMaker Clarify: Detección de sesgos
  - Model Monitor: Supervisión de cambios
  - OpenSearch: Visualización de tendencias

#### 7.2.7. Implementar Framework de Reentrenamiento Automatizado
- Automatizar proceso de reentrenamiento con nuevos datos
- **Herramientas AWS**:
  - SageMaker Pipelines: Automatización de flujos de trabajo
  - AWS Step Functions: Orquestación de procesos
  - Jenkins: Integración continua

#### 7.2.8. Revisar Datos y Características Actualizadas
- Evaluar cambios en la naturaleza de los datos entrantes
- **Herramientas AWS**:
  - SageMaker Data Wrangler: Exploración y análisis de datos

#### 7.2.9. Introducir Supervisión Humana
- Complementar métricas técnicas con evaluación humana
- Especialmente útil para sistemas AI conversacionales o generativos
- **Herramientas AWS**:
  - Amazon Augmented AI (A2I): Integración de evaluación humana
    - Opción de equipos propios
    - Opción de equipos gestionados por Amazon

#### 7.2.10. Monitorizar Uso y Costos
- Seguimiento detallado de gastos por actividad
- **Herramientas AWS**:
  - Tagging: Etiquetado de recursos para análisis de costos
  - Budgets: Gestión de presupuestos

#### 7.2.11. Monitorizar Retorno de Inversión
- Evaluar si el valor generado justifica los costos
- **Herramientas AWS**:
  - Amazon QuickSight: Visualización de métricas de negocio

#### 7.2.12. Monitorizar Uso de Endpoints
- Ajustar tamaño de flota según necesidades reales
- **Herramientas AWS**:
  - CloudWatch: Monitorización de uso
  - SageMaker Autoscaling: Ajuste automático de recursos

#### 7.2.13. Optimizar Almacenamiento para Entrenamiento a Gran Escala
- **Amazon FSX for Lustre**: Alternativa a S3 para entrenamiento a gran escala
  - Mayor ancho de banda
  - Recomendado para modelos con trillones de parámetros

### 7.3. Gestión de Eficiencia y Sostenibilidad

#### 7.3.1. Medir Eficiencia Material
- **Definición**: Recursos aprovisionados ÷ Resultados de negocio
- Evaluar si los costos son proporcionales al valor generado
- Métrica fundamental para justificar inversión en ML

#### 7.3.2. Optimizar Frecuencia de Reentrenamiento
- Reentrenar solo cuando sea necesario
- Definir umbrales de precisión aceptables
- Iniciar reentrenamiento solo cuando el modelo deja de cumplir objetivos de negocio
- **Herramientas AWS**:
  - SageMaker Model Monitor: Detección de degradación de rendimiento
  - SageMaker Pipelines: Automatización de flujos de reentrenamiento
  - AWS Step Functions Data Science SDK: Orquestación de procesos

---

## Resumen para el examen

### Herramientas Clave para Monitorización ML
- **SageMaker Model Monitor**: Supervisión específica del comportamiento del modelo
- **CloudWatch**: Monitorización de infraestructura y métricas operativas
- **SageMaker Clarify**: Detección de sesgos y explicabilidad
- **SageMaker Lineage Tracking**: Seguimiento del flujo de datos y modelos
- **Amazon A2I**: Integración de evaluación humana

### Aspectos Críticos de Monitorización
- **Seguridad**: Asegurar endpoints con logging, GuardDuty y Macie
- **Escalabilidad**: Implementar auto-scaling para ajuste dinámico
- **Recuperabilidad**: Mantener control de versiones con ECR, CloudFormation y CodeCommit
- **Data Drift**: Detectar cambios en patrones de datos con Clarify y Model Monitor
- **Reentrenamiento**: Automatizar con SageMaker Pipelines y Step Functions

### Optimización de Costos y Eficiencia
- **Eficiencia Material**: Recursos aprovisionados ÷ Resultados de negocio (métrica crucial)
- **Estrategias de Optimización**:
  - Reentrenar solo cuando sea necesario (no periódicamente)
  - Dimensionar correctamente la flota de instancias
  - Considerar Amazon FSX for Lustre para entrenamiento a gran escala
  - Establecer presupuestos y monitorizar gastos con tagging

### Integración con Objetivos de Negocio
- Monitorizar ROI y valor generado, no solo métricas técnicas
- Usar QuickSight para visualizar impacto en objetivos de negocio
- Establecer umbrales de precisión aceptables basados en requisitos de negocio

Para el examen, es importante entender la interrelación entre las herramientas de monitorización y cómo cada una contribuye a diferentes aspectos de la supervisión del modelo. También es fundamental comprender cómo la monitorización puede optimizar costos y mejorar la eficiencia general del sistema ML.