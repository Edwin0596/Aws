# Security, Identity, and Compliance

## 1. Principle of Least Privilege

El **Principle of Least Privilege** es un concepto fundamental en seguridad que establece que sólo se deben otorgar los permisos mínimos necesarios para que un usuario o servicio pueda realizar sus tareas específicas. La guía del examen AWS Certified Machine Learning - Associate menciona explícitamente que debes conocer este principio.

### 1.1 Conceptos clave:

- **Definición**: Otorgar únicamente los permisos requeridos para que alguien pueda realizar una tarea específica.
- **Objetivo**: Reducir la superficie de ataque limitando los permisos que podrían ser explotados.
- **Enfoque práctico**: 
  - Durante el desarrollo, es común comenzar con permisos más amplios
  - Una vez finalizado el desarrollo, se deben restringir los permisos al mínimo necesario

### 1.2 Ejemplo práctico:

A continuación se muestra un ejemplo de una política IAM que implementa el Principle of Least Privilege para acceso a S3:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "s3:ListBucket",
      "Resource": "arn:aws:s3:::my-specific-bucket",
      "Condition": {
        "StringLike": {
          "s3:prefix": "data/reports/*"
        }
      }
    },
    {
      "Effect": "Allow",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::my-specific-bucket/data/reports/*.csv"
    }
  ]
}
```

Este ejemplo de política IAM:
1. Permite solo listar objetos en un bucket específico y únicamente dentro del prefijo "data/reports/"
2. Permite solo leer archivos que terminen en ".csv" dentro de ese prefijo específico
3. No otorga permisos para modificar, eliminar o acceder a otros archivos o ubicaciones

### 1.3 Herramientas de ayuda:

**IAM Access Analyzer**: Esta herramienta de AWS puede generar automáticamente políticas de privilegio mínimo basadas en la actividad de acceso observada durante el período de pruebas.

### 1.4 Recomendaciones:
- Si un workflow solo necesita leer archivos CSV de una ubicación específica, limita el acceso solo a esa ubicación y a ese tipo de archivos
- Utiliza IAM Access Analyzer para ayudarte a identificar y crear políticas de privilegio mínimo

---

### 📝 Resumen para el examen:

- El **Principle of Least Privilege** es un concepto de seguridad fundamental que aparece en el examen AWS ML Associate
- Consiste en otorgar únicamente los permisos mínimos necesarios para realizar una tarea
- En la práctica, durante el desarrollo se pueden usar permisos más amplios, pero en producción deben limitarse al mínimo
- Las políticas IAM permiten definir permisos granulares (por bucket, prefijo, tipo de archivo, etc.)
- AWS ofrece herramientas como **IAM Access Analyzer** para facilitar la creación de políticas basadas en el privilegio mínimo
- Recuerda que implementar este principio reduce la superficie de ataque y mejora la seguridad general de tu solución

## 2. Data Masking and Anonymization

La protección de datos sensibles es un concepto fundamental en seguridad que aparece reiteradamente en el examen AWS ML Associate. Revisaremos técnicas para proteger información sensible como datos personales identificables (PII).

### 2.1 Data Masking

Es una técnica de obfuscación de datos que oculta parcialmente información sensible mientras mantiene cierta utilidad de los datos.

#### 2.1.1 Ejemplos comunes:
- **Números de tarjeta de crédito**: Mostrar solo los últimos 4 dígitos (e.g., •••• •••• •••• 1234)
- **Números de Seguridad Social**: Mostrar solo los últimos 4 dígitos (e.g., •••-••-1234)
- **Contraseñas**: Reemplazar cada carácter por un símbolo (e.g., ********)

#### 2.1.2 Servicios AWS con soporte nativo:
- **AWS Glue DataBrew**
- **Amazon Redshift**

#### 2.1.3 Ejemplo de política de masking en Redshift:

```sql
CREATE MASKING POLICY masked_credit_card_full
WITH (credit_card VARCHAR(256))
USING ('********');
```

Este ejemplo crea una política que reemplaza completamente el número de tarjeta de crédito con asteriscos.

### 2.2 Técnicas de Anonymization

La anonimización va más allá del enmascaramiento, haciendo que los datos no sean rastreables a su origen.

| Técnica | Descripción | Aplicación |
|---------|-------------|------------|
| **Reemplazo** | Sustituir datos con información aleatoria | Reemplazar IDs con valores aleatorios |
| **Shuffling** | Mezclar valores en una columna | Desconectar datos de usuario de sus valores reales |
| **Encryption** | Cifrar datos usando algoritmos | Proteger datos manteniendo posibilidad de recuperación |
| **Hashing** | Aplicar función hash a los datos | Convertir datos a valores no reversibles |
| **Eliminación** | Borrar completamente los datos sensibles | La técnica más segura cuando los datos no son necesarios |

#### 2.2.1 Tipos de encriptación:
- **Determinística**: La misma entrada siempre produce la misma salida cifrada
- **Probabilística**: La misma entrada puede producir diferentes salidas cifradas

#### 2.2.2 Consideraciones sobre hashing:
- Múltiples entradas pueden generar el mismo valor hash (colisiones)
- Las colisiones aumentan el nivel de anonimización
- No es reversible (a diferencia de la encriptación)

### 2.3 Mejor práctica de seguridad

La estrategia más segura para datos sensibles es:
- **No importar** datos sensibles innecesarios
- **Eliminar** datos sensibles durante el proceso ETL
- Aplicar el principio de "si no lo necesitas, no lo almacenes"

---

### 📝 Resumen para el examen:

- **Data Masking**: Oculta parcialmente información sensible (tarjetas de crédito, SSN)
- **Anonymization**: Hace que los datos no sean rastreables a su origen
- AWS proporciona soporte nativo para masking en **Glue DataBrew** y **Redshift**
- Técnicas principales: reemplazo, shuffling, encryption (determinística/probabilística), hashing
- La mejor práctica de seguridad es no importar/almacenar datos sensibles innecesarios
- Debes conocer las diferencias entre masking (oculta parcialmente) y anonymization (hace datos irrastreables)
- Estos conceptos son importantes para el manejo ético y seguro de datos en proyectos de ML

## 3. SageMaker Security: Encryption at Rest and in Transit

La seguridad es un aspecto fundamental en AWS y particularmente importante en servicios como SageMaker que manejan grandes volúmenes de datos potencialmente sensibles. Revisaremos las mejores prácticas de seguridad para SageMaker que aparecen con frecuencia en el examen de AWS ML Associate.

### 3.1 Mejores prácticas generales de seguridad en AWS

#### 3.1.1 Identity and Access Management (IAM)
- Utilizar IAM para restringir permisos entre servicios
- Configurar cuentas de usuario con permisos mínimos necesarios (ej. acceso solo a SageMaker notebooks y S3 para datos)
- Implementar el principio de privilegio mínimo (least access) para SageMaker

#### 3.1.2 Seguridad adicional
- **Multi-Factor Authentication (MFA)**: Activar especialmente para cuentas de administrador y root
- **SSL/TLS**: Utilizar siempre para cualquier conexión (especialmente para EMR)
- **CloudTrail**: Implementar para registrar actividad de APIs y usuarios
  - **Diferencia importante para el examen**: CloudTrail (auditoría) vs CloudWatch (monitoreo/alarmas)
- **Encriptación**: Aplicar para información de identificación personal (PII)

### 3.2 Encriptación en reposo (Encryption at Rest) en SageMaker

#### 3.2.1 AWS Key Management Service (KMS)
- Todos los jobs y notebooks de SageMaker aceptan claves KMS para encriptar datos almacenados
- Se pueden encriptar con KMS:
  - Artefactos de entrenamiento
  - Artefactos de parameter tuning
  - Artefactos de batch transform
  - Artefactos de endpoints
  - Notebooks
  - Contenido en carpetas `/opt/ml` y `/temp` en contenedores Docker

#### 3.2.2 Encriptación de datos de entrenamiento y hosting
- Utilizar técnicas estándar de encriptación de S3
- S3 puede utilizar:
  - Claves KMS
  - Claves propias de S3

### 3.3 Encriptación en tránsito (Encryption in Transit) en SageMaker

#### 3.3.1 Protocolos de seguridad
- Todo el tráfico en SageMaker soporta TLS/SSL

#### 3.3.2 Inter-container Traffic Encryption
- Encripta la comunicación entre nodos durante el entrenamiento
- Opcional pero útil para datos altamente sensibles
- Consideraciones importantes:
  - Aumenta el tiempo de entrenamiento
  - Implica mayor costo en entrenamientos complejos (deep learning)
  - Se activa desde la consola o API de SageMaker durante la configuración del training job

#### 3.3.3 Configuración a través de:
- Consola de SageMaker
- API para training
- API para tuning

---

### 📝 Resumen para el examen:

- La seguridad es un componente crítico en los exámenes de AWS, también para SageMaker
- Debes conocer cómo implementar: IAM (least privilege), MFA, SSL/TLS, CloudTrail y encriptación
- **Encryption at Rest**: Usa KMS para encriptar notebooks, jobs, artefactos y contenedores
- **Encryption in Transit**: Todo el tráfico soporta TLS/SSL, incluyendo inter-container traffic encryption (opcional)
- Recordar la diferencia clave entre **CloudTrail** (auditoría/logging) y **CloudWatch** (monitoreo/alarmas)
- La encriptación inter-container aumenta tiempo y costo de entrenamiento pero puede ser necesaria para datos sensibles
- Implementar siempre las protecciones adecuadas para información PII (nombres, direcciones, números de seguridad social)

## 4. SageMaker Security: VPC's, IAM, Logging and Monitoring

La integración de SageMaker con los distintos servicios de seguridad de AWS es un tema importante para el examen AWS ML Associate. En esta sección, exploraremos consideraciones específicas de seguridad para SageMaker relacionadas con redes, permisos y monitoreo.

### 4.1 SageMaker y Virtual Private Clouds (VPCs)

#### 4.1.1 Configuración de VPC para Training Jobs
- Los training jobs de SageMaker operan dentro de una VPC
- Para mayor seguridad, se puede especificar una VPC privada al lanzar un training job

#### 4.1.2 Consideraciones de conectividad
- **Problema principal**: SageMaker requiere acceso a S3 para:
  - Obtener datos de entrenamiento
  - Almacenar artefactos del modelo entrenado
  - Recuperar modelos para deployment

#### 4.1.3 Soluciones para VPC privadas
- Configurar **S3 VPC endpoints** para habilitar la comunicación
- Se pueden aplicar políticas de endpoint personalizadas
- Mantener políticas de bucket en S3 para seguridad adicional

### 4.2 Acceso a Internet en SageMaker

#### 4.2.1 SageMaker Notebooks
- **Por defecto**: Los notebooks tienen acceso a Internet habilitado
  - Ventaja: Facilita la descarga de datasets públicos
  - Desventaja: Potencial riesgo de seguridad

- **Deshabilitar acceso a Internet**:
  - Se puede hacer durante la creación del notebook
  - Requiere configuración adicional para S3

#### 4.2.2 Alternativas cuando se deshabilita el acceso a Internet
- Configurar un **Interface Endpoint** (AWS PrivateLink)
- Implementar un **NAT Gateway**
- Ambos permiten conexiones salientes necesarias para training y hosting

#### 4.2.3 Contenedores de Training e Inference
- También tienen acceso a Internet por defecto
- Se puede habilitar **Network Isolation**
  - Implica pérdida de acceso directo a S3
  - Requiere soluciones alternativas para mantener funcionalidad

### 4.3 Gestión de Permisos con IAM

#### 4.3.1 Permisos específicos para SageMaker
SageMaker ofrece permisos granulares para:
- Crear training jobs
- Crear modelos
- Crear endpoints
- Crear hyperparameter tuning jobs
- Crear notebooks

#### 4.3.2 Políticas predefinidas
- **SageMaker Read Only Access**: Solo lectura
- **SageMaker Full Access**: Acceso completo
- **Administrator Access**: Acceso administrativo
- **Data Scientist**: Permisos típicos para científicos de datos

#### 4.3.3 Casos de uso
- Restringir usuarios a solo crear notebooks sin deployments
- Limitar acceso a hyperparameter tuning jobs (costosos)
- Personalizar permisos según roles y necesidades

### 4.4 Logging y Monitoring

#### 4.4.1 CloudWatch para SageMaker
Permite monitorear:
- **Endpoints**:
  - Invocaciones
  - Latencia
- **Nodos**:
  - Estado de salud
  - CPU disponible
  - Memoria disponible

#### 4.4.2 CloudWatch con Ground Truth
- Monitoreo de trabajadores humanos:
  - Número de trabajadores activos
  - Cantidad de trabajo completado
  - Rendimiento del etiquetado

#### 4.4.3 CloudTrail para auditoría
- Registra acciones de usuarios, roles y servicios
- Los archivos de log se entregan a S3
- Facilita auditorías de seguridad posteriores

---

### 📝 Resumen para el examen:

- **VPC y SageMaker**: Si usas VPC privadas, debes configurar S3 VPC endpoints para que SageMaker funcione correctamente
- **Acceso a Internet**: Los notebooks y contenedores tienen acceso a Internet por defecto; al deshabilitarlo, se necesita PrivateLink o NAT Gateway
- **Network Isolation**: Opción para aumentar seguridad pero requiere soluciones para mantener acceso a S3
- **IAM**: Proporciona permisos granulares para controlar diferentes aspectos de SageMaker (training, endpoints, notebooks)
- **Políticas predefinidas**: Disponibles para roles comunes (Read Only, Full Access, Administrator, Data Scientist)
- **Monitoring**: CloudWatch monitorea endpoints (invocaciones, latencia) y recursos (CPU, memoria)
- **Ground Truth**: CloudWatch también puede monitorear trabajadores humanos y su rendimiento
- **Auditoría**: CloudTrail registra todas las acciones en SageMaker para revisión posterior
- La distinción entre CloudWatch (monitoreo) y CloudTrail (auditoría) es importante para el examen

## 5. IAM en AWS: Usuarios, Grupos, Políticas, MFA y Roles

La seguridad de cualquier entorno de AWS, incluidos los proyectos de machine learning, comienza con una gestión adecuada de identidad y acceso. IAM (Identity and Access Management) es un servicio global fundamental para el examen AWS ML Associate.

### 5.1 Conceptos básicos de IAM

#### 5.1.1 Usuarios y Grupos
- **Usuario root**: Creado por defecto con la cuenta AWS. Solo debe usarse para configuración inicial.
- **Usuarios IAM**: Representan personas individuales dentro de la organización.
- **Grupos IAM**: Conjunto de usuarios con necesidades de permisos similares.
  - Los grupos solo pueden contener usuarios, no otros grupos
  - Un usuario puede pertenecer a múltiples grupos
  - Un usuario no necesita pertenecer a ningún grupo (aunque no es recomendable)

#### 5.1.2 Ejemplo de organización:
```
Organización
├── Grupo Developers (Alice, Bob, Charles)
├── Grupo Operations (David, Edward)
├── Grupo Audit (Charles, David)
└── Usuario sin grupo (Fred)
```

### 5.2 Políticas IAM

Las políticas IAM son documentos JSON que definen permisos para usuarios, grupos o roles.

#### 5.2.1 Estructura de una política IAM
```json
{
  "Version": "2012-10-17",
  "Id": "IdentificadorPolítica",
  "Statement": [
    {
      "Sid": "1",
      "Effect": "Allow",
      "Principal": {"AWS": "arn:aws:iam::account-id:root"},
      "Action": ["s3:GetObject", "s3:ListBucket"],
      "Resource": "arn:aws:s3:::my-bucket/*"
    }
  ]
}
```

#### 5.2.2 Componentes principales:
- **Version**: Versión del lenguaje de política (generalmente "2012-10-17")
- **Id**: Identificador opcional de la política
- **Statement**: Una o más declaraciones de permisos que contienen:
  - **Sid**: Identificador opcional de la declaración
  - **Effect**: "Allow" o "Deny" (permitir o denegar acceso)
  - **Principal**: A quién se aplica la política (usuarios/roles)
  - **Action**: Las operaciones de API permitidas/denegadas
  - **Resource**: Los recursos a los que se aplican las acciones
  - **Condition**: Condiciones opcionales para la aplicación de la política

#### 5.2.3 Herencia de políticas
- Las políticas adjuntas a un grupo se aplican a todos sus miembros
- Si un usuario pertenece a múltiples grupos, hereda todas las políticas de esos grupos
- Las políticas inline se adjuntan directamente a usuarios específicos

### 5.3 Protección de cuentas con MFA y políticas de contraseña

#### 5.3.1 Políticas de contraseña
AWS permite configurar políticas que controlan:
- Longitud mínima de contraseña
- Requisitos de caracteres específicos (mayúsculas, minúsculas, números, especiales)
- Capacidad de los usuarios para cambiar sus propias contraseñas
- Expiración y renovación de contraseñas (ej. cada 90 días)
- Prevención de reutilización de contraseñas

#### 5.3.2 Multi-Factor Authentication (MFA)
MFA combina algo que conoces (contraseña) con algo que posees (dispositivo MFA):

| Tipo de dispositivo MFA | Descripción | Características |
|-------------------------|-------------|----------------|
| **Virtual MFA Device** | Aplicaciones en smartphones | Google Authenticator (un dispositivo), Authy (múltiples tokens) |
| **U2F Security Key** | Dispositivo físico como YubiKey | Soporta múltiples usuarios IAM y root con una sola llave |
| **Hardware Key Fob** | Dispositivo proporcionado por Gemalto | Alternativa física para autenticación |
| **Hardware Key Fob (GovCloud)** | Proporcionado por SurePassID | Específico para AWS GovCloud en EE.UU. |

### 5.4 Roles IAM

Los roles IAM son entidades similares a los usuarios pero están diseñados para ser utilizados por servicios AWS, no por personas físicas.

#### 5.4.1 Casos de uso de roles
- **EC2 Instance Roles**: Permiten a instancias EC2 realizar acciones en AWS
- **Lambda Function Roles**: Otorgan permisos a funciones Lambda
- **CloudFormation Roles**: Permiten a CloudFormation crear recursos

#### 5.4.2 Funcionamiento
1. Se crea un rol IAM con permisos específicos
2. Se asigna el rol al servicio AWS (ej. instancia EC2)
3. El servicio asume temporalmente el rol para realizar acciones en AWS
4. Las acciones se permiten según las políticas adjuntas al rol

---

### 📝 Resumen para el examen:

- **IAM** es un servicio global para gestionar usuarios, grupos y permisos en AWS
- El **Principle of Least Privilege** debe aplicarse a todos los usuarios y servicios
- **MFA** es crítico para proteger cuentas, especialmente la cuenta root
- Las **políticas IAM** son documentos JSON que definen permisos
- Los componentes clave de una política son: Effect, Principal, Action y Resource
- Los **roles IAM** permiten a los servicios AWS (como SageMaker, EC2, Lambda) realizar acciones en tu cuenta
- La seguridad comienza con buenas prácticas de IAM:
  - No usar la cuenta root para operaciones diarias
  - Crear usuarios individuales para cada persona
  - Usar grupos para gestionar permisos
  - Implementar MFA para todas las cuentas importantes
  - Utilizar políticas de contraseña robustas
  - Asignar roles adecuados a los servicios AWS
- Para el examen AWS ML Associate, debes entender cómo configurar permisos adecuados para servicios de machine learning como SageMaker

## 6. Encryption 101

La encriptación es un componente crítico de la seguridad en AWS, especialmente en aplicaciones de Machine Learning donde se manejan datos sensibles. Es fundamental entender los conceptos básicos de encriptación para el examen AWS ML Associate.

### 6.1 Tipos de encriptación

Existen tres tipos principales de encriptación en la nube de AWS:

#### 6.1.1 Encriptación en tránsito (Encryption in Flight)
- **También conocida como**: TLS (Transport Layer Security) o SSL (Secure Sockets Layer)
- **Funcionamiento básico**: 
  - Los datos se encriptan antes de enviarse
  - Se desencriptan después de recibirse
  - Protege la comunicación entre cliente y servidor a través de la red

#### 6.1.2 Encriptación en reposo del lado del servidor (Server-Side Encryption at Rest)
- **Funcionamiento básico**:
  - Los datos se encriptan después de ser recibidos por el servidor
  - Se almacenan en formato encriptado
  - Se desencriptan antes de enviarse de vuelta al cliente
  - El servidor gestiona las claves de encriptación/desencriptación

#### 6.1.3 Encriptación del lado del cliente (Client-Side Encryption)
- **Funcionamiento básico**:
  - Los datos se encriptan y desencriptan en el lado del cliente
  - El servidor no puede desencriptar los datos (no tiene acceso a las claves)
  - El cliente es responsable de la gestión de claves

### 6.2 Encriptación en tránsito (TLS/SSL)

#### 6.2.1 Características principales
- Utiliza certificados TLS para la encriptación
- Se identifica en sitios web como HTTPS
- Protege contra ataques "man-in-the-middle"
- Garantiza que solo el servidor destino pueda desencriptar los datos

#### 6.2.2 Ejemplo práctico
Cuando un usuario inicia sesión en un servicio AWS:
1. El usuario introduce sus credenciales (nombre de usuario y contraseña)
2. La encriptación TLS encripta estos datos en el lado del cliente
3. Los datos encriptados viajan por la red
4. Ningún servidor intermedio puede leer los datos encriptados
5. Solo el servidor destino puede desencriptar y procesar las credenciales

### 6.3 Encriptación en reposo del lado del servidor

#### 6.3.1 Características principales
- Los datos se almacenan encriptados en el servidor
- Se utilizan "data keys" (claves de datos) para la encriptación/desencriptación
- El servidor debe gestionar y tener acceso a estas claves
- El proceso es transparente para el cliente

#### 6.3.2 Ejemplo con Amazon S3
1. Cliente envía un objeto a S3 (posiblemente a través de HTTPS)
2. S3 recibe el objeto desencriptado
3. S3 utiliza una data key para encriptar el objeto
4. El objeto se almacena encriptado
5. Cuando se solicita, S3 desencripta el objeto con la data key
6. El objeto desencriptado se envía de vuelta al cliente

### 6.4 Encriptación del lado del cliente

#### 6.4.1 Características principales
- Toda la encriptación/desencriptación ocurre en el cliente
- El servidor nunca tiene acceso a los datos desencriptados
- Proporciona un mayor nivel de seguridad cuando no se confía en el servidor
- El cliente es responsable de la gestión y seguridad de las claves

#### 6.4.2 Proceso típico
1. El cliente posee un objeto y una data key
2. El cliente encripta el objeto localmente
3. El objeto encriptado se envía a cualquier servicio de almacenamiento (S3, EBS, etc.)
4. El servidor almacena el objeto sin poder desencriptarlo
5. Al recuperar, el cliente recibe el objeto encriptado
6. El cliente usa su data key para desencriptar el objeto localmente

---

### 📝 Resumen para el examen:

- **Encryption in Flight (TLS/SSL)**:
  - Protege datos durante la transmisión por la red
  - Utiliza certificados TLS/SSL
  - Se identifica como HTTPS
  - Previene ataques "man-in-the-middle"

- **Server-Side Encryption at Rest**:
  - Los datos se almacenan encriptados en el servidor
  - El servidor gestiona las claves de encriptación
  - El proceso es transparente para el cliente
  - Ejemplo común: Amazon S3 Server-Side Encryption

- **Client-Side Encryption**:
  - La encriptación/desencriptación ocurre únicamente en el cliente
  - El servidor nunca accede a datos desencriptados
  - El cliente gestiona las claves de encriptación
  - Proporciona máxima seguridad cuando no se confía en el servidor

- Estos mecanismos de encriptación son fundamentales para proteger datos sensibles en aplicaciones de machine learning y otros servicios AWS
- Para datos sensibles en proyectos ML, considera utilizar una combinación de estos métodos para maximizar la seguridad

## 7. AWS KMS (Key Management Service)

AWS Key Management Service (KMS) es un servicio fundamental para la gestión de claves de encriptación en AWS, especialmente relevante para proteger datos sensibles en aplicaciones de machine learning.

### 7.1 Introducción a AWS KMS

#### 7.1.1 Funcionalidad principal
- Servicio que gestiona claves de encriptación por nosotros
- Completamente integrado con IAM para autorización
- Facilita el control de acceso a datos encriptados
- Permite auditar cada llamada a la API para usar claves a través de CloudTrail

#### 7.1.2 Integración con servicios AWS
AWS KMS se integra de manera transparente con la mayoría de servicios AWS, incluyendo:
- EBS (volúmenes)
- S3 (buckets)
- RDS (bases de datos)
- SSM (Parameter Store)
- SageMaker (notebooks, training jobs)

#### 7.1.3 Uso directo de KMS
- Accesible mediante API calls, AWS CLI o SDK
- Recomendado para encriptar datos sensibles que no deben almacenarse en texto plano
- Los secretos encriptados pueden almacenarse en código o variables de entorno

### 7.2 Tipos de claves KMS

#### 7.2.1 Por estructura de clave

| Tipo | Descripción | Uso |
|------|-------------|-----|
| **Symmetric KMS Keys** | Una única clave para encriptar y desencriptar | - Servicios AWS integrados con KMS<br>- Accesible solo mediante API calls |
| **Asymmetric KMS Keys** | Par de claves: pública (encriptar) y privada (desencriptar) | - Operaciones encrypt/decrypt o sign/verify<br>- Clave pública descargable<br>- Clave privada accesible solo mediante API |

#### 7.2.2 Por propiedad/gestión

| Tipo | Descripción | Características | Costo |
|------|-------------|-----------------|-------|
| **AWS Owned Keys** | Pertenecen a AWS | - Usadas en SSE-S3, SSE-DynamoDB<br>- No visibles en consola KMS | Gratuito |
| **AWS Managed Keys** | Gestionadas por AWS | - Formato: `aws/service-name`<br>- Ej: aws/RDS, aws/EBS<br>- Uso limitado al servicio asociado | Gratuito |
| **Customer Managed Keys** | Creadas y gestionadas por el cliente | - Máximo control<br>- Personalizables<br>- Pueden importarse de fuentes externas | $1/mes + API calls |

### 7.3 Consideraciones importantes de KMS

#### 7.3.1 Precios
- $1/mes por cada Customer Managed Key
- ~$0.03 por 10,000 llamadas API a KMS

#### 7.3.2 Rotación automática de claves
- AWS Managed Keys: Rotación automática anual
- Customer Managed Keys: Rotación automática configurable
- Claves importadas: Solo rotación manual (usando alias)

#### 7.3.3 Alcance regional
- Las claves KMS están limitadas a una región específica
- Para copiar datos encriptados entre regiones, se requiere reencriptación

#### 7.3.4 Ejemplo: Copiar volumen EBS encriptado entre regiones
1. Crear snapshot del volumen EBS (hereda la encriptación con misma clave KMS)
2. Copiar snapshot a otra región (AWS reencripta con diferente clave KMS)
3. Restaurar snapshot en la nueva región como volumen EBS

### 7.4 Políticas de claves KMS

#### 7.4.1 Control de acceso
- Similar a las políticas de bucket de S3
- **Diferencia crucial**: Sin política de clave KMS, nadie puede acceder a la clave

#### 7.4.2 Tipos de políticas
1. **Política por defecto**:
   - Creada automáticamente si no se especifica una personalizada
   - Permite a todos en la cuenta acceder a la clave (sujeto a permisos IAM)

2. **Política personalizada**:
   - Define usuarios/roles específicos que pueden acceder a la clave
   - Define administradores de la clave
   - Facilita acceso entre cuentas

#### 7.4.3 Caso de uso: Copiar snapshots encriptados entre cuentas
1. Crear snapshot encriptado con Customer Managed Key
2. Adjuntar política de clave KMS para autorizar acceso entre cuentas
3. Compartir snapshot encriptado con cuenta destino
4. En cuenta destino, crear copia del snapshot y encriptarlo con clave propia
5. Crear volumen desde snapshot en cuenta destino

---

### 📝 Resumen para el examen:

- **AWS KMS** es el servicio principal para gestionar claves de encriptación en AWS
- Las **Symmetric Keys** usan la misma clave para encriptar/desencriptar y son accesibles solo vía API
- Las **Asymmetric Keys** tienen una parte pública (encriptación) y una privada (desencriptación)
- Tipos por gestión: **AWS Owned Keys** (gratuitas), **AWS Managed Keys** (gratuitas, formato aws/service) y **Customer Managed Keys** ($1/mes)
- Las claves KMS están **limitadas a una región**; reencriptación necesaria para mover datos entre regiones
- **Rotación de claves**: automática anual (AWS Managed), configurable (Customer Managed) o manual (importadas)
- Las **políticas de claves KMS** controlan el acceso a las claves (sin política = sin acceso)
- KMS es crucial para **machine learning seguro** en SageMaker, protegiendo datos sensibles y modelos
- El uso de KMS se audita a través de **CloudTrail**, importante para cumplimiento normativo
- AWS KMS está completamente integrado con IAM y la mayoría de servicios AWS

## 8. Amazon Macie

Amazon Macie es un servicio de seguridad y privacidad de datos que utiliza machine learning para detectar información sensible en AWS, particularmente relevante para proyectos de ML que manejan datos personales.

### 8.1 Descripción general

#### 8.1.1 Características principales
- Servicio completamente gestionado para seguridad y privacidad de datos
- Utiliza machine learning y reconocimiento de patrones
- Diseñado para descubrir y proteger datos sensibles en AWS
- Especializado en la detección de información personalmente identificable (PII)

#### 8.1.2 Funcionamiento básico
- Analiza datos almacenados en buckets de S3
- Identifica automáticamente datos que pueden clasificarse como PII
- Notifica los descubrimientos a través de Amazon EventBridge
- Permite integraciones con:
  - Amazon SNS (notificaciones)
  - AWS Lambda (acciones automatizadas)
  - Otros servicios de AWS

### 8.2 Implementación y uso

#### 8.2.1 Facilidad de configuración
- Activación con un solo clic
- Solo requiere especificar los buckets S3 que se desean monitorear

#### 8.2.2 Casos de uso en ML
- Verificación de datos de entrenamiento para identificar PII antes del entrenamiento de modelos
- Cumplimiento de regulaciones de protección de datos (GDPR, HIPAA, etc.)
- Auditoría continua de datos almacenados

### 8.3 Beneficios para seguridad en ML

- Reduce el riesgo de filtración de datos sensibles
- Ayuda a cumplir con regulaciones de privacidad
- Automatiza la detección de información sensible
- Complementa otras medidas de seguridad (encriptación, IAM, etc.)

---

### 📝 Resumen para el examen:

- **Amazon Macie** es un servicio que utiliza machine learning para detectar datos sensibles en S3
- Se especializa en la identificación de **información personalmente identificable (PII)**
- Funciona **analizando buckets S3** específicos (no otros servicios de almacenamiento)
- Las notificaciones se envían a través de **EventBridge** y pueden integrarse con SNS, Lambda, etc.
- La configuración es **simple y rápida** (un solo clic + selección de buckets)
- Es una herramienta importante para cumplir con regulaciones de privacidad en proyectos de ML
- Macie complementa las estrategias de seguridad que incluyen IAM, KMS y encriptación
- Recuerda que Macie solo analiza datos en S3, no en otros servicios de almacenamiento

## 9. AWS Secrets Manager

AWS Secrets Manager es un servicio especializado para almacenar y gestionar información sensible como credenciales, claves API y otros secretos, con características avanzadas de rotación automática y encriptación.

### 9.1 Características principales

#### 9.1.1 Gestión de secretos
- Almacenamiento seguro de información confidencial
- Diferente de SSM Parameter Store por su capacidad de gestión avanzada

#### 9.1.2 Rotación de secretos
- Capacidad de forzar la rotación de secretos cada X días
- Automatización de la generación de nuevos secretos durante la rotación
- Utiliza funciones Lambda para generar nuevos secretos automáticamente

#### 9.1.3 Integraciones nativas
- Integración avanzada con diversos servicios AWS, especialmente bases de datos:
  - Amazon RDS (MySQL, PostgreSQL, SQL Server)
  - Amazon Aurora
  - Otros servicios de bases de datos AWS

#### 9.1.4 Seguridad
- Encriptación de secretos mediante AWS KMS
- Controles de acceso a través de IAM
- Auditoría a través de CloudTrail

### 9.2 Secretos multi-región

#### 9.2.1 Funcionamiento
- Replicación de secretos entre múltiples regiones AWS
- Secrets Manager mantiene sincronizados los secretos de la región principal con las regiones secundarias

#### 9.2.2 Beneficios
- Resiliencia ante fallos regionales (ej. problemas en US-East-1)
- Capacidad de promover un secreto réplica como secreto independiente
- Facilita la construcción de aplicaciones multi-región
- Soporta estrategias de recuperación ante desastres (DR)
- Complementa bases de datos replicadas entre regiones

#### 9.2.3 Implementación
- Se crea un secreto en la región primaria
- Se configura la replicación a regiones secundarias
- Secrets Manager se encarga de mantener la sincronización

### 9.3 Casos de uso en Machine Learning

- Almacenar credenciales para acceder a fuentes de datos
- Proteger claves API para servicios externos utilizados en pipelines de ML
- Gestionar credenciales de acceso a bases de datos que almacenan resultados de modelos
- Compartir secretos entre componentes de una arquitectura ML distribuida
- Garantizar la seguridad en entornos multi-región para implementaciones de ML

---

### 📝 Resumen para el examen:

- **AWS Secrets Manager** es el servicio especializado para almacenar y gestionar secretos
- A diferencia de SSM Parameter Store, permite la **rotación automática** de secretos
- Utiliza **funciones Lambda** para generar nuevos secretos durante la rotación
- Está **integrado nativamente** con servicios de bases de datos AWS (RDS, Aurora)
- Los secretos se **encriptan mediante KMS**
- Soporta **secretos multi-región** para alta disponibilidad y recuperación ante desastres
- Permite **sincronización automática** entre la región principal y regiones secundarias
- Es ideal para gestionar credenciales en entornos ML complejos y distribuidos
- Cuándo usarlo: cuando veas preguntas sobre secretos, rotación de credenciales o integración segura con RDS/Aurora

## 10. AWS WAF (Web Application Firewall)

AWS WAF es un firewall de aplicaciones web que protege las aplicaciones contra vulnerabilidades y ataques comunes en la capa de aplicación (Layer 7). Este servicio es importante para securizar aplicaciones de machine learning expuestas a través de APIs o interfaces web.

### 10.1 Conceptos básicos

#### 10.1.1 Definición y alcance
- Protege aplicaciones web contra exploits comunes en la Capa 7 (HTTP/HTTPS)
- A diferencia de los firewalls de Capa 4 que operan a nivel TCP/UDP
- Servicio regional, excepto para CloudFront donde es global

#### 10.1.2 Servicios compatibles
AWS WAF puede desplegarse en:
- Application Load Balancer (ALB)
- Amazon API Gateway
- Amazon CloudFront
- AWS AppSync GraphQL API
- Amazon Cognito User Pools

> **Nota importante para el examen**: WAF **NO** puede desplegarse en Network Load Balancer (NLB) porque NLB opera en la Capa 4, mientras que WAF es para la Capa 7.

### 10.2 Web ACLs y reglas

#### 10.2.1 Web ACL (Listas de Control de Acceso Web)
- Unidad principal de configuración de WAF
- Contiene reglas para filtrar el tráfico
- Regional para la mayoría de servicios, global para CloudFront

#### 10.2.2 Tipos de reglas
AWS WAF permite filtrar basándose en:

| Tipo de regla | Descripción | Ejemplo |
|---------------|-------------|---------|
| **IP Sets** | Filtrado por dirección IP | Bloquear IPs maliciosas (hasta 10,000 IPs por set) |
| **HTTP Headers** | Filtrado por cabeceras HTTP | Verificar User-Agent o tokens |
| **HTTP Body** | Filtrado por contenido | Validar datos enviados |
| **URI Strings** | Filtrado por URI | Prevenir SQL injection y XSS |
| **Size Constraints** | Restricciones de tamaño | Limitar requests a 2MB |
| **Geo Match** | Filtrado geográfico | Permitir/bloquear países específicos |
| **Rate-based** | Filtrado por frecuencia | Limitar a 10 requests/segundo por IP (protección DDoS) |

#### 10.2.3 Rule Groups
- Conjuntos lógicos de reglas que pueden añadirse a múltiples Web ACLs
- Facilitan la organización y reutilización de reglas

### 10.3 Caso de uso: IP fija con WAF y ALB

#### 10.3.1 Problema
- Se necesita una IP fija para una aplicación web con WAF
- ALB no ofrece IPs fijas
- WAF no es compatible con NLB (que sí ofrece IPs fijas)

#### 10.3.2 Solución arquitectónica
1. Implementar un **AWS Global Accelerator** para obtener IPs fijas
2. Configurar el Global Accelerator para que apunte a un **Application Load Balancer**
3. Habilitar **AWS WAF** con Web ACLs en el ALB
4. Desplegar la aplicación detrás del ALB (ej. instancias EC2)

#### 10.3.3 Flujo de tráfico
- Cliente → Global Accelerator (IP fija) → ALB con WAF (filtrado) → Aplicación

### 10.4 Aplicaciones en Machine Learning

- Protección de APIs de inferencia de modelos ML
- Seguridad para interfaces web de herramientas de ML
- Prevención de ataques de inyección en servicios de machine learning
- Control de acceso geográfico a servicios de IA
- Limitación de tasas para prevenir abuso de APIs de ML

---

### 📝 Resumen para el examen:

- **AWS WAF** protege aplicaciones web contra ataques en la Capa 7 (HTTP)
- Se puede desplegar en **ALB, API Gateway, CloudFront, AppSync y Cognito** (NO en NLB)
- Las **Web ACLs** son regionales excepto para CloudFront (global)
- Permite filtrar por **IP, HTTP headers/body, URI, tamaño, ubicación geográfica y tasa de solicitudes**
- Un **IP Set** puede contener hasta 10,000 direcciones IP
- Las **Rate-based rules** pueden limitar solicitudes por IP (útil contra DDoS)
- Para obtener una IP fija con WAF, combinar **Global Accelerator + ALB + WAF**
- **Rule Groups** permiten agrupar reglas para reutilizarlas en múltiples Web ACLs
- WAF es crucial para proteger APIs y servicios ML expuestos públicamente

## 11. AWS Shield

AWS Shield es un servicio de protección contra ataques de Denegación de Servicio Distribuido (DDoS), ofreciendo diferentes niveles de protección para infraestructuras cloud que incluyen aplicaciones de machine learning.

### 11.1 Conceptos de DDoS

#### 11.1.1 Definición de DDoS
- **Distributed Denial of Service (DDoS)**: Ataque coordinado donde múltiples sistemas envían tráfico masivo hacia un objetivo
- **Objetivo del ataque**: Sobrecargar la infraestructura para que no pueda servir a usuarios legítimos
- **Impacto**: Interrupción del servicio, pérdida de disponibilidad, posibles costos adicionales

#### 11.1.2 Tipos de ataques DDoS
- **Layer 3/4 (Red/Transporte)**: SYN floods, UDP floods, ataques de reflexión
- **Layer 7 (Aplicación)**: Ataques HTTP flood, ataques SlowLoris, ataques a nivel de aplicación

### 11.2 AWS Shield Standard

#### 11.2.1 Características principales
- Servicio gratuito activado automáticamente para todos los clientes AWS
- Proporciona protección contra ataques DDoS más comunes:
  - SYN floods
  - UDP floods
  - Ataques de reflexión
  - Otros ataques de capa 3 y capa 4

#### 11.2.2 Beneficios
- Mitigación automática
- Sin costo adicional
- Protección básica pero efectiva para la mayoría de casos

### 11.3 AWS Shield Advanced

#### 11.3.1 Características principales
- Servicio de mitigación DDoS opcional y premium
- Costo aproximado: $3,000 por mes por organización
- Protección contra ataques DDoS más sofisticados
- Cobertura para servicios específicos:
  - Amazon EC2
  - Elastic Load Balancing
  - Amazon CloudFront
  - AWS Global Accelerator
  - Amazon Route 53

#### 11.3.2 Beneficios adicionales
- Acceso 24/7 al equipo de respuesta DDoS de AWS
- Protección contra costos elevados durante ataques (cost protection)
- Mitigación automática de ataques en capa de aplicación (Layer 7)
- Creación, evaluación y despliegue automático de reglas WAF

#### 11.3.3 Integración con WAF
- Complementa AWS WAF para ataques de Layer 7
- Crea y administra automáticamente reglas de WAF durante ataques

### 11.4 Aplicaciones en entornos de Machine Learning

- Protección de endpoints de inferencia de modelos ML
- Seguridad para servicios de predicción con alta disponibilidad
- Continuidad de servicio para APIs de ML críticas
- Defensa contra atacantes que intentan agotar recursos computacionales

### 11.5 Comparativa Shield Standard vs Shield Advanced

| Característica | Shield Standard | Shield Advanced |
|----------------|----------------|-----------------|
| **Costo** | Gratuito | ~$3,000/mes |
| **Activación** | Automática | Opcional |
| **Nivel de protección** | Básico (Layer 3/4) | Avanzado (Layer 3/4/7) |
| **Servicios protegidos** | Todos | EC2, ELB, CloudFront, Global Accelerator, Route 53 |
| **Soporte** | General | Equipo DDoS dedicado 24/7 |
| **Cost protection** | No | Sí |
| **Integración WAF** | No | Sí (automática) |

---

### 📝 Resumen para el examen:

- **AWS Shield Standard**:
  - Servicio gratuito y automático para todos los clientes AWS
  - Protege contra ataques DDoS comunes de capa 3/4
  - No requiere configuración especial

- **AWS Shield Advanced**:
  - Servicio premium (~$3,000/mes por organización)
  - Protege contra ataques DDoS más sofisticados
  - Cubre específicamente EC2, ELB, CloudFront, Global Accelerator y Route 53
  - Incluye acceso al equipo de respuesta DDoS 24/7
  - Ofrece protección contra costos elevados durante ataques
  - Integra automáticamente con WAF para mitigar ataques de capa 7

- Los ataques DDoS buscan sobrecargar la infraestructura para interrumpir servicios, lo que puede ser crítico para aplicaciones ML que requieren alta disponibilidad
- La combinación de Shield y WAF proporciona una protección completa contra diferentes tipos de ataques

## 12. Virtual Private Cloud (VPC) y Seguridad de Red

El entendimiento de la infraestructura de red en AWS es crucial para implementar soluciones de machine learning seguras y efectivas. Esta sección cubre los conceptos fundamentales de VPC y seguridad de red en AWS.

### 12.1 Conceptos básicos de VPC y Subnets

#### 12.1.1 Virtual Private Cloud (VPC)
- Red privada virtual dentro de AWS para desplegar recursos
- Recurso regional (distinto VPC para cada región AWS)
- Un VPC predeterminado se crea automáticamente para cada región

#### 12.1.2 Subnets
- Particiones de red dentro del VPC
- Definidas a nivel de Zona de Disponibilidad (AZ)
- **Subnet pública**: Accesible desde Internet
- **Subnet privada**: No accesible desde Internet
- Las tablas de enrutamiento definen el flujo de tráfico entre subnets

#### 12.1.3 CIDR Range
- Rango de IPs permitidas dentro del VPC
- No deben solaparse entre VPCs que necesiten comunicarse

### 12.2 Conectividad a Internet

#### 12.2.1 Internet Gateway
- Permite que instancias en subnets públicas se conecten a Internet
- Se define a nivel de VPC
- Una subnet pública tiene ruta directa al Internet Gateway

#### 12.2.2 NAT Gateway/NAT Instance
- Permite que instancias en subnets privadas accedan a Internet (salida) sin ser accesibles desde Internet (entrada)
- NAT Gateway: Gestionado por AWS, más fácil de escalar
- NAT Instance: Autogestionado
- Se despliega en subnet pública con ruta al Internet Gateway
- Las subnets privadas tienen ruta al NAT Gateway/Instance

### 12.3 Seguridad de Red

#### 12.3.1 Network ACL (NACL)
- Firewall que controla tráfico hacia y desde subnets
- Soporta reglas de permiso y denegación (allow/deny)
- Se aplica a nivel de subnet
- Solo incluye direcciones IP
- Es stateless (requiere reglas para tráfico entrante y saliente)

#### 12.3.2 Security Groups
- Firewall que controla tráfico hacia y desde instancias EC2/ENI
- Solo soporta reglas de permiso (allow)
- Puede referenciar direcciones IP u otros Security Groups
- Es stateful (el tráfico de retorno se permite automáticamente)

#### 12.3.3 VPC Flow Logs
- Captura información sobre tráfico IP en interfaces
- Tipos: VPC Flow Logs, Subnet Flow Logs, ENI Flow Logs
- Ayuda a monitorear y solucionar problemas de conectividad
- Registra tráfico permitido y denegado
- Captura información de servicios gestionados por AWS (ELB, ElastiCache, RDS, etc.)
- Los datos pueden enviarse a S3, CloudWatch Logs o Kinesis Data Firehose

### 12.4 Conectividad entre VPCs y servicios AWS

#### 12.4.1 VPC Peering
- Conecta dos VPCs como si fueran parte de la misma red
- Requiere que los rangos IP no se solapen
- No es transitivo (requiere conexiones directas entre cada par de VPCs)

#### 12.4.2 VPC Endpoints
- Permite conectar a servicios AWS usando red privada en lugar de Internet público
- **VPC Endpoint Gateway**: Para S3 y DynamoDB
- **VPC Endpoint Interface**: Para el resto de servicios AWS
- Proporciona seguridad y menor latencia

#### 12.4.3 AWS PrivateLink
- Conecta servicios entre VPCs directa y privadamente
- No requiere VPC Peering, Internet Gateway o NAT
- Permite a proveedores exponer servicios a clientes AWS de forma privada
- Arquitectura:
  - Proveedor: Network Load Balancer (NLB)
  - Cliente: Elastic Network Interface (ENI)
  - PrivateLink conecta ambos manteniendo el tráfico privado

### 12.5 Conectividad a infraestructura on-premises

#### 12.5.1 Site-to-Site VPN
- Conecta dispositivo VPN on-premises a AWS
- Conexión encriptada a través de Internet público
- Rápida configuración (minutos)

#### 12.5.2 Direct Connect
- Conexión física dedicada entre on-premises y AWS
- Provee conexión privada, segura y rápida
- No utiliza Internet público
- Requiere tiempo para establecerse (mínimo un mes)

---

### 📝 Resumen para el examen:

- **VPC**: Red privada virtual regional donde se despliegan recursos AWS
- **Subnets**: Particiones de red en AZs (públicas con acceso a Internet, privadas sin acceso directo)
- **Internet Gateway**: Proporciona acceso a Internet para subnets públicas
- **NAT Gateway/Instance**: Permite acceso a Internet desde subnets privadas
- **Security Groups**: Firewall stateful a nivel de instancia (solo reglas allow)
- **NACL**: Firewall stateless a nivel de subnet (reglas allow/deny)
- **VPC Flow Logs**: Registra tráfico de red para monitoreo y solución de problemas
- **VPC Peering**: Conecta VPCs sin rangos IP solapados (no transitivo)
- **VPC Endpoints**: Acceso privado a servicios AWS (Gateway para S3/DynamoDB, Interface para otros)
- **PrivateLink**: Conecta servicios entre VPCs de forma privada y escalable
- **Site-to-Site VPN**: Conexión encriptada a través de Internet público con on-premises
- **Direct Connect**: Conexión física privada entre on-premises y AWS
- Estos conceptos son esenciales para configurar entornos ML seguros, especialmente cuando se manejan datos sensibles

