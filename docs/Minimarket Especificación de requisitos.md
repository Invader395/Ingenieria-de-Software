# Especificación de requisitos

**Sistema:** Minimarket Control  
**Autor:** Julián Guerrero Martínez  
**Versión:** 1.0  
**Fecha de la última actualización:** 22 de septiembre de 2026  

---

## 1. Propósito y alcance

**Propósito del documento:**  
Este documento define de forma precisa, comprobable y detallada los requisitos funcionales y no funcionales del sistema *Minimarket Control*. Está dirigido al desarrollador del sistema y al cliente (dueño del minimarket) para guiar las etapas de diseño, desarrollo de prototipos, pruebas y análisis de impacto de cambios.

**Alcance del sistema:**  
El sistema abarca la gestión interna del punto de venta y control operativo del negocio mediante:
- Registro de productos organizados por categoría.
- Descuento y actualización automática de existencias en tiempo real tras cada venta.
- Catálogo de proveedores con historial de precios de compra por producto para comparación de tarifas.
- Registro de ventas realizadas por el cajero o empleado en turno.
- Gestión de clientes frecuentes para la acumulación y canje de puntos, permitiendo también ventas sin registrar al cliente.
- Creación, consulta y liquidación (pago/entrega) de pedidos apartados con la reserva de los productos.
- Reporte diario de ventas por empleado y reporte de productos con stock por debajo del umbral mínimo especificado.

**Fuera del alcance:**  
- Procesamiento de pagos en línea mediante tarjetas de crédito, débito o pasarelas externas.
- Generación y timbrado de facturación electrónica automática.
- Servicio de logística, entrega a domicilio o aplicaciones móviles para clientes.

---

## 2. Usuarios y su contexto

| Usuario | Qué hace hoy sin el sistema | Qué espera del sistema |
| :--- | :--- | :--- |
| **Dueño / Administrador** | Cuenta productos manualmente y los compara con ventas anotadas. Revisa facturas de meses pasados en papel para comparar proveedores. Juzga el desempeño de empleados por memoria e impresiones generales. | Conocer la ganancia real y el stock exacto, comparar costos de proveedores en pantalla y evaluar el desempeño de los empleados con datos objetivos de ventas. |
| **Cajero / Empleado** | Anota apartados en hojas de papel que a veces se pierden. Cobra de memoria o con notas rápidas sin registro exacto de su turno. | Procesar ventas rápidamente sin generar filas, consultar pedidos apartados de forma ágil, registrar sus turnos y sumar puntos a clientes sin complicaciones. |

**Conflictos identificados entre usuarios:**  
- **Captura exhaustiva vs. Agilidad en caja:** El dueño requiere capturar datos exhaustivos (proveedor, lote, cliente) en cada transacción para asegurar la trazabilidad. Sin embargo, el cajero necesita un proceso de cobro ágil para no entorpecer la atención frente a las filas de clientes. Se resuelve automatizando el registro de empleado y fecha/hora en segundo plano, dejando la captura de proveedores solo para el módulo de compras/recepción del dueño.

---

## 3. Requisitos funcionales

### 3.1 Resumen

| ID | Nombre | Prioridad | Origen |
| :--- | :--- | :--- | :--- |
| **RF-001** | Registro de venta y descuento de inventario | Imprescindible | Visión del producto (Alcance / RF1) |
| **RF-002** | Registro de pedido apartado con reserva de stock | Imprescindible | Visión del producto (Alcance / RF2) |
| **RF-003** | Acumulación de puntos para cliente frecuente | Importante | Visión del producto (Alcance / RF3) y Supuesto propio |
| **RF-004** | Consulta comparativa de precios de proveedores | Imprescindible | Visión del producto (Alcance / RF4) |
| **RF-005** | Generación de reporte diario de ventas por empleado | Imprescindible | Visión del producto (Alcance / RF5) |

---

### 3.2 Fichas

#### RF-001 · Registro de venta y descuento de inventario
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema registra la venta de mercancía descontando automáticamente del inventario la cantidad de unidades vendidas. |
| **Origen** | Documento Visión del Producto (Sección 3: Alcance y Sección 6: RF1). |
| **Prioridad** | Imprescindible |
| **Criterio de aceptación** | Al confirmar el cobro de una venta con $N$ unidades de un producto, la existencia registrada en la base de datos para ese producto disminuye exactamente en $N$ unidades. Si las existencias son menores a $N$, el sistema impide la venta y muestra una alerta de stock insuficiente. |
| **Relacionado con** | RF-003, RNF-USA-001, RNF-CON-001 |

---

#### RF-002 · Registro de pedido apartado con reserva de stock
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema registra los datos del pedido apartado y reserva de inmediato las existencias de mercancía en la base de datos. |
| **Origen** | Documento Visión del Producto (Sección 3: Alcance y Sección 4: Reglas de Negocio). |
| **Prioridad** | Imprescindible |
| **Criterio de aceptación** | Al guardar un nuevo apartado, el stock disponible para venta directa en mostrador se reduce en la cantidad indicada. Si se consulta la lista de productos, dicha cantidad reservada no está disponible para venta general hasta que el apartado sea cancelado o entregado. |
| **Relacionado con** | RF-001, RNF-CON-001 |

---

#### RF-003 · Acumulación de puntos para cliente frecuente
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema solicita el número telefónico del cliente frecuente para acumular los puntos correspondientes a su compra. |
| **Origen** | Documento Visión del Producto (Sección 6: Supuestos propios) y Registro de clientes. |
| **Prioridad** | Importante |
| **Criterio de aceptación** | Al ingresar un número telefónico registrado previamente durante el proceso de cobro, el sistema calcula 1 punto por cada $10 MXN de compra y los suma al saldo acumulado del cliente al finalizar la transacción. Si el número no existe, el sistema indica el error y permite realizar la venta en modo anónimo sin acumulación. |
| **Relacionado con** | RF-001, RNF-USA-001 |

---

#### RF-004 · Consulta comparativa de precios de proveedores
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema muestra una lista comparativa de los precios de compra de un producto registrados por cada proveedor. |
| **Origen** | Documento Visión del Producto (Sección 2: Problema del dueño y Sección 6: RF4). |
| **Prioridad** | Imprescindible |
| **Criterio de aceptación** | Al seleccionar un producto en el módulo de proveedores, el sistema desglosa todos los proveedores que lo surten junto con el costo de compra unitario asignado a cada uno, ordenados de menor a mayor precio. |
| **Relacionado con** | RNF-SEG-001 |

---

#### RF-005 · Generación de reporte diario de ventas por empleado
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema genera un reporte diario consolidado de ventas clasificando las transacciones por el empleado en turno que las procesó. |
| **Origen** | Documento Visión del Producto (Sección 1: Descripción y Sección 6: RF5). |
| **Prioridad** | Imprescindible |
| **Criterio de aceptación** | Al seleccionar una fecha específica, el sistema genera un desglose totalizador indicando cada empleado, el número de ventas procesadas por él y la cantidad total de dinero recaudado en su turno. |
| **Relacionado con** | RNF-CON-001 |

---

## 4. Requisitos no funcionales

### 4.1 Resumen

| ID | Atributo | Nombre | Prioridad | Origen |
| :--- | :--- | :--- | :--- | :--- |
| **RNF-USA-001** | Usabilidad | Pasos máximos para el proceso de cobro | Imprescindible | Visión del Producto (Sección 4: Atributos y Sección 6: RNF1) |
| **RNF-SEG-001** | Seguridad | Restricción de acceso por roles de usuario | Imprescindible | Visión del Producto (Sección 4: Control de acceso y Sección 6: RNF2) |
| **RNF-CON-001** | Confiabilidad / Trazabilidad | Registro inmutable de auditoría por transacción | Imprescindible | Visión del Producto (Sección 4: Trazabilidad e Integridad y Sección 6: RNF3) |

---

### 4.2 Fichas

#### RNF-USA-001 · Pasos máximos para el proceso de cobro
| Campo | Contenido |
| :--- | :--- |
| **Atributo de calidad** | Usabilidad |
| **Descripción** | El proceso completo de cobro y registro de una venta en mostrador se realiza en menos de cuatro pasos de navegación en la pantalla. |
| **Métrica** | Máximo 4 clics o confirmaciones de teclado desde la selección del último producto hasta la impresión/cierre de la pantalla de cobro. |
| **Origen** | Supuesto propio respaldado por la necesidad operativa expresada por el cajero en la Visión del Producto. |
| **Prioridad** | Imprescindible |
| **Por qué importa** | La venta ocurre en el mostrador con filas de clientes. Un proceso largo causa cuellos de botella y provoca que el cajero deje de usar el sistema para anotar en papel. |
| **Afecta a** | RF-001, RF-003 |

---

#### RNF-SEG-001 · Restricción de acceso por roles de usuario
| Campo | Contenido |
| :--- | :--- |
| **Atributo de calidad** | Seguridad |
| **Descripción** | El sistema restringe el acceso al módulo de costos de compra y catálogo de proveedores únicamente a los usuarios con rol de Administrador/Dueño mediante contraseña. |
| **Métrica** | 0% de accesos no autorizados permitidos desde cuentas con rol de Cajero/Empleado al intentar navegar directamente o ejecutar funciones del módulo de proveedores y costos. |
| **Origen** | Derivado de la preocupación explícita del dueño expresada en la Visión del Producto. |
| **Prioridad** | Imprescindible |
| **Por qué importa** | Previene que los empleados conozcan los márgenes de ganancia, costos de adquisición o acuerdos comerciales privados con proveedores. |
| **Afecta a** | RF-004 |

---

#### RNF-CON-001 · Registro inmutable de auditoría por transacción
| Campo | Contenido |
| :--- | :--- |
| **Atributo de calidad** | Confiabilidad (Trazabilidad e Integridad de datos) |
| **Descripción** | Todo movimiento de inventario, registro de apartado o venta almacena automáticamente la fecha, hora exacta y el identificador del empleado en turno sin permitir la edición posterior de dicho registro. |
| **Métrica** | 100% de los registros de auditoría (*logs*) almacenan la fecha y la hora con un margen de precisión de hasta 1 segundo y el código del empleado en turno, bloqueando cualquier comando de modificación (UPDATE/DELETE) sobre la bitácora. |
| **Origen** | Derivado del tipo de sistema (Sistemas de Información) y necesidad de control de pérdidas. |
| **Prioridad** | Imprescindible |
| **Por qué importa** | Garantiza la integridad de la información y permite deslindar responsabilidades operativas ante pérdidas de mercancía o faltantes en caja. |
| **Afecta a** | RF-001, RF-002, RF-005 |

---

## 5. Casos de uso

*(Se trabajarán en la semana 7, después de realizar la entrevista de elicitación correspondiente).*

---

## 6. Trazabilidad

| Requisito | Origen | Caso de uso | Elemento del prototipo |
| :--- | :--- | :--- | :--- |
| **RF-001** | Visión del producto / Alcance | *Por definir (Semana 7)* | Pantalla de Punto de Venta / Caja |
| **RF-002** | Visión del producto / Alcance | *Por definir (Semana 7)* | Módulo de Apartados |
| **RF-003** | Visión del producto / Supuesto | *Por definir (Semana 7)* | Modal de Cliente Frecuente |
| **RF-004** | Visión del producto / Alcance | *Por definir (Semana 7)* | Módulo de Comparación de Proveedores |
| **RF-005** | Visión del producto / Alcance | *Por definir (Semana 7)* | Pantalla de Reportes y Gerencia |
| **RNF-USA-001** | Visión del producto / Atributos | *Por definir (Semana 7)* | Flujo de Cobro en Pantalla de Caja |
| **RNF-SEG-001** | Visión del producto / Atributos | *Por definir (Semana 7)* | Control de Sesión y Roles |
| **RNF-CON-001** | Visión del producto / Atributos | *Por definir (Semana 7)* | Bitácora de Transacciones / Log |

---

## 7. Registro de cambios

| Fecha | Requisito | Qué cambió | Por qué |
| :--- | :--- | :--- | :--- |
| 22/09/2026 | Todos | Creación del documento inicial de especificación de requisitos (v1.0). | Entrega intersemestral |
