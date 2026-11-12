# SC-001: Identificación de apartados próximos a vencer

---

## Estado

Propuesta · 12 de noviembre de 2026

## Quién la pide

**Solicitante:** Ana Victoria Hernández Álvarez
**Papel:** Dupla de revisión del sistema Minimarket Control.

## Qué se pide

Quiero que el dueño y los cajeros puedan identificar fácilmente cuáles pedidos apartados están próximos a vencer, específicamente aquellos cuya fecha límite sea hoy o mañana.

También quiero que puedan consultar la fecha de vencimiento y los datos del cliente para dar seguimiento a estos pedidos antes de que sean cancelados.

## Por qué

Actualmente, el sistema contempla que los pedidos apartados tengan una vigencia máxima de siete días naturales y que se cancelen automáticamente al vencer.

Sin embargo, no se especifica una manera de identificar anticipadamente cuáles están próximos a su fecha límite. Esto dificulta que los empleados puedan dar seguimiento a los pedidos pendientes y recordar a los clientes que deben recogerlos.

El cambio permitiría mejorar la organización de los apartados y reducir la cantidad de pedidos que terminan cancelándose por falta de seguimiento.

## Qué pasa si no se hace

Los empleados tendrían que revisar manualmente las fechas de vencimiento para identificar los apartados próximos a expirar.

Esto podría ocasionar que algunos pedidos se cancelen sin que el personal haya tenido oportunidad de darles seguimiento, generando posibles pérdidas de ventas y una mala experiencia para los clientes.

## Tipo de mantenimiento

- [ ] **Correctivo** · algo está mal y hay que repararlo.
- [ ] **Adaptativo** · el entorno cambió y hay que ajustarse.
- [x] **Perfectivo** · funciona, pero puede ser mejor.
- [ ] **Preventivo** · todavía no falla, pero va a fallar.

**Justificación:** El sistema ya contempla la gestión y expiración de apartados, pero esta funcionalidad puede mejorarse al facilitar la identificación anticipada de los pedidos próximos a vencer.

## Criterio de aceptación

1. El dueño y los cajeros pueden consultar los apartados pendientes cuya fecha límite sea hoy o mañana.
2. Cada apartado próximo a vencer muestra su identificador, nombre del cliente y fecha límite.
3. Los apartados liquidados o expirados no aparecen entre los pedidos próximos a vencer.
4. Los apartados cuya fecha límite sea posterior a mañana no aparecen en esta consulta.
5. La consulta no modifica el estado de los apartados ni su fecha de vencimiento.

## Prioridad que sugiere el solicitante

**Media.**

El cambio facilitaría el seguimiento de los pedidos y podría reducir cancelaciones, pero no impide que las funciones principales del sistema, como registrar ventas, gestionar inventario y administrar apartados, continúen operando.

---

*La solicitud describe qué se quiere, nunca cómo hacerlo. El cómo se decide en el análisis de impacto, y lo decide quien conoce el diseño.*
