# Especificación de requisitos

**Sistema:** Minimarket Control  
**Autor:** Julián Guerrero Martínez  
**Versión:** 2.0  
**Fecha de la última actualización:** 24 de septiembre de 2026  

---

## 1. Propósito y alcance

**Propósito del documento:**  
Este documento define de forma precisa, comprobable y detallada los requisitos funcionales y no funcionales del sistema *Minimarket Control*. Está dirigido al desarrollador del sistema, a la dupla evaluadora y al cliente (dueño del minimarket) para guiar las etapas de diseño, desarrollo de prototipos en Figma, pruebas de software y análisis de impacto de cambios.

**Alcance del sistema:**  
El sistema abarca la gestión interna del punto de venta y control operativo del negocio mediante:
- Registro y catalogación de productos organizados por categoría.
- Descuento y actualización automática de existencias en tiempo real tras cada venta.
- Catálogo de proveedores con registro e historial de precios de compra por producto para comparación de tarifas.
- Registro de ventas realizadas en caja asociadas automáticamente al empleado en turno.
- Gestión de clientes frecuentes mediante número telefónico para la acumulación de puntos, permitiendo también ventas anónimas.
- Creación, consulta y liquidación de pedidos apartados con reserva inmediata de mercancía por un plazo máximo de 7 días naturales.
- Reporte diario consolidado de ventas por empleado y reporte de productos con stock por debajo del umbral mínimo.

**Fuera del alcance:**  
- Procesamiento de pagos en línea mediante tarjetas de crédito, débito o pasarelas externas.
- Generación y timbrado de facturación electrónica automática.
- Servicio de logística, entrega a domicilio o aplicaciones móviles para clientes.

---

## 2. Usuarios y su contexto

| Usuario | Qué hace hoy sin el sistema | Qué espera del sistema |
| :--- | :--- | :--- |
| **Dueño / Administrador** | Cuenta productos a mano y los compara con ventas. Revisa facturas físicas de papel en carpetas durante 2 o 3 días a la semana para comparar proveedores. Juzga el desempeño de empleados por impresiones. | Conocer la ganancia real y stock exacto. Comparar costos de proveedores en pantalla para elegir la mejor tarifa. Proteger la confidencialidad de sus costos frente a empleados y evaluar ventas por turno con datos objetivos. |
| **Cajero / Empleado** | Anota apartados en libretas de papel que se traspapelan y generan disputas. Cobra registrando notas en mano. Si un producto no tiene código, pide al cliente traer otro. | Procesar ventas en menos de 4 pasos para evitar filas. Consultar e identificar apartados rápidamente por nombre/teléfono sin depender de libretas. Acumular puntos por teléfono fácilmente. |

**Conflictos identificados entre usuarios:**  
- **Captura exhaustiva vs. Agilidad en caja:** El dueño requería capturar datos exhaustivos (proveedor, lote) en cada cobro. El cajero necesita agilidad para evitar filas en las 3 cajas. **Solución:** Se automatiza el registro de empleado, fecha y hora en segundo plano tras iniciar turno, limitando la captura manual en caja a los productos y el teléfono del cliente.

---

## 3. Requisitos funcionales

### 3.1 Resumen

| ID | Nombre | Prioridad | Origen |
| :--- | :--- | :--- | :--- |
| **RF-001** | Registro de venta y descuento de inventario | Imprescindible | Confirmado en entrevista (Proceso actual 3) |
| **RF-002** | Registro de pedido apartado con reserva de stock | Imprescindible | Confirmado en entrevista (Excepción 1 y dolor de libretas) |
| **RF-003** | Acumulación de puntos para cliente frecuente | Importante | Confirmado en entrevista (Verificación Puntos) |
| **RF-004** | Consulta comparativa de precios de proveedores | Imprescindible | Confirmado en entrevista (Proceso actual 2) |
| **RF-005** | Generación de reporte diario de ventas por empleado | Imprescindible | Confirmado en entrevista (Proceso actual 3) |
| **RF-006** | Liberación automática de apartados expirados | Importante | Descubrimiento en entrevista (Excepción 1: 7 días límite) |

---

### 3.2 Fichas

#### RF-001 · Registro de venta y descuento de inventario
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema registra la venta de mercancía descontando automáticamente del inventario la cantidad de unidades vendidas. |
| **Origen** | Confirmado en entrevista con el dueño (22 de septiembre de 2026). |
| **Prioridad** | Imprescindible |
| **Criterio de aceptación** | Al finalizar el cobro de una venta con $N$ unidades de un producto, la existencia en base de datos para dicho producto disminuye exactamente en $N$ unidades. Si las existencias son menores a $N$, el sistema despliega una alerta de stock insuficiente e impide finalizar la transacción. |
| **Relacionado con** | RF-003, RNF-USA-001, RNF-CON-001 |

---

#### RF-002 · Registro de pedido apartado con reserva de stock
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema registra el pedido apartado solicitando nombre completo y número de teléfono del cliente, reservando de inmediato las existencias en la base de datos. |
| **Origen** | Confirmado en entrevista con el dueño (Datos obligatorios de apartado). |
| **Prioridad** | Imprescindible |
| **Criterio de aceptación** | Al guardar un apartado especificando nombre y teléfono del cliente, la cantidad de unidades indicadas se resta del stock disponible para venta directa en mostrador. La mercancía queda asociada al cliente y no puede venderse a terceros hasta su liquidación o cancelación. |
| **Relacionado con** | RF-001, RF-006, RNF-CON-001 |

---

#### RF-003 · Acumulación de puntos para cliente frecuente
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema solicita el número telefónico del cliente frecuente antes de finalizar el cobro para abonar los puntos de la compra. |
| **Origen** | Confirmado en entrevista con el dueño (Verificación de Puntos). |
| **Prioridad** | Importante |
| **Criterio de aceptación** | Si el cajero ingresa un teléfono registrado antes del cierre de venta, el sistema calcula 1 punto por cada $10 MXN pagados y actualiza el saldo del cliente. Si el número no existe o se omite, el sistema procesa la venta como anónima sin acumular puntos. |
| **Relacionado con** | RF-001, RNF-USA-001 |

---

#### RF-004 · Consulta comparativa de precios de proveedores
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema muestra una lista comparativa de los precios de compra de un producto registrados por cada proveedor. |
| **Origen** | Confirmado en entrevista con el dueño (Proceso actual 2 y dolor de facturas). |
| **Prioridad** | Imprescindible |
| **Criterio de aceptación** | Al seleccionar un producto dentro del módulo de administración de proveedores, el sistema muestra el desglose de proveedores que lo surten con sus respectivos costos históricos de compra, ordenados del costo más bajo al más alto. |
| **Relacionado con** | RNF-SEG-001 |

---

#### RF-005 · Generación de reporte diario de ventas por empleado
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema genera un reporte diario consolidado de ventas clasificando las transacciones por el empleado en turno que las procesó. |
| **Origen** | Confirmado en entrevista con el dueño (Proceso actual 3). |
| **Prioridad** | Imprescindible |
| **Criterio de aceptación** | Al consultar una fecha determinada, el sistema despliega un resumen que detalla por cada empleado: total de ventas cobradas, número de operaciones realizadas y suma total de ingresos acumulados en su turno. |
| **Relacionado con** | RNF-CON-001 |

---

#### RF-006 · Liberación automática de apartados expirados
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema cancela los apartados que superen 7 días naturales sin liquidar y reintegra automáticamente la mercancía reservada al inventario general. |
| **Origen** | Descubrimiento en entrevista con el dueño (Excepción 1). |
| **Prioridad** | Importante |
| **Criterio de aceptación** | Al cumplirse las 00:00 horas del octavo día natural transcurrido desde la creación de un apartado no liquidado, el sistema cambia su estado a "Expirado" e incrementa las existencias disponibles para venta en mostrador en la misma cantidad que estaba reservada. |
| **Relacionado con** | RF-002, RNF-CON-001 |

---

## 4. Requisitos no funcionales

### 4.1 Resumen

| ID | Atributo | Nombre | Prioridad | Origen |
| :--- | :--- | :--- | :--- | :--- |
| **RNF-USA-001** | Usabilidad | Pasos máximos para el proceso de cobro | Imprescindible | Visión del Producto y confirmación en entrevista |
| **RNF-SEG-001** | Seguridad | Restricción de acceso a costos por roles | Imprescindible | Confirmado en entrevista (Regla estricta de privacidad) |
| **RNF-CON-001** | Confiabilidad / Trazabilidad | Registro inmutable de auditoría por transacción | Imprescindible | Derivado del tipo de sistema (Sistemas de Información) |

---

### 4.2 Fichas

#### RNF-USA-001 · Pasos máximos para el proceso de cobro
| Campo | Contenido |
| :--- | :--- |
| **Atributo de calidad** | Usabilidad |
| **Descripción** | El proceso completo de cobro y registro de una venta en mostrador se realiza en menos de cuatro pasos de navegación en la pantalla. |
| **Métrica** | Un máximo de 4 clics o confirmaciones de teclado desde que se agrega el último producto al carrito hasta la finalización del cobro y despliegue del recibo en pantalla. |
| **Origen** | Confirmado en entrevista con el dueño (Verificación de Rapidez). |
| **Prioridad** | Imprescindible |
| **Por qué importa** | Las ventas se realizan en horas pico con filas en 3 cajas. Si el software requiere más pasos, alentece el cobro y provoca que los empleados abandonen el sistema para anotar en papel. |
| **Afecta a** | RF-001, RF-003 |

---

#### RNF-SEG-001 · Restricción de acceso a costos por roles
| Campo | Contenido |
| :--- | :--- |
| **Atributo de calidad** | Seguridad (Control de Acceso) |
| **Descripción** | El sistema restringe el acceso al catálogo de proveedores, costos de compra y márgenes de ganancia únicamente a usuarios con rol de Administrador/Dueño mediante contraseña. |
| **Métrica** | 0% de accesos permitidos a vistas de proveedores o costos desde sesiones con rol de Cajero/Empleado. |
| **Origen** | Confirmado en entrevista con el dueño (Regla estricta no revelada inicialmente). |
| **Prioridad** | Imprescindible |
| **Por qué importa** | El dueño exige confidencialidad absoluta sobre sus márgenes de ganancia y los costos pactados con proveedores para evitar filtraciones o conflictos operativos. |
| **Afecta a** | RF-004 |

---

#### RNF-CON-001 · Registro inmutable de auditoría por transacción
| Campo | Contenido |
| :--- | :--- |
| **Atributo de calidad** | Confiabilidad (Trazabilidad e Integridad de datos) |
| **Descripción** | Todo movimiento de inventario, registro de apartado o venta almacena automáticamente fecha, hora exacta y el identificador del empleado en turno, impidiendo la modificación posterior del registro. |
| **Métrica** | 100% de las transacciones guardan marca de tiempo ($1\text{ segundo}$ de precisión) e ID de empleado, bloqueando comandos de alteración (UPDATE/DELETE) en la tabla de bitácora. |
| **Origen** | Derivado del tipo de sistema (Sistemas de Información). |
| **Prioridad** | Imprescindible |
| **Por qué importa** | Elimina la incertidumbre sobre descuadres de caja y discrepancias de mercancía, permitiendo auditorías objetivas sin depender de memorias. |
| **Afecta a** | RF-001, RF-002, RF-005, RF-006 |

---

## 5. Casos de uso

### 5.1 Relación de Casos de Uso del Sistema

1. **CU-01:** Registrar venta en caja (Caso de uso principal detallado)
2. **CU-02:** Registrar pedido apartado
3. **CU-03:** Acumular puntos de cliente frecuente
4. **CU-04:** Consultar comparativa de precios de proveedores
5. **CU-05:** Generar reporte de ventas por empleado
6. **CU-06:** Gestionar entregas y cancelaciones de apartados

---

### 5.2 Detalle del Caso de Uso Principal: CU-01 Registrar venta en caja

- **Identificador:** CU-01
- **Título:** Registrar venta en caja
- **Actor principal:** Cajero / Empleado
- **Objetivo:** Registrar los productos adquiridos por un cliente, procesar el cobro y descontar las existencias del inventario.
- **Precondición:** El cajero ha iniciado sesión en el sistema y se encuentra en la pantalla de Punto de Venta.

#### Escenario Principal (Flujo Feliz):
1. El cajero escanea el código de barras del primer producto presentado por el cliente.
2. El sistema valida la existencia del producto, añade el ítem a la lista de venta y actualiza el subtotal.
3. El cajero repite el paso 1 para cada producto restante.
4. El cajero presiona el botón "Finalizar Venta" (Paso 1 del cobro).
5. El sistema solicita opcionalmente el número telefónico del cliente para acumulación de puntos.
6. El cajero omite el número o ingresa un teléfono válido y presiona "Continuar a Pago" (Paso 2).
7. El cajero selecciona el método de pago en efectivo e ingresa el monto recibido (Paso 3).
8. El cajero confirma la transacción presionando "Cobrar" (Paso 4).
9. El sistema descuenta el stock de la base de datos, registra la transacción en la bitácora con la fecha, hora e ID de empleado, calcula el cambio y despliega el comprobante.

#### Flujos Alternos:
- **Flujo Alterno 2a (Producto sin código o ilogible):**
  1. En el paso 1, si el producto no tiene código visible o no se puede leer, el cajero solicita al cliente que tome un producto idéntico del estante que sí tenga código legible (regla del negocio).
  2. El producto dañado/sin código se coloca a un lado para etiquetado al final del día.
  3. El cajero escanea el nuevo producto recibido y el flujo continúa en el paso 2.

- **Flujo Alterno 2b (Stock insuficiente):**
  1. En el paso 2, el sistema detecta que la cantidad solicitada supera el stock disponible en base de datos.
  2. El sistema bloquea el agregado del ítem y muestra un mensaje de alerta: *"Stock insuficiente. Disponibles: X unidades"*.
  3. El cajero ajusta la cantidad al stock disponible o retira el producto del carrito y el flujo regresa al paso 3.

- **Flujo Alterno 6a (Cliente acumula puntos por número de teléfono):**
  1. En el paso 6, el cajero ingresa el número telefónico de 10 dígitos proporcionado por el cliente.
  2. El sistema valida que el teléfono pertenece a un cliente frecuente registrado y muestra su nombre en pantalla.
  3. El sistema calcula 1 punto por cada $10 MXN del total de la venta.
  4. Al confirmarse el pago en el paso 9, los puntos se suman automáticamente al saldo del cliente y el flujo continúa.

- **Postcondición:** El inventario de los productos vendidos se actualiza en tiempo real, se genera el registro inmutable en la bitácora y la pantalla queda limpia para la siguiente venta.
- **Requisitos que realiza:** RF-001, RF-003, RNF-USA-001, RNF-CON-001.

---

## 6. Trazabilidad

| Requisito | Origen | Caso de uso | Elemento del prototipo | Estado |
| :--- | :--- | :--- | :--- | :--- |
| **RF-001** | Entrevista 22 sep | CU-01 Registrar venta en caja | Pantalla Punto de Venta / Carrito | Vigente |
| **RF-002** | Entrevista 22 sep | CU-02 Registrar pedido apartado | Pantalla Módulo de Apartados | Vigente |
| **RF-003** | Entrevista 22 sep | CU-03 Acumular puntos de cliente | Modal Cliente Frecuente en Caja | Vigente |
| **RF-004** | Entrevista 22 sep | CU-04 Consultar comparativa proveedores | Pantalla Matriz de Proveedores | Vigente |
| **RF-005** | Entrevista 22 sep | CU-05 Generar reporte de ventas | Dashboard de Reportes / Gerencia | Vigente |
| **RF-006** | Entrevista 22 sep | CU-06 Gestionar entregas y apartados | Tabla de Apartados Expirados | Nuevo (Post-entrevista) |
| **RNF-USA-001** | Entrevista 22 sep | CU-01 Registrar venta en caja | Flujo de Cobro de 4 pasos | Vigente |
| **RNF-SEG-001** | Entrevista 22 sep | CU-04 Consultar comparativa proveedores | Control de Acceso y Login de Dueño | Vigente |
| **RNF-CON-001** | Tipo de Sistema | CU-01, CU-02, CU-05, CU-06 | Módulo de Bitácora / Auditoría | Vigente |

---

## 7. Registro de cambios

| Fecha | Requisito | Qué cambió | Por qué |
| :--- | :--- | :--- | :--- |
| 22/09/2026 | Todos | Creación inicial de especificación (v1.0). | Borrador intersemestral |
| 24/09/2026 | RF-002 | Se añadieron nombre y teléfono como datos obligatorios. | Solicitud explícita del cliente en la entrevista |
| 24/09/2026 | RF-006 | Incorporación del requisito RF-006 (Liberación en 7 días). | Regla de negocio descubierta en la entrevista |
| 24/09/2026 | RNF-SEG-001 | Ajuste en origen y prioridad estricta. | El dueño confirmó confidencialidad total de costos ante empleados |
| 24/09/2026 | Todos | Formalización de Casos de Uso, Trazabilidad y v2.0. | Preparación final de entregables de Unidad 2 |
