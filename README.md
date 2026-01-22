# CONSERSA

## Índice
1. [Contexto de la empresa](#-contexto-de-la-empresa)
2. [Problema identificado](#️-problema-identificado)
3. [¿Por qué elegir nuestra solución?](#-por-qué-elegir-nuestra-solución)
4. [Tecnologías y Requerimientos No Funcionales](#️-tecnologías-y-requerimientos-no-funcionales)
5. [Justificación de la arquitectura](#️-justificación-de-la-arquitectura)
---

## Contexto de la empresa

**Consersa** es una empresa contratista que ejecuta trabajos de mantenimiento y reparación de redes de agua potable y alcantarillado sanitario en la región La Libertad, bajo la supervisión de **Sedalib**.  
Entre sus principales actividades se encuentran la atención de fugas de agua, atoros de desagüe, reparaciones de redes y conexiones nuevas.

El proceso inicia cuando un usuario reporta una incidencia al call center de Sedalib, donde se genera una orden de servicio que posteriormente es derivada a Consersa. Esta orden es asignada a un supervisor y un equipo de operarios según el tipo de trabajo y la zona geográfica.  
Finalizado el trabajo en campo, se registran los metrados y materiales utilizados, los cuales pasan por un proceso de revisión, validación y valorización para determinar el monto facturable.

---

## Problema identificado

El sistema actual de gestión de órdenes y valorizaciones presenta **fallas operativas** que afectan directamente la facturación.  
El sistema es utilizado por **4 personas en dos turnos**, lo que provoca problemas como:

- Duplicidad de órdenes de servicio  
- Modificación o eliminación de órdenes ya valorizadas  
- Falta de control de accesos entre turnos  
- Riesgo de pérdidas económicas y sanciones por parte de Sedalib  

Estas fallas ocurren debido a la ausencia de validaciones de estado, control de concurrencia y auditoría de acciones críticas.

---

## ¿Por qué elegir nuestra solución?

Nuestra solución evita la duplicidad y eliminación indebida de órdenes ya valorizadas, garantizando:

- Integridad de la información entre turnos  
- Evitar cobros duplicados por valorizaciones repetidas  
- Prevención de pérdidas económicas  
- Trazabilidad completa de cada acción realizada sobre una orden  

El sistema valida el estado global de la orden antes de permitir cualquier modificación, asegurando que una orden valorizada no pueda ser alterada por personal no autorizado.

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
