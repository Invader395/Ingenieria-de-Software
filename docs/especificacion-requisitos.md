# Especificación de requisitos

**Sistema:** Minimarket Control  
**Autor:** Julián Guerrero Martínez  
**Versión:** 2.4  
**Fecha de la última actualización:** 28 de septiembre de 2026  

---

## 1. Propósito y alcance

**Propósito del documento:**  
Este documento define de forma precisa, comprobable y detallada los requisitos funcionales y no funcionales del sistema *Minimarket Control*. Está dirigido al desarrollador del sistema, a la dupla evaluadora y al cliente (dueño del minimarket) para guiar las etapas de diseño, desarrollo de prototipos en Figma, pruebas de software y análisis de impacto de cambios.

**Alcance del sistema:**  
El sistema abarca la gestión interna del punto de venta y control operativo del negocio mediante:
- Autenticación y control de acceso por roles (Administrador y Cajero).
- Registro, actualización y catálogo de productos organizados por categoría.
- Descuento y actualización automática de existencias en tiempo real tras cada venta y registro de mermas.
- Generación automática de alertas de stock mínimo y consulta de reportes de productos por reabastecer.
- Catálogo de proveedores con registro de datos de contacto e historial de precios de compra por producto para comparación de tarifas.
- Registro de ventas realizadas en caja asociadas automáticamente al empleado en turno.
- Gestión de clientes frecuentes (alta de cliente por teléfono, acumulación y canje de puntos), permitiendo también ventas anónimas.
- Creación, consulta, liquidación y entrega de pedidos apartados con reserva inmediata de mercancía por un plazo máximo de 7 días naturales.
- Consulta de reporte diario consolidado de ventas por empleado.

**Fuera del alcance:**  
- Procesamiento de pagos en línea mediante tarjetas de crédito, débito o pasarelas externas.
- Generación y timbrado de facturación electrónica automática.
- Servicio de logística, entrega a domicilio o aplicaciones móviles para clientes.

---

## 2. Usuarios y su contexto

| Usuario | Qué hace hoy sin el sistema | Qué espera del sistema |
| :--- | :--- | :--- |
| **Dueño / Administrador** | Cuenta productos a mano y los compara con ventas. Revisa facturas físicas de papel en carpetas durante 2 o 3 días a la semana para comparar proveedores. Juzga el desempeño de empleados por impresiones. | Conocer la ganancia real y stock exacto. Comparar costos de proveedores en pantalla para elegir la mejor tarifa. Proteger la confidencialidad de sus costos frente a empleados y consultar las ventas por turno y productos por reabastecer con datos objetivos. |
| **Cajero / Empleado** | Anota apartados en libretas de papel que se traspapelan y generan disputas. Cobra registrando notas en mano. Si un producto no tiene código, pide al cliente traer otro. | Procesar ventas en menos de 4 pasos para evitar filas. Consultar e identificar apartados rápidamente por nombre/teléfono sin depender de libretas. Acumular y canjear puntos por teléfono fácilmente. |

**Conflictos identificados entre usuarios:**  
- **Captura exhaustiva vs. Agilidad en caja:** El dueño requería capturar datos exhaustivos (proveedor, lote) en cada cobro. El cajero necesita agilidad para evitar filas en las 3 cajas. **Solución:** Se automatiza el registro de empleado, fecha y hora en segundo plano tras iniciar turno, limitando la captura manual en caja a los productos y el teléfono del cliente.

---

## 3. Requisitos funcionales

### 3.1 Resumen

| ID | Nombre del Requisito (Infinitivo) | Prioridad | Origen |
| :--- | :--- | :--- | :--- |
| **RF-001** | Iniciar sesión en el sistema | Imprescindible | Derivado de la gestión de turnos y seguridad por rol |
| **RF-002** | Cerrar sesión en el sistema | Imprescindible | Derivado del control de acceso por turnos |
| **RF-003** | Dar de alta de productos | Imprescindible | Confirmado en entrevista (Gestión de catálogo) |
| **RF-004** | Dar de baja de productos | Importante | Confirmado en entrevista (Gestión de catálogo) |
| **RF-005** | Modificar datos de productos | Importante | Confirmado en entrevista (Gestión de catálogo) |
| **RF-006** | Consultar productos en stock | Imprescindible | Confirmado en entrevista (Control de existencias) |
| **RF-007** | Registrar producto con proveedor | Imprescindible | Confirmado en entrevista (Matriz de costos) |
| **RF-008** | Registrar venta en caja | Imprescindible | Confirmado en entrevista (Proceso actual 3) |
| **RF-009** | Descontar existencias de inventario por venta | Imprescindible | Confirmado en entrevista (Proceso actual 3) |
| **RF-010** | Validar disponibilidad de stock antes de cobro | Imprescindible | Confirmado en entrevista (Regla de negocio) |
| **RF-011** | Registrar pedido apartado | Imprescindible | Confirmado en entrevista (Excepción 1) |
| **RF-012** | Reservar mercancía de apartado en inventario | Imprescindible | Confirmado en entrevista (Excepción 1) |
| **RF-013** | Acumular puntos para cliente frecuente | Importante | Confirmado en entrevista (Verificación Puntos) |
| **RF-014** | Consultar comparativa de precios de proveedores | Imprescindible | Confirmado en entrevista (Proceso actual 2) |
| **RF-015** | Consultar reporte diario de ventas por empleado | Imprescindible | Confirmado en entrevista (Proceso actual 3) |
| **RF-016** | Liberar automáticamente mercancía de apartados expirados | Importante | Descubrimiento en entrevista (Límite 7 días) |
| **RF-017** | Registrar merma de productos | Importante | Derivado del control de inventario y pérdidas |
| **RF-018** | Registrar alta de cliente frecuente | Imprescindible | Confirmado en entrevista (Prerrequisito de puntos) |
| **RF-019** | Registrar proveedor | Imprescindible | Confirmado en entrevista (Gestión de proveedores) |
| **RF-020** | Generar alerta automática de stock bajo umbral mínimo | Imprescindible | Declarante en la Visión del Producto |
| **RF-021** | Canjear puntos de cliente frecuente | Importante | Declarante en la Visión del Producto |
| **RF-022** | Liquidar pedido apartado | Imprescindible | Confirmado en entrevista (Excepción 1) |
| **RF-023** | Consultar reporte de productos con stock bajo umbral mínimo | Imprescindible | Declarante en la Visión del Producto |

---

### 3.2 Fichas

#### RF-001 · Iniciar sesión en el sistema
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema autentica la identidad del usuario mediante credenciales (usuario y contraseña) para asignar los permisos correspondientes según su rol (Administrador o Cajero). |
| **Origen** | Derivado del control de turnos y seguridad por rol. |
| **Prioridad** | Imprescindible |
| **Criterio de aceptación** | Al ingresar un usuario y contraseña válidos, el sistema inicia la sesión en la interfaz correspondiente a su rol. Si las credenciales son incorrectas, el sistema bloquea el acceso y despliega el mensaje *"Credenciales inválidas"*. |
| **Relacionado con** | RF-002, RNF-SEG-001 |

---

#### RF-002 · Cerrar sesión en el sistema
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema finaliza la sesión activa del usuario actual y retorna a la pantalla de autenticación. |
| **Origen** | Derivado del control de acceso por turnos. |
| **Prioridad** | Imprescindible |
| **Criterio de aceptación** | Al presionar el botón *"Cerrar sesión"*, el sistema destruye el token de sesión activo, bloquea el acceso a las funciones del punto de venta y muestra la pantalla de inicio de sesión. |
| **Relacionado con** | RF-001, RNF-CON-001 |

---

#### RF-003 · Alta de productos
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema registra un nuevo producto en el catálogo solicitando código de barras, nombre, categoría, precio de venta y stock mínimo. |
| **Origen** | Confirmado en entrevista con el dueño (Gestión de catálogo). |
| **Prioridad** | Imprescindible |
| **Criterio de aceptación** | Al guardar un producto con todos los campos obligatorios completos y un código de barras no duplicado, el sistema crea el registro en la base de datos y lo despliega en el catálogo general. |
| **Relacionado con** | RF-004, RF-005, RF-006, RF-020 |

---

#### RF-004 · Baja de productos
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema deshabilita un producto del catálogo para impedir su selección en ventas futuras manteniendo su historial de transacciones. |
| **Origen** | Confirmado en entrevista con el dueño (Gestión de catálogo). |
| **Prioridad** | Importante |
| **Criterio de aceptación** | Al confirmar la baja de un producto, el sistema cambia su estado a *"Inactivo"*, eliminándolo de las búsquedas en caja pero conservando su registro en reportes históricos. |
| **Relacionado con** | RF-003, RF-008 |

---

#### RF-005 · Modificar datos de productos
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema actualiza los datos informativos de un producto existente (nombre, categoría, precio de venta o stock mínimo). |
| **Origen** | Confirmado en entrevista con el dueño (Gestión de catálogo). |
| **Prioridad** | Importante |
| **Criterio de aceptación** | Al guardar los cambios sobre un producto seleccionado, el sistema valida la coherencia de los datos y refleja los nuevos valores inmediatamente en todo el sistema. |
| **Relacionado con** | RF-003, RF-006, RF-020 |

---

#### RF-006 · Consulta de productos en stock
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema muestra la cantidad de unidades disponibles en inventario para un producto específico o listado general. |
| **Origen** | Confirmado en entrevista con el dueño (Control de existencias). |
| **Prioridad** | Imprescindible |
| **Criterio de aceptación** | Al realizar la búsqueda por código de barras o nombre, el sistema despliega el stock actual en tiempo real. |
| **Relacionado con** | RF-003, RF-009, RF-012, RF-020, RF-023 |

---

#### RF-007 · Registrar producto con proveedor
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema vincula un producto existente con un proveedor registrado guardando el costo de compra unitario. |
| **Origen** | Confirmado en entrevista con el dueño (Matriz de costos). |
| **Prioridad** | Imprescindible |
| **Criterio de aceptación** | Al ingresar el identificador de un proveedor registrado, el producto y el costo de adquisición, el sistema añade el registro a la matriz histórica de costos del producto. |
| **Relacionado con** | RF-003, RF-014, RF-019, RNF-SEG-001 |

---

#### RF-008 · Registrar venta en caja
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema procesa la transacción comercial asignando el monto pagado, el desglose de artículos, la marca de tiempo y el identificador del cajero en turno. |
| **Origen** | Confirmado en entrevista con el dueño (Proceso actual 3). |
| **Prioridad** | Imprescindible |
| **Criterio de aceptación** | Al confirmar el cobro de la venta, el sistema genera la transacción asociando la fecha, la hora exacta, el ID del cajero en turno y emite el recibo digital/impreso. |
| **Relacionado con** | RF-009, RF-010, RNF-USA-001, RNF-CON-001 |

---

#### RF-009 · Descontar existencias de inventario por venta
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema resta automáticamente de las existencias generales las unidades comercializadas al finalizar una venta. |
| **Origen** | Confirmado en entrevista con el dueño (Proceso actual 3). |
| **Prioridad** | Imprescindible |
| **Criterio de aceptación** | Tras confirmarse una venta de $N$ unidades de un producto, la cantidad en inventario para dicho ítem disminuye exactamente en $N$ unidades en tiempo real. |
| **Relacionado con** | RF-006, RF-008, RF-020, RNF-CON-001 |

---

#### RF-010 · Validar disponibilidad de stock antes de cobro
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema verifica que la cantidad solicitada de un producto no supere las existencias físicas disponibles en la base de datos antes de permitir el cobro. |
| **Origen** | Confirmado en entrevista con el dueño (Regla de negocio). |
| **Prioridad** | Imprescindible |
| **Criterio de aceptación** | Si la cantidad ingresada en el carrito supera las unidades disponibles en inventario, el sistema bloquea la adición del producto y despliega el mensaje *"Stock insuficiente. Disponibles: X unidades"*. |
| **Relacionado con** | RF-006, RF-008 |

---

#### RF-011 · Registrar pedido apartado
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema crea un pedido diferido vinculando los productos elegidos al nombre completo y número telefónico del cliente. |
| **Origen** | Confirmado en entrevista con el dueño (Excepción 1). |
| **Prioridad** | Imprescindible |
| **Criterio de aceptación** | Al guardar el pedido especificando nombre y teléfono del cliente, el sistema genera un folio único con estado *"Pendiente de liquidación"* y fecha límite fijada en 7 días naturales. |
| **Relacionado con** | RF-012, RF-016, RF-022 |

---

#### RF-012 · Reservar mercancía de apartado en inventario
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema descuenta del stock disponible para venta directa las unidades asociadas a un nuevo pedido apartado. |
| **Origen** | Confirmado en entrevista con el dueño (Excepción 1). |
| **Prioridad** | Imprescindible |
| **Criterio de aceptación** | Al generarse un pedido apartado con $N$ unidades, las existencias para venta en mostrador se reducen en $N$ unidades y quedan congeladas exclusivamente para dicho pedido. |
| **Relacionado con** | RF-006, RF-011, RF-016, RF-020 |

---

#### RF-013 · Acumular puntos para cliente frecuente
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema calcula y suma puntos en el saldo del cliente en función del importe total cobrado al ingresar su número telefónico. |
| **Origen** | Confirmado en entrevista con el dueño (Verificación Puntos). |
| **Prioridad** | Importante |
| **Criterio de aceptación** | Al ingresar un teléfono registrado durante el cobro, el sistema abona 1 punto por cada $10 MXN de la venta al saldo del cliente. Si el número no existe, procesa la venta como anónima o permite su alta (RF-018) sin detener la transacción. |
| **Relacionado con** | RF-008, RF-018, RF-021, RNF-USA-001 |

---

#### RF-014 · Consultar comparativa de precios de proveedores
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema muestra un listado de los costos históricos de compra de un producto provistos por diferentes proveedores. |
| **Origen** | Confirmado en entrevista con el dueño (Proceso actual 2). |
| **Prioridad** | Imprescindible |
| **Criterio de aceptación** | Al seleccionar un producto en el módulo de proveedores, el sistema despliega la lista de proveedores asociados ordenados del costo unitario más bajo al más alto. |
| **Relacionado con** | RF-007, RF-019, RNF-SEG-001 |

---

#### RF-015 · Consultar reporte diario de ventas por empleado
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema permite al usuario autenticado consultar en pantalla un resumen consolidado de ingresos y ventas desglosadas por cada empleado en una fecha seleccionada. |
| **Origen** | Confirmado en entrevista con el dueño (Proceso actual 3). |
| **Prioridad** | Imprescindible |
| **Criterio de aceptación** | Al seleccionar una fecha en el panel de reportes y presionar "Consultar", el sistema despliega en pantalla la lista de cajeros con: total acumulado en dinero ($MXN$), número de ventas realizadas y promedio de venta por transacción. |
| **Relacionado con** | RF-008, RNF-CON-001 |

---

#### RF-016 · Liberar automáticamente mercancía de apartados expirados
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema cancela los apartados no liquidados tras 7 días naturales y reintegra las unidades reservadas al stock disponible. |
| **Origen** | Descubrimiento en entrevista con el dueño (Excepción 1). |
| **Prioridad** | Importante |
| **Criterio de aceptación** | A las 00:00 horas del octavo día natural tras la creación de un apartado no pagado, el sistema cambia su estado a *"Expirado"* e incrementa el stock disponible para venta directa en la misma cantidad de unidades que estaba congelada. |
| **Relacionado con** | RF-011, RF-012, RNF-CON-001 |

---

#### RF-017 · Registrar merma de productos
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema reduce el inventario de un producto por concepto de daño, caducidad o extravío, registrando la justificación. |
| **Origen** | Derivado del control de inventario y pérdidas en tienda. |
| **Prioridad** | Importante |
| **Criterio de aceptación** | Al ingresar la cantidad defectuosa y el motivo de la merma, el sistema resta las unidades del inventario total y genera un asiento inmutable en la bitácora de mermas. |
| **Relacionado con** | RF-006, RF-020, RNF-CON-001 |

---

#### RF-018 · Registrar alta de cliente frecuente
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema registra un nuevo cliente frecuente solicitando su nombre completo y número telefónico de 10 dígitos para habilitar la acumulación y consulta de puntos. |
| **Origen** | Confirmado en entrevista con el dueño (Prerrequisito para acumulación de puntos). |
| **Prioridad** | Imprescindible |
| **Criterio de aceptación** | Al ingresar un nombre completo y un número telefónico de 10 dígitos no duplicado en el módulo de clientes o durante la venta, el sistema crea el registro con un saldo inicial de 0 puntos. Si el número telefónico ya existe en el sistema, este bloquea el registro y despliega el mensaje *"El número telefónico ya se encuentra registrado"*. |
| **Relacionado con** | RF-013, RF-021, RNF-USA-001 |

---

#### RF-019 · Registrar proveedor
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema registra un nuevo proveedor ingresando su nombre comercial, teléfono de contacto y dirección. |
| **Origen** | Confirmado en entrevista con el dueño (Gestión de proveedores). |
| **Prioridad** | Imprescindible |
| **Criterio de aceptación** | Al guardar un proveedor con nombre comercial no existente previamente, el sistema genera la ficha del proveedor en la base de datos permitiendo asignarle productos y precios de compra. |
| **Relacionado con** | RF-007, RF-014, RNF-SEG-001 |

---

#### RF-020 · Generar alerta automática de stock bajo umbral mínimo
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema evalúa en tiempo real las existencias tras cada movimiento de inventario (venta, reserva o merma) y genera un indicador o notificación de alerta cuando la cantidad disponible es igual o inferior al stock mínimo configurado. |
| **Origen** | Declarante explícito en la Visión del Producto (Sección Alcance) y reglas de inventario. |
| **Prioridad** | Imprescindible |
| **Criterio de aceptación** | Inmediatamente después de que una transacción (RF-009, RF-012, RF-017) reduzca el stock disponible de un producto a un nivel $\le$ stock mínimo del ítem, el sistema marca el producto con la bandera/alerta visual de *"Stock crítico"* en el sistema. |
| **Relacionado con** | RF-003, RF-006, RF-009, RF-012, RF-017, RF-023 |

---

#### RF-021 · Canjear puntos de cliente frecuente
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema descuenta un monto determinado de puntos acumulados por un cliente para aplicarlo como descuento equivalente en dinero durante el cobro. |
| **Origen** | Declarante explícito en la Visión del Producto (Sección Alcance y Reglas de negocio). |
| **Prioridad** | Importante |
| **Criterio de aceptación** | Al seleccionar la opción "Canjear puntos" en caja e ingresar los puntos a aplicar ($\le$ saldo actual del cliente), el sistema calcula el descuento monetario equivalente, restando el total de puntos del saldo del cliente y descontando dicho importe del total a pagar. |
| **Relacionado con** | RF-008, RF-013, RF-018, RNF-USA-001 |

---

#### RF-022 · Liquidar pedido apartado
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema registra el pago del saldo pendiente de un pedido apartado, cambiando su estado a "Liquidado/Entregado". |
| **Origen** | Confirmado en entrevista con el dueño (Excepción 1). |
| **Prioridad** | Imprescindible |
| **Criterio de aceptación** | Al buscar un pedido apartado vigente por folio o teléfono de cliente y confirmar el cobro de la cantidad adeudada, el sistema cambia el estado del apartado a *"Liquidado"*, registra la venta cobrada asignada al cajero y libera el registro de reserva. |
| **Relacionado con** | RF-008, RF-011, RF-012, RNF-CON-001 |

---

#### RF-023 · Consultar reporte de productos con stock bajo umbral mínimo
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema permite al usuario autenticado consultar en pantalla un listado con todos los productos cuya existencia disponible sea menor o igual a su umbral mínimo, indicando la cantidad faltante para reabastecer. |
| **Origen** | Declarante explícito en la Visión del Producto (Sección Alcance). |
| **Prioridad** | Imprescindible |
| **Criterio de aceptación** | Al acceder al panel de reportes de inventario y seleccionar "Ver productos con stock bajo", el sistema despliega en pantalla la tabla con: código de producto, nombre, stock actual, stock mínimo y unidades faltantes sugeridas para reabastecer. |
| **Relacionado con** | RF-006, RF-020 |

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
| **Afecta a** | RF-008, RF-013, RF-018, RF-021 |

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
| **Afecta a** | RF-001, RF-007, RF-014, RF-019 |

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
| **Afecta a** | RF-002, RF-008, RF-009, RF-011, RF-015, RF-016, RF-017, RF-022 |

---

## 5. Casos de uso

### 5.1 Relación de Casos de Uso del Sistema

1. **CU-01:** Registrar venta en caja (Caso de uso principal detallado)
2. **CU-02:** Registrar y liquidar pedido apartado
3. **CU-03:** Gestionar cliente frecuente (Alta, acumulación y canje de puntos)
4. **CU-04:** Gestionar proveedores y consultar comparativa de precios
5. **CU-05:** Consultar reportes de ventas por empleado y stock bajo
6. **CU-06:** Gestionar mermas y cancelaciones de apartados

---

### 5.2 Detalle del Caso de Uso Principal: CU-01 Registrar venta en caja

- **Identificador:** CU-01
- **Título:** Registrar venta en caja
- **Actor principal:** Cajero / Empleado
- **Objetivo:** Registrar los productos adquiridos por un cliente, procesar el cobro, acumular/canjear puntos y descontar las existencias del inventario.
- **Precondición:** El cajero ha iniciado sesión en el sistema (RF-001) y se encuentra en la pantalla de Punto de Venta.

#### Escenario Principal (Flujo Feliz):
1. El cajero escanea el código de barras del primer producto presentado por el cliente.
2. El sistema valida la disponibilidad de stock (RF-010), añade el ítem a la lista de venta y actualiza el subtotal.
3. El cajero repite el paso 1 para cada producto restante.
4. El cajero presiona el botón "Finalizar Venta" (Paso 1 del cobro).
5. El sistema solicita opcionalmente el número telefónico del cliente para acumular/canjear puntos.
6. El cajero omite el número o ingresa un teléfono válido y presiona "Continuar a Pago" (Paso 2).
7. El cajero selecciona el método de pago en efectivo e ingresa el monto recibido (Paso 3).
8. El cajero confirma la transacción presionando "Cobrar" (Paso 4).
9. El sistema descuenta el stock de la base de datos (RF-009), evalúa si el nivel activa la alerta de stock crítico (RF-020), registra la transacción en la bitácora con la fecha, hora e ID de empleado, calcula los puntos abonados (RF-013), calcula el cambio y despliega el comprobante.

#### Flujos Alternos:
- **Flujo Alterno 2a (Producto sin código o ilegible):**
  1. En el paso 1, si el producto no tiene código visible o no se puede leer, el cajero solicita al cliente que tome un producto idéntico del estante que sí tenga código legible (regla del negocio).
  2. El producto dañado/sin código se coloca a un lado para etiquetado o registro de merma (RF-017).
  3. El cajero escanea el nuevo producto recibido y el flujo continúa en el paso 2.

- **Flujo Alterno 2b (Stock insuficiente - RF-010):**
  1. En el paso 2, el sistema detecta que la cantidad solicitada supera el stock disponible en base de datos.
  2. El sistema bloquea el agregado del ítem y muestra un mensaje de alerta: *"Stock insuficiente. Disponibles: X unidades"*.
  3. El cajero ajusta la cantidad al stock disponible o retira el producto del carrito y el flujo regresa al paso 3.

- **Flujo Alterno 6a (Cliente no registrado solicita alta inmediata - RF-018):**
  1. En el paso 6, el cajero ingresa el número telefónico proporcionado por el cliente y el sistema indica que no existe.
  2. El cajero presiona el botón "Registrar cliente".
  3. El cajero ingresa el nombre completo del cliente y confirma.
  4. El sistema crea el perfil del cliente (RF-018) y retorna al flujo de cobro manteniendo los productos en el carrito.
  5. El flujo continúa en el paso 7 procesando los puntos correspondientes a la compra actual (RF-013).

- **Flujo Alterno 6b (Cliente canjea puntos - RF-021):**
  1. En el paso 6, tras ingresar un teléfono registrado, el sistema muestra el saldo de puntos disponibles.
  2. El cajero selecciona "Canjear puntos".
  3. El sistema calcula el descuento equivalente en $MXN y lo resta del total a pagar.
  4. El flujo continúa en el paso 7 con el importe ajustado.

- **Postcondición:** El inventario de los productos vendidos se actualiza en tiempo real, se actualizan las alertas de stock si corresponde, se genera el registro inmutable en la bitácora y la pantalla queda limpia para la siguiente venta.
- **Requisitos que realiza:** RF-008, RF-009, RF-010, RF-013, RF-018, RF-020, RF-021, RNF-USA-001, RNF-CON-001.

---

## 6. Trazabilidad

| Requisito | Origen | Caso de uso | Elemento del prototipo | Estado |
| :--- | :--- | :--- | :--- | :--- |
| **RF-001** | Derivado de seguridad | CU-01 Registrar venta en caja | Pantalla Login / Autenticación | Vigente |
| **RF-002** | Derivado de seguridad | CU-01 Registrar venta en caja | Botón / Menú Cerrar Sesión | Vigente |
| **RF-003** | Entrevista 22 sep | CU-03 Gestión de catálogo | Modal Alta de Producto | Vigente |
| **RF-004** | Entrevista 22 sep | CU-03 Gestión de catálogo | Tabla Catálogo / Acciones | Vigente |
| **RF-005** | Entrevista 22 sep | CU-03 Gestión de catálogo | Modal Editar Producto | Vigente |
| **RF-006** | Entrevista 22 sep | CU-01, CU-03 | Buscador de Inventario / Alerta Stock | Vigente |
| **RF-007** | Entrevista 22 sep | CU-04 Consultar proveedores | Matriz Proveedor-Producto | Vigente |
| **RF-008** | Entrevista 22 sep | CU-01 Registrar venta en caja | Pantalla Punto de Venta / Cobro | Vigente |
| **RF-009** | Entrevista 22 sep | CU-01 Registrar venta en caja | Contador de Inventario en BD | Vigente |
| **RF-010** | Entrevista 22 sep | CU-01 Registrar venta en caja | Alerta Modal de Stock Insuficiente | Vigente |
| **RF-011** | Entrevista 22 sep | CU-02 Registrar pedido apartado | Pantalla Módulo de Apartados | Vigente |
| **RF-012** | Entrevista 22 sep | CU-02 Registrar pedido apartado | Indicador Stock Reservado | Vigente |
| **RF-013** | Entrevista 22 sep | CU-01, CU-03 | Modal Cliente Frecuente en Caja | Vigente |
| **RF-014** | Entrevista 22 sep | CU-04 Consultar proveedores | Pantalla Comparativa de Precios | Vigente |
| **RF-015** | Entrevista 22 sep | CU-05 Consultar reportes | Dashboard de Reportes / Ventas | Vigente |
| **RF-016** | Entrevista 22 sep | CU-06 Gestionar apartados | Tabla de Apartados Expirados | Vigente |
| **RF-017** | Derivado de inventario | CU-06 Gestionar mermas | Formulario de Registro de Merma | Vigente |
| **RF-018** | Entrevista 22 sep | CU-01, CU-03 | Modal Alta Rápida de Cliente | Vigente |
| **RF-019** | Visión del producto | CU-04 Consultar proveedores | Modal Registrar Proveedor | Vigente |
| **RF-020** | Visión del producto | CU-01, CU-03, CU-05 | Badge / Indicador de Alerta de Stock Crítico | Vigente |
| **RF-021** | Visión del producto | CU-01, CU-03 | Opción Canje de Puntos en Caja | Vigente |
| **RF-022** | Entrevista 22 sep | CU-02 Registrar pedido apartado | Modal Cobro / Liquidar Apartado | Vigente |
| **RF-023** | Visión del producto | CU-05 Consultar reportes | Reporte de Reabastecimiento / Umbral | Vigente |
| **RNF-USA-001** | Entrevista 22 sep | CU-01 Registrar venta en caja | Flujo de Cobro de 4 pasos | Vigente |
| **RNF-SEG-001** | Entrevista 22 sep | CU-04 Consultar proveedores | Control de Acceso y Login de Dueño | Vigente |
| **RNF-CON-001** | Tipo de Sistema | Todos los casos de uso | Módulo de Bitácora / Auditoría | Vigente |

---

## 7. Registro de cambios

| Fecha | Requisito | Qué cambió | Por qué |
| :--- | :--- | :--- | :--- |
| 22/09/2026 | Todos | Creación inicial de especificación (v1.0). | Borrador intersemestral |
| 24/09/2026 | RF-011 | Se añadieron nombre y teléfono como datos obligatorios. | Solicitud explícita del cliente en la entrevista |
| 24/09/2026 | RF-016 | Incorporación del requisito RF-016 (Liberación en 7 días). | Regla de negocio descubierta en la entrevista |
| 24/09/2026 | RNF-SEG-001 | Ajuste en origen y prioridad estricta. | El dueño confirmó confidencialidad total de costos ante empleados |
| 28/09/2026 | RF-001 a RF-017 | Desglose atómico en infinitivo e independización de reglas (RF-009, RF-010, RF-012, RF-017). | Adecuación a la guía de redacción de requisitos (v2.0) |
| 28/09/2026 | RF-018 | Incorporación del requisito RF-018 (Alta de cliente frecuente) como Imprescindible. | Prerrequisito indispensable confirmado para poder acumular puntos |
| 28/09/2026 | RF-019 a RF-022 | Adición de RF-019 (Registrar proveedor), RF-020 (Reporte stock bajo umbral), RF-021 (Canjear puntos) y RF-022 (Liquidar apartado). | Alineación al 100% con la Visión del Producto y alcance declarado (v2.2) |
| 28/09/2026 | RF-015 | Ajuste de verbo a "Consultar reporte..." en RF-015. | Diferenciación entre procesamiento interno y consulta visual de usuario (v2.3) |
| 28/09/2026 | RF-020, RF-023 | Separación de RF-020 (Generar alerta automática) y adición de RF-023 (Consultar reporte de stock bajo). | Cumplimiento del principio de atomicidad de requisitos (v2.4) |
