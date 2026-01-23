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

El sistema de gestión de órdenes y valorizaciones utilizado por Consersa presenta problemas operativos que afectan directamente la facturación. El sistema  es usado por un grupo de 4 personas, en 2 turnos diferentes, 2 personas en la mañana (7 a.m. -  3 p.m) y las otras 2 personas en la tarde (3 p.m - 11 p.m), en cada turno uno se encarga del Call Center de Consersa y el otro se encarga de Valorizar. La duplicidad de órdenes de servicio ocurre cuando el sistema  guarda 2 veces o elimina una orden, por ejemplo; una orden de servicio es generada en un turno, luego valorizada y los trabajadores del otro turno al abrir el sistema podrían eliminar o modificar datos de la orden ya valorizada, generando la problemática.

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

| ID | Atributo | Requerimiento No Funcional | Tecnologías |
|----|---------|----------------------------|-------------|
| RNF 01 | Accesibilidad | Hasta 10 sesiones concurrentes | AWS Cognito, Amazon API Gateway |
| RNF 02 | Accesibilidad | Actualización de estados ≤ 2 s | AWS AppSync (GraphQL), Amazon SNS |
| RNF 03 | Accesibilidad | Consulta de órdenes ≤ 2 s | AWS Lambda, Amazon DynamoDB (DAX) |
| RNF 04 | Accesibilidad | Disponibilidad ≥ 99.5% mensual | AWS Global Accelerator, Route 53 |
| RNF 05 | Accesibilidad | Soporte a picos de 200 órdenes/día | Amazon SQS (FIFO), AWS Lambda |
| RNF 06 | Accesibilidad | Registro y visualización ≤ 3 s | AWS API Gateway, AWS Lambda |
| RNF 07 | Seguridad | Control de acceso basado en roles | Azure AD B2C |
| RNF 08 | Seguridad | Autenticación multifactor (MFA) | AWS Cognito MFA |
| RNF 09 | Seguridad | 0% de duplicidad de órdenes | AWS EventBridge |
| RNF 10 | Seguridad | Auditoría del 100% de acciones | Event Sourcing |
| RNF 11 | Fiabilidad | Backups diarios (30 días) | Amazon RDS Automated Backups |
| RNF 12 | Fiabilidad | RTO ≤ 2 h, RPO ≤ 24 h | Amazon RDS Multi-AZ, AWS Backup |
| RNF 13 | Fiabilidad | Réplica activa MySQL | Amazon RDS Read Replica |
| RNF 14 | Mantenibilidad | Auditoría de órdenes anuladas | CloudWatch Logs, RDS MySQL |
| RNF 15 | Mantenibilidad | Alertas automáticas de fallos | CloudWatch Alarms, SNS, SQS DLQ |
| RNF 16 | Flexibilidad | Nuevos tipos de órdenes sin rediseño | Amazon EventBridge |
| RNF 17 | Flexibilidad | Cambio de estados ≤ 30 min | AWS Step Functions |
| RNF 18 | Flexibilidad | Cambios de precios sin afectar histórico | AWS Lambda, EventBridge |
| RNF 19 | Flexibilidad | Hasta 50 actividades por orden | AWS Systems Manager, CloudWatch |
| RNF 20 | Modificabilidad | Despliegue autónomo de servicios | AWS Lambda (Microservicios) |

---

##  Justificación de la arquitectura

Se eligió una **arquitectura híbrida Serverless + Event-Driven**, apoyada en una base de datos transaccional, debido a que permite:

- Desacoplar procesos críticos como registro, valorización y auditoría  
- Evitar inconsistencias de datos entre turnos  
- Garantizar integridad y trazabilidad mediante eventos inmutables  
- Reducir duplicidades causadas por reintentos o fallos parciales  
- Optimizar costos al ejecutarse solo durante los turnos operativos  

Este enfoque asegura alta disponibilidad, escalabilidad inmediata y confiabilidad de la información, minimizando riesgos económicos y operativos para Consersa.
