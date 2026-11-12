# Análisis de impacto de SC-001

---

## Solicitud que se analiza

**Referencia:** `sc-001-identificacion-apartados-proximos-a-vencer.md`

**Solicitante:** Ana Victoria Hernández Álvarez
**Papel:** Dupla de revisión.  
**Fecha de solicitud:** 12 de noviembre de 2026.

**Responsable del análisis:** Julián Guerrero Martínez, dueño del diseño del sistema Minimarket Control.

**Cambio solicitado:** Permitir que el dueño y los cajeros identifiquen los pedidos apartados pendientes cuya fecha de vencimiento sea hoy o mañana.

## Traducción a términos del sistema

La solicitud afecta principalmente al módulo **Pedidos apartados**, definido en el diagrama de componentes de la API de Minimarket Control.

Actualmente, la clase `Apartado` cuenta con los atributos `estado`, `fechaCreacion` y `fechaLimite`, además de las operaciones `estaVigente()` y `expirar()`.

Para satisfacer la solicitud, el módulo deberá permitir consultar los apartados cuyo estado sea `Pendiente` y cuya fecha límite corresponda al día actual o al siguiente.

La aplicación web deberá presentar estos resultados al administrador y al cajero.

No será necesario agregar nuevos estados ni modificar la regla de vencimiento de siete días.

La funcionalidad podrá incorporarse dentro del monolito modular definido en el ADR-001, sin requerir nuevos servicios externos.

## Impacto directo

| Artefacto | Unidad | Qué hay que cambiar |
|---|---|---|
| Nuevo requisito RF-025 (identificador propuesto) | 2 | Especificar la consulta de apartados pendientes próximos a vencer. |
| Caso de uso: Consultar apartados próximos a vencer | 2 | Agregar el flujo donde el administrador o cajero consulta los pedidos que vencen hoy o mañana. |
| Diagrama de clases | 4 | Incorporar la responsabilidad de consultar apartados próximos a vencer en el módulo correspondiente. La clase `Apartado` conserva sus atributos actuales. |
| Diagrama de secuencia | 4 | Documentar la interacción entre la aplicación web, el módulo Pedidos apartados y la base de datos para realizar la consulta. |
| Diagrama de componentes de la API | 3 | Actualizar la descripción del módulo Pedidos apartados para incluir la consulta de próximos vencimientos. |
| Interfaz de la aplicación web | 2 | Incorporar una vista o sección para consultar los apartados próximos a vencer. |

**Nota:** La numeración RF-025 deberá verificarse con la especificación completa antes de incorporar el requisito.

## Impacto indirecto

| Artefacto | Unidad | Por qué se ve afectado |
|---|---|---|
| ADR-001: Monolito modular en lugar de microservicios | 3 | Sin impacto. La nueva consulta utiliza el módulo existente y no requiere modificar la decisión arquitectónica. |
| Diagrama de contexto C4 | 3 | Sin impacto. Los actores y sistemas externos permanecen iguales. |
| Diagrama de contenedores C4 | 3 | Sin impacto. Se conservan la aplicación web, la API Node.js y PostgreSQL. |
| Máquina de estados de Apartado | 4 | Sin impacto. Se mantienen los estados Pendiente, Liquidado y Expirado. |
| RF-016: Expiración automática de apartados | 2 | Sin modificación funcional. Debe comprobarse que la nueva consulta sea consistente con la expiración automática existente. |
| Control de acceso por roles | 3 | Se debe verificar que tanto Administrador como Cajero puedan consultar los vencimientos sin acceder a información restringida. |
| Bitácora de cambios | 3 | Sin impacto funcional. La consulta no genera modificaciones en los apartados. |
| Alertas de stock bajo | 3 | Sin impacto. El mecanismo Observer del inventario continúa funcionando independientemente. |

## Lo que queda sin tocar

El cambio no requiere modificar los siguientes elementos:

- **Ventas y clientes frecuentes:** El proceso de cobro, acumulación y canje de puntos permanece igual.
- **Inventario:** No se modifican las reglas de descuento, reserva o liberación de existencias.
- **Proveedores:** No se altera la gestión de proveedores ni de costos de compra.
- **Autenticación:** Se conservan los roles Administrador y Cajero.
- **Vigencia de apartados:** Los pedidos mantienen su duración máxima de siete días naturales.
- **Estados de apartado:** Continúan siendo Pendiente, Liquidado y Expirado.
- **Base de datos:** No se necesitan nuevas tablas ni columnas porque la fecha límite y el estado ya están contemplados en el modelo.
- **Arquitectura:** Se mantiene el monolito modular y la comunicación existente entre la aplicación web, la API y PostgreSQL.
- **Registro de auditoría:** No se modifica el comportamiento actual de los registros de cambios.

## Riesgos

| Riesgo | Probabilidad | Consecuencia |
|---|---|---|
| Cálculo incorrecto de la fecha de vencimiento | Media | Podrían mostrarse pedidos que todavía no están próximos a vencer o excluirse algunos que sí lo están. |
| Mostrar apartados liquidados o expirados | Baja | Los empleados podrían intentar dar seguimiento a pedidos que ya no están pendientes. |
| Inconsistencia con la expiración automática | Media | Un apartado podría aparecer como pendiente aunque su vencimiento ya haya sido procesado o esté en proceso. |
| Incremento del tiempo de consulta | Baja | Una consulta ineficiente podría afectar la rapidez de la aplicación si aumenta considerablemente la cantidad de apartados. |

**Riesgo de no implementar el cambio:** Los empleados seguirán dependiendo de la revisión manual de fechas, aumentando la posibilidad de omitir pedidos próximos a vencer.

## Alternativas consideradas

### Opción A: Sección de apartados próximos a vencer

Agregar una sección dentro del módulo Pedidos apartados que permita consultar únicamente los pedidos pendientes cuya fecha límite sea hoy o mañana.

**Ventajas:**
- Facilita la identificación de los pedidos que requieren atención.
- Mantiene separada la consulta de próximos vencimientos del listado general.
- Aprovecha los datos y componentes existentes.

**Desventajas:**
- Requiere incorporar una nueva sección en la interfaz.
- Necesita una consulta específica y pruebas adicionales.

### Opción B: Indicadores visuales en el listado general de apartados

Mantener el listado actual de apartados e identificar visualmente aquellos cuya fecha de vencimiento sea hoy o mañana.

**Ventajas:**
- Requiere menos modificaciones en la navegación.
- Permite identificar vencimientos sin cambiar de sección.

**Desventajas:**
- Los pedidos próximos a vencer pueden pasar desapercibidos cuando existen muchos registros.
- El usuario tendría que revisar el listado general para encontrarlos.

### Alternativa seleccionada

Se recomienda la **Opción A**, porque permite consultar directamente los pedidos que necesitan seguimiento, sin mezclarlos con el resto de los apartados.

Además, el cambio puede realizarse dentro del módulo existente sin modificar la arquitectura general del sistema.

## Estimación de esfuerzo

| Concepto | Esfuerzo |
|---|---|
| Cambios de diseño y documentación | 3 horas |
| Implementación | 6 horas |
| Pruebas | 3 horas |
| **Total** | **12 horas** |

### De dónde sale este número

La estimación considera que Minimarket Control ya contempla la clase `Apartado`, sus estados y la fecha límite de vencimiento.

**Diseño y documentación (3 horas):**
- Definición del nuevo requisito.
- Actualización del caso de uso.
- Ajuste de los diagramas afectados.
- Diseño de la sección de consulta.

**Implementación (6 horas):**
- Incorporación de la consulta de apartados próximos a vencer.
- Integración con el módulo existente.
- Presentación de resultados en la aplicación web.
- Validación de los permisos de acceso.

**Pruebas (3 horas):**
- Verificación de apartados que vencen hoy.
- Verificación de apartados que vencen mañana.
- Exclusión de apartados liquidados, expirados y con vencimiento posterior.
- Validación de cambios de día y consistencia con la expiración automática.

La estimación supone que ya existen las funcionalidades básicas de gestión de apartados y acceso a sus datos.

No incluye construir desde cero el módulo de apartados ni la infraestructura general del sistema. Por lo tanto, representa el esfuerzo incremental estimado una vez que esas funcionalidades estén disponibles.

## Recomendación

**Aceptar la solicitud SC-001.**

El cambio proporciona una mejora funcional que facilita el seguimiento de los pedidos apartados y podría reducir cancelaciones por falta de atención.

Su impacto arquitectónico es bajo, porque aprovecha la estructura existente del módulo Pedidos apartados y no requiere modificar la base de datos, los estados de los pedidos ni las decisiones documentadas en el ADR-001.

La prioridad recomendada se mantiene en **Media**, ya que mejora la operación del negocio sin representar una funcionalidad indispensable para completar las ventas.

Se recomienda incorporar el cambio una vez que el módulo básico de apartados y su expiración automática estén funcionando, para evitar duplicar esfuerzos durante el desarrollo.

---

*El objetivo de este documento no es dar un número exacto. Es que cualquiera pueda seguir tu razonamiento y señalarte dónde te equivocaste.*
