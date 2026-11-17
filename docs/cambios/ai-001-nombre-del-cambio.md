# Análisis de impacto de SC-001

---

## Solicitud que se analiza

**Referencia:** `SC-001-Identificación-de-apartados-próximos-a-vencer.md`  
**Solicitante:** Ana Victoria Hernández Álvarez, dupla de revisión.  
**Fecha:** 12 de noviembre de 2026.

La solicitud pide que el dueño y los cajeros puedan consultar los apartados pendientes que vencen hoy o mañana, mostrando su identificador, nombre del cliente y fecha límite, sin modificar los pedidos.

## Traducción a términos del sistema

El cambio afecta principalmente al módulo **Pedidos apartados** de Minimarket Control. La clase `Apartado` ya contiene `estado` y `fechaLimite`, así que se necesita una operación de consulta que seleccione exclusivamente apartados con estado `Pendiente` cuya fecha límite corresponda a hoy o mañana, tomando como referencia la fecha local de la tienda.

La aplicación web deberá mostrar el resultado a usuarios autenticados con rol **Administrador** o **Cajero**. El cambio puede implementarse dentro del monolito modular existente sin agregar servicios externos, tablas, columnas ni estados. No modifica la vigencia de siete días ni la expiración automática (RF-016).

El nuevo requisito se propone como **RF-026**, porque `RF-025` ya corresponde a «Consultar puntos de cliente frecuente». Su caso de uso propuesto será **CU-22**. Ambos quedan pendientes de aprobación de SC-001.

## Impacto directo

| Artefacto | Unidad | Qué hay que cambiar |
|---|---|---|
| Requisito RF-026 (propuesto) | 2 | Especificar la consulta de apartados pendientes con vencimiento hoy o mañana, sus datos visibles y la condición de solo lectura. |
| Caso de uso CU-22 (propuesto) | 2 | Describir cómo el Administrador o Cajero consulta los vencimientos, incluyendo el caso sin resultados. |
| Aplicación web | 3 | Agregar una sección de «Próximos a vencer» que muestre identificador, cliente y fecha límite. |
| Diagrama de componentes de la API | 3 | Incorporar al módulo Pedidos apartados la responsabilidad de consultar próximos vencimientos, sin crear otro módulo. |
| Diagrama de clases | 4 | Incorporar la operación de consulta en el servicio de apartados y la consulta filtrada en su repositorio; conservar los atributos de `Apartado`. |
| Diagrama de secuencia | 4 | Representar la consulta desde la aplicación web hasta el módulo de apartados y su repositorio, y el retorno de resultados. |
| Acceso a datos de Apartado | 3 | Consultar los registros existentes por estado y fecha límite sin modificar el esquema de PostgreSQL. |

## Impacto indirecto

| Artefacto | Unidad | Por qué se ve afectado |
|---|---|---|
| ADR-001: Monolito modular | 3 | **Sin impacto en la decisión.** La consulta se incorpora al módulo existente y respeta sus límites. |
| Diagrama de contexto C4 | 3 | **Sin impacto.** No aparecen nuevos actores ni sistemas externos. |
| Diagrama de contenedores C4 | 3 | **Sin impacto.** Siguen la aplicación web TypeScript, la API Node.js y PostgreSQL. |
| Máquina de estados de Apartado | 4 | **Sin impacto.** Se conservan `Pendiente`, `Liquidado` y `Expirado`; la consulta no produce transiciones. |
| RF-016: Expiración automática | 2 | **Sin cambio de regla.** Debe verificarse que un apartado ya expirado no aparezca al consultar, especialmente al cambiar de día. |
| RF-022: Liquidación de apartados | 2 | **Sin cambio de regla.** Debe verificarse que los apartados liquidados desaparezcan de la consulta. |
| Autenticación y RNF-SEG-001 | 3 | **Sin cambio de permisos generales.** Debe comprobarse que solo los usuarios autenticados puedan acceder y que ambos roles vean únicamente los datos autorizados. |
| Bitácora de auditoría | 3 | **Sin impacto funcional.** Consultar no modifica apartados ni genera movimientos de inventario. |
| Inventario y alerta Observer | 3 | **Sin impacto.** La consulta no reserva ni libera mercancía y no cambia el stock. |

## Lo que queda sin tocar

No cambian las reglas de vigencia de siete días ni los estados de los apartados; tampoco su fecha límite, sus productos reservados ni la lógica de liquidación y expiración. Se mantienen los módulos de Ventas, Inventario y Proveedores, el esquema de base de datos, las restricciones de acceso a costos y márgenes, la arquitectura de monolito modular y el patrón Observer de alertas de stock. No se agregan notificaciones automáticas a clientes: el personal solo consulta la información para dar seguimiento.

## Riesgos

| Riesgo | Probabilidad | Consecuencia |
|---|---|---|
| Comparar incorrectamente las fechas o la zona horaria | Media | Se muestran apartados fuera del plazo o se omiten apartados que sí vencen hoy o mañana. |
| Incluir apartados liquidados o expirados | Media | Se da seguimiento a pedidos que ya no están pendientes. |
| Consultar mientras ocurre la expiración automática | Media | Podrían mostrarse resultados temporalmente desactualizados. |
| Configurar incorrectamente los permisos | Baja | Un usuario sin sesión o sin autorización podría acceder a la consulta. |
| Una consulta ineficiente con muchos apartados | Baja | La sección puede responder lentamente. |

**Riesgo de no hacer el cambio:** El personal continuará revisando fechas manualmente y podría pasar por alto apartados próximos a vencer.

## Alternativas consideradas

1. **Opción A: Sección de apartados próximos a vencer.** Incorporar una sección que muestre únicamente apartados pendientes que vencen hoy o mañana. **Costo:** una consulta, una sección de interfaz y pruebas de fechas, estados y permisos; se contempla dentro de las 12 horas estimadas. **Pendiente:** verificar que el módulo básico de apartados esté operativo. **Ventaja:** los pedidos que requieren seguimiento se encuentran directamente.
2. **Opción B: Indicadores en el listado general de apartados.** Destacar visualmente las fechas hoy y mañana dentro del listado existente. **Costo:** cambios menores en la navegación, pero se requieren el mismo filtrado de estado y pruebas; se espera un esfuerzo menor en interfaz, aunque no se dispone de mediciones para cuantificarlo. **Pendiente:** el usuario tendría que revisar el listado completo, por lo que algunos pedidos podrían pasar inadvertidos.

**Alternativa preferida: Opción A**, porque separa visualmente los pendientes próximos a vencer y facilita su seguimiento sin cambiar la arquitectura.

## Estimación de esfuerzo

| Concepto | Esfuerzo |
|---|---|
| Cambios de diseño y documentación | 3 horas-persona |
| Implementación | 6 horas-persona |
| Pruebas | 3 horas-persona |
| **Total** | **12 horas-persona** |

**De dónde sale este número.** Es una **estimación preliminar por analogía y desglose de actividades**, no una medición de horas reales. Se compara con consultas filtradas ya previstas en Minimarket Control, como la consulta de stock crítico y la de costos de proveedores: el esfuerzo adicional de SC-001 está principalmente en filtrar por fechas y estados e integrar una nueva vista.

Las **3 horas de diseño** se distribuyen en 0.5 horas para actualizar RF-026, CU-22 y la trazabilidad, 1 hora para ajustar los diagramas afectados y 1.5 horas para diseñar la sección de consulta. Las **6 horas de implementación** se distribuyen en 1.5 horas para la consulta de datos, 1 hora para la API y permisos, 2 horas para la sección web y 1.5 horas para la integración. Las **3 horas de pruebas** contemplan 1.5 horas para verificar fechas y estados, 0.75 horas para comprobar permisos y 0.75 horas para comprobar su consistencia con la liquidación y expiración automática.

Se supone que **ya existen y funcionan** la autenticación, el módulo básico de apartados, sus fechas y estados, y el acceso a PostgreSQL. **No se incluye** construir desde cero esos componentes, configurar toda la infraestructura ni corregir defectos anteriores. Por ello, las 12 horas estiman únicamente el cambio incremental; si falta la implementación base, será necesario estimarla por separado.

## Recomendación

**Aceptar SC-001 con prioridad media, cuando el módulo básico de apartados y su expiración automática estén operativos.** Es una mejora perfectiva que permite dar seguimiento a pedidos con riesgo de cancelación y aprovecha los componentes existentes, sin cambiar la arquitectura ni las reglas de negocio. Esta es una recomendación del análisis: la solicitud continúa en estado **Propuesta** hasta que se registre una decisión formal.
