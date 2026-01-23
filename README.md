# CONSERSA

## Índice
1. [Contexto de la empresa](#-contexto-de-la-empresa)
2. [Problema identificado](#️-problema-identificado)
3. [¿Por qué elegir nuestra solución?](#-por-qué-elegir-nuestra-solución)
4. [Tecnologías y Requerimientos No Funcionales](#️-tecnologías-y-requerimientos-no-funcionales)
5. [Justificación de la arquitectura](#️-justificación-de-la-arquitectura)
---

## Contexto de la empresa

**Consersa** es una empresa contratista que ejecuta trabajos de mantenimiento y reparación de redes de agua potable y alcantarillado sanitario en la región La Libertad, bajo supervisión de Sedalib. Entre sus principales actividades se encuentran la atención de fugas de agua, atoros de desagüe, reparaciones de redes y conexiones nuevas.
El proceso inicia cuando un usuario reporta una incidencia a través del call center de Sedalib. Esta información es registrada en el sistema de Sedalib, donde se genera una orden de servicio que posteriormente es derivada a Consersa. El call center de Consersa  recibe la orden y esta la deriva a un supervisor formado por operarios (6 - 8 personas) según corresponda el tipo de trabajo y la zona geográfica.
Una vez ejecutado el trabajo en campo, el supervisor registra las actividades realizadas en una hoja de campo, incluyendo los materiales utilizados y los metrados de la intervención. Estas hojas son revisadas por un ingeniero que se encarga de revisar el metrado y cálculos, luego se deriva estas hojas a un supervisor de Sedalib para que sean validadas y por último el ingeniero de sistemas se encarga de Valorizar las hojas de campo de acuerdo a las órdenes de servicio que son descargadas en el call center.
La valorización consiste en ingresar los metrados al sistema, los cuales se calculan automáticamente con base en precios unitarios previamente establecidos por contrato. El resultado de este proceso determina el monto facturable del servicio ejecutado, flujo que se repite diariamente para cada orden de servicio atendida.


---

## Problema identificado

El sistema de gestión de órdenes y valorizaciones utilizado por Consersa presenta problemas operativos que afectan directamente la facturación. El sistema  es usado por un grupo de 100 personas, en 2 turnos diferentes, en la mañana (7 a.m. -  3 p.m) y otro grupo en la tarde (3 p.m - 11 p.m), en cada turno se encargan del Call Center de Consersa y se encargan de Valorizar. La duplicidad de órdenes de servicio ocurre cuando el sistema  guarda 2 veces o elimina una orden, por ejemplo; una orden de servicio es generada en un turno, luego valorizada y los trabajadores del otro turno al abrir el sistema podrían eliminar o modificar datos de la orden ya valorizada, generando la problemática.

---

## ¿Por qué elegir nuestra solución?

Por ejemplo, un trabajador intenta guardar esta orden:
| N° O.S | Ord.SED | Fecha | Hora | Ubicación | Usuario | Tipo de trabajo |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 23482 | 512879 | 04/12/2025 | 21:04:44 | JOSE M. ZAPIOLA 1935 - 1939 - LA ESPERANZA | SAUCEDA CHAVEZ NELVA MICAELA | Reparación de alcantarillado |

y una vez ejecutado el trabajo, se manda la hoja de campo.
Luego en el siguiente turno, otro trabajador modifica datos de la orden ya valorizada, lo vuelve a guardar creando un duplicado.

| N° O.S | Ord.SED | Fecha | Hora | Ubicación | Usuario | Tipo de trabajo |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 23482 | 512879 | 04/12/2025 | 21:04:44 | JOSE M. ZAPIOLA 1935 - 1939 - LA ESPERANZA | SAUCEDA CHAVEZ NELVA MICAELA | Reparación de alcantarillado |
| 23482 | 512879 | 04/12/2025 | 21:04:44 | JOSE M. ZAPIOLA 1935 - 1939 - LA ESPERANZA | SAUCEDA CHAVEZ NELVA MICAELA | Hundimiento en la pista |

y lo vuelve a valorizar. Esta actividad cuesta 13,901.96 soles, esta actividad se ha duplicado, entonces les cobra el doble lo cual les causará una pérdida y una posible sanción de parte de Sedalib. En el caso que el otro turno elimine la orden de servicio ya valorizada, eliminaría todo y tendrían una pérdida de 13,901.96 soles. Nuestro sistema solucionaría tanto el problema de la duplicidad para evitar la duplicación de precios y la eliminación de órdenes de los trabajos valorizados.

---

## Tecnologías y Requerimientos No Funcionales

A continuación se detallan los atributos de calidad, requerimientos técnicos y las tecnologías seleccionadas para el proyecto.

| ID | Atributos de calidad | Requerimiento No Funcional | Tecnologías |
| :--- | :--- | :--- | :--- |
| **RNF 01** | Accesibilidad | El sistema debe permitir hasta 100 sesiones concurrentes. | AWS Cognito + Amazon API Gateway |
| **RNF 02** | Accesibilidad | El sistema debe mostrar los cambios de estado de una orden en un tiempo máximo de 2 segundos. | AWS AppSync (GraphQL) + Amazon SNS |
| **RNF 03** | Accesibilidad | El sistema debe responder las consultas de órdenes en un tiempo máximo de 2 segundos. | AWS Lambda + Amazon DynamoDB (DAX) |
| **RNF 04** | Accesibilidad | El sistema debe estar disponible durante los turnos operativos (7:00 a.m. - 11:00 p.m.) con una disponibilidad mínima del 99.5% mensual. | AWS Global Accelerator + Amazon Route 53 |
| **RNF 05** | Accesibilidad | El sistema debe permitir el acceso continuo a los trabajadores autorizados aun cuando se presenten picos de carga de hasta 200 órdenes diarias. | Amazon SQS (FIFO) + AWS Lambda |
| **RNF 06** | Accesibilidad | El sistema debe permitir el registro y visualización de una orden en un tiempo máximo de 3 segundos desde su creación. | AWS API Gateway + AWS Lambda |
| **RNF 07** | Seguridad | El sistema deberá implementar un control de acceso basado en roles, evitando la anulación o eliminación de órdenes de servicio por usuarios no autorizados. | Azure AD B2C |
| **RNF 08** | Seguridad | El sistema debe implementar una autenticación multifactor (MFA) para los operarios del sistema. | AWS Cognito MFA |
| **RNF 09** | Seguridad | El sistema deberá evitar la duplicidad de órdenes y valorizaciones, asegurando que una misma operación registrada más de una vez genere un único registro, logrando una tasa de duplicidad del 0%, incluso ante hasta 1,000,000 de intentos repetidos para una misma orden. | AWS EventBridge |
| **RNF 10** | Seguridad | El sistema deberá registrar el 100% de las acciones críticas (crear, guardar, anular, valorizar) con usuario, fecha/hora y detalle de cambio para auditoría. | Event Sourcing |
| **RNF 11** | Fiabilidad | El sistema deberá realizar copias de seguridad automáticas diarias de la base de datos, con retención mínima de 30 días. | Amazon RDS Automated Backups |
| **RNF 12** | Fiabilidad | El sistema debe permitir la recuperación completa de datos ante fallos, con un RTO ≤ 2 horas y RPO ≤ 24 horas. | Amazon RDS Multi-AZ, AWS Backup |
| **RNF 13** | Fiabilidad | El sistema deberá contar con al menos una réplica activa de la base de datos Mysql, reduciendo el riesgo de pérdida de datos a menos del 0.5% mensual. | Amazon RDS Read Replica |
| **RNF 14** | Mantenibilidad | El sistema debe permitir auditar órdenes anuladas o eliminadas. | Amazon CloudWatch Logs, Amazon RDS MySQL |
| **RNF 15** | Mantenibilidad | El sistema debe implementar alertas automáticas ante fallos en el procesamiento de solicitudes de valorización. | Amazon CloudWatch Alarms, Amazon SNS, Amazon SQS (DLQ) |
| **RNF 16** | Flexibilidad | El sistema deberá permitir adaptar el sistema a nuevos tipos de órdenes o actividades sin necesidad de rediseñar el sistema completo, manteniendo el funcionamiento normal del sistema. | Amazon EventBridge |
| **RNF 17** | Flexibilidad | El sistema deberá permitir modificar los estados de una orden sin afectar órdenes ya registradas, aplicando cambios en un máximo de 30 minutos. | AWS Step Functions |
| **RNF 18** | Flexibilidad | El sistema debe permitir la modificación de parámetros de negocio y precios unitarios sin afectar órdenes ya valorizadas, garantizando una consistencia histórica del 100%. | AWS lambda, AWS EventBridge |
| **RNF 19** | Flexibilidad | El sistema deberá configurar el número máximo de actividades por orden, soportando al menos 50 actividades por orden de servicio. | AWS Systems Manager, Amazon CloudWatch |
| **RNF 20** | Modificabilidad | El sistema debe permitir el despliegue autónomo de los servicios, evitando las dependencias que afecten a otros elementos del sistema y reduciendo el impacto de cambios. | AWS lambda microservicios |

---

##  Justificación de la arquitectura

Hemos elegido una arquitectura híbrida Serverless + Event-Driven, apoyada en una base de datos transaccional, porque resuelve la falta de integridad de datos entre turnos de operación del sistema de Consersa mediante el desacoplamiento de procesos y la consistencia en tiempo real. Al basarse en eventos, cada acción crítica,como el guardado, anulación o cambio de estado de una orden, se procesa de forma asíncrona y queda registrada en una línea de tiempo inmutable, evitando que el turno de la tarde trabaje con información obsoleta o pueda restaurar órdenes eliminadas erróneamente, ya que el sistema valida siempre el estado global antes de ejecutar cualquier operación. El enfoque Event-Driven de sacopla procesos como el registro de órdenes, la valorización y la auditoría, reduciendo el riesgo de duplicidades causadas por reintentos o fallos parciales. Asimismo, el modelo Serverless optimiza los costos operativos al ejecutarse únicamente durante las ventanas de los dos turnos (7 a.m. a 11 p.m.), sin necesidad de administrar servidores, garantizando alta disponibilidad y escalabilidad inmediata para que la facturación no se vea afectada. Finalmente, el uso de una base de datos transaccional asegura la integridad y confiabilidad de los registros de órdenes y facturación, minimizando riesgos económicos y operativos.
