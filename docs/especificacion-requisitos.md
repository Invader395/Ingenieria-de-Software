# Especificación de requisitos

**Sistema:** Minimarket Control  
**Autor:** Julián Guerrero Martínez  
**Versión:** 2.7  
**Fecha de la última actualización:** 29 de septiembre de 2026  

---

## 1. Propósito y alcance

**Propósito del documento:**  
Este documento define de forma precisa, comprobable y detallada los requisitos funcionales y no funcionales del sistema *Minimarket Control*. Está dirigido al desarrollador del sistema, a la dupla evaluadora y al cliente (dueño del minimarket) para guiar las etapas de diseño, desarrollo de prototipos en Figma, pruebas de software y análisis de impacto de cambios.

**Alcance del sistema:**  
El sistema abarca la gestión interna del punto de venta y control operativo del negocio mediante:
- Autenticación y control de acceso por roles (Administrador y Cajero).
- Registro, actualización, ajuste manual de stock y catálogo de productos organizados por categoría.
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
| **RF-003** | Dar de alta productos | Imprescindible | Confirmado en entrevista (Gestión de catálogo) |
| **RF-004** | Dar de baja productos | Importante | Confirmado en entrevista (Gestión de catálogo) |
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
| **RF-016** | Cancelar automáticamente apartados de mercancía expirados | Importante | Descubrimiento en entrevista (Límite 7 días) |
| **RF-017** | Registrar merma de productos | Importante | Derivado del control de inventario y pérdidas |
| **RF-018** | Registrar alta de cliente frecuente | Imprescindible | Confirmado en entrevista (Prerrequisito de puntos) |
| **RF-019** | Registrar proveedor | Imprescindible | Confirmado en entrevista (Gestión de proveedores) |
| **RF-020** | Generar alerta automática de stock bajo umbral mínimo | Imprescindible | Declarante en la Visión del Producto |
| **RF-021** | Canjear puntos de cliente frecuente | Importante | Declarante en la Visión del Producto |
| **RF-022** | Registrar el pago del pedido apartado | Imprescindible | Confirmado en entrevista (Excepción 1) |
| **RF-023** | Consultar reporte de productos con stock bajo umbral mínimo | Imprescindible | Declarante en la Visión del Producto |
| **RF-024** | Modificar stock de productos | Imprescindible | Confirmado en entrevista (Ajuste/Reabastecimiento de inventario) |

---

### 3.2 Fichas

#### RF-001 · Iniciar sesión en el sistema
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema autentica la identidad del usuario mediante credenciales (usuario y contraseña) para asignar los permisos correspondientes según su rol (Administrador o Cajero). |
| **Origen** | Derivado del control de turnos y seguridad por rol. |
| **Prioridad** | Imprescindible |
| **Criterio de aceptación** | - Al ingresar un usuario y contraseña válidos, el sistema inicia la sesión en la interfaz correspondiente a su rol.<br>- Si las credenciales son incorrectas, el sistema bloquea el acceso.<br>- Al bloquear el acceso por credenciales incorrectas, el sistema despliega el mensaje *"Credenciales inválidas"*. |
| **Relacionado con** | RF-002, RNF-SEG-001 |

---

#### RF-002 · Cerrar sesión en el sistema
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema finaliza la sesión activa del usuario actual y retorna a la pantalla de autenticación. |
| **Origen** | Derivado del control de acceso por turnos. |
| **Prioridad** | Imprescindible |
| **Criterio de aceptación** | - Al presionar el botón *"Cerrar sesión"*, el sistema destruye el token de sesión activo.<br>- El sistema bloquea inmediatamente el acceso a las funciones del punto de venta.<br>- El sistema redirige al usuario y muestra la pantalla de inicio de sesión. |
| **Relacionado con** | RF-001, RNF-CON-001 |

---

#### RF-003 · Dar de alta productos
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema registra un nuevo producto en el catálogo solicitando código de barras, nombre, categoría, precio de venta y stock mínimo. |
| **Origen** | Confirmado en entrevista con el dueño (Gestión de catálogo). |
| **Prioridad** | Imprescindible |
| **Criterio de aceptación** | - Al guardar el producto todos los campos deben estar completos.<br>- El código del producto debe ser único.<br>- Una vez guardado el producto el sistema crea el registro en la base de datos.<br>- Al finalizar el registro la información se despliega en el catálogo general. |
| **Relacionado con** | RF-004, RF-005, RF-006, RF-020, RF-024 |

---

#### RF-004 · Dar de baja productos
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema deshabilita un producto del catálogo para impedir su selección en ventas futuras manteniendo su historial de transacciones. |
| **Origen** | Confirmado en entrevista con el dueño (Gestión de catálogo). |
| **Prioridad** | Importante |
| **Criterio de aceptación** | - Al confirmar la baja de un producto, el sistema cambia su estado a *"Inactivo"*.<br>- El producto deshabilitado se elimina de las búsquedas en la pantalla de caja.<br>- El sistema conserva el registro del producto para reportes históricos. |
| **Relacionado con** | RF-003, RF-008 |

---

#### RF-005 · Modificar datos de productos
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema actualiza los datos informativos de un producto existente (nombre, categoría, precio de venta o stock mínimo). |
| **Origen** | Confirmado en entrevista con el dueño (Gestión de catálogo). |
| **Prioridad** | Importante |
| **Criterio de aceptación** | - Al guardar los cambios sobre un producto seleccionado, el sistema valida la coherencia de los datos ingresados.<br>- El sistema guarda la nueva información en la base de datos.<br>- Los nuevos valores se reflejan inmediatamente en todo el sistema. |
| **Relacionado con** | RF-003, RF-006, RF-020 |

---

#### RF-006 · Consultar productos en stock
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema muestra la cantidad de unidades disponibles en inventario para un producto específico o listado general. |
| **Origen** | Confirmado en entrevista con el dueño (Control de existencias). |
| **Prioridad** | Imprescindible |
| **Criterio de aceptación** | - El sistema permite realizar la búsqueda por código de barras o por nombre del producto.<br>- Al realizar la búsqueda, el sistema despliega el stock actual en tiempo real.<br>- La información mostrada incluye la cantidad disponible para venta directa. |
| **Relacionado con** | RF-003, RF-009, RF-012, RF-020, RF-023, RF-024 |

---

#### RF-007 · Registrar producto con proveedor
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema vincula un producto existente con un proveedor registrado guardando el costo de compra unitario. |
| **Origen** | Confirmado en entrevista con el dueño (Matriz de costos). |
| **Prioridad** | Imprescindible |
| **Criterio de aceptación** | - El usuario debe ingresar el identificador de un proveedor registrado, el producto y el costo de adquisición.<br>- Al guardar la relación, el sistema añade el registro a la matriz histórica de costos del producto.<br>- El costo ingresado queda asociado a la fecha de registro para futuras comparativas. |
| **Relacionado con** | RF-003, RF-014, RF-019, RNF-SEG-001 |

---

#### RF-008 · Registrar venta en caja
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema procesa la transacción comercial asignando el monto pagado, el desglose de artículos, la marca de tiempo y el identificador del cajero en turno. |
| **Origen** | Confirmado en entrevista con el dueño (Proceso actual 3). |
| **Prioridad** | Imprescindible |
| **Criterio de aceptación** | - Al confirmar el cobro, el sistema genera la transacción asociando la fecha y hora exacta.<br>- El registro asigna automáticamente el ID del cajero con la sesión activa.<br>- El sistema emite el recibo digital/impreso con el desglose de artículos y total pagado. |
| **Relacionado con** | RF-009, RF-010, RNF-USA-001, RNF-CON-001 |

---

#### RF-009 · Descontar existencias de inventario por venta
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema resta automáticamente de las existencias generales las unidades comercializadas al finalizar una venta. |
| **Origen** | Confirmado en entrevista con el dueño (Proceso actual 3). |
| **Prioridad** | Imprescindible |
| **Criterio de aceptación** | - Tras confirmarse una venta de $N$ unidades de un producto, el sistema localiza el registro en base de datos.<br>- La cantidad en inventario para dicho ítem disminuye exactamente en $N$ unidades.<br>- El descuento en el stock se ejecuta en tiempo real tras la confirmación del pago. |
| **Relacionado con** | RF-006, RF-008, RF-020, RNF-CON-001 |

---

#### RF-010 · Validar disponibilidad de stock antes de cobro
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema verifica que la cantidad solicitada de un producto no supere las existencias físicas disponibles en la base de datos antes de permitir el cobro. |
| **Origen** | Confirmado en entrevista con el dueño (Regla de negocio). |
| **Prioridad** | Imprescindible |
| **Criterio de aceptación** | - Antes de agregar un ítem o procesar el cobro, el sistema compara la cantidad solicitada contra el stock disponible.<br>- Si la cantidad solicitada supera las unidades disponibles, el sistema bloquea la adición del producto.<br>- Al bloquear la acción, el sistema despliega el mensaje *"Stock insuficiente. Disponibles: X unidades"*. |
| **Relacionado con** | RF-006, RF-008 |

---

#### RF-011 · Registrar pedido apartado
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema crea un pedido diferido vinculando los productos elegidos al nombre completo y número telefónico del cliente. |
| **Origen** | Confirmado en entrevista con el dueño (Excepción 1). |
| **Prioridad** | Imprescindible |
| **Criterio de aceptación** | - Al crear el pedido, el sistema exige ingresar el nombre completo y número telefónico del cliente.<br>- Al guardar la información, el sistema genera un folio único para el apartado.<br>- El sistema asigna automáticamente el estado *"Pendiente de liquidación"*.<br>- La fecha límite de liquidación se fija automáticamente en 7 días naturales a partir de su creación. |
| **Relacionado con** | RF-012, RF-016, RF-022 |

---

#### RF-012 · Reservar mercancía de apartado en inventario
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema descuenta del stock disponible para venta directa las unidades asociadas a un nuevo pedido apartado. |
| **Origen** | Confirmado en entrevista con el dueño (Excepción 1). |
| **Prioridad** | Imprescindible |
| **Criterio de aceptación** | - Al generarse un pedido apartado con $N$ unidades de un producto, el sistema actualiza el inventario.<br>- Las existencias para venta directa en mostrador se reducen exactamente en $N$ unidades.<br>- Las unidades descontadas quedan congeladas en el sistema vinculadas exclusivamente al folio del apartado. |
| **Relacionado con** | RF-006, RF-011, RF-016, RF-020 |

---

#### RF-013 · Acumular puntos para cliente frecuente
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema calcula y suma puntos en el saldo del cliente en función del importe total cobrado al ingresar su número telefónico. |
| **Origen** | Confirmado en entrevista con el dueño (Verificación Puntos). |
| **Prioridad** | Importante |
| **Criterio de aceptación** | - Al ingresar un número telefónico registrado durante el cobro, el sistema abona 1 punto por cada $10 MXN de compra.<br>- Los puntos calculados se suman al saldo acumulado del cliente en la base de datos.<br>- Si el número no existe, el sistema permite procesar la venta como anónima o iniciar el alta del cliente (RF-018) sin detener la transacción. |
| **Relacionado con** | RF-008, RF-018, RF-021, RNF-USA-001 |

---

#### RF-014 · Consultar comparativa de precios de proveedores
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema muestra un listado de los costos históricos de compra de un producto provistos por diferentes proveedores. |
| **Origen** | Confirmado en entrevista con el dueño (Proceso actual 2). |
| **Prioridad** | Imprescindible |
| **Criterio de aceptación** | - Al seleccionar un producto en el módulo de proveedores, el sistema consulta los costos históricos registrados.<br>- El sistema despliega la lista de todos los proveedores asociados a dicho producto.<br>- La lista se presenta ordenada automáticamente del costo unitario más bajo al más alto. |
| **Relacionado con** | RF-007, RF-019, RNF-SEG-001 |

---

#### RF-015 · Consultar reporte diario de ventas por empleado
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema permite al usuario autenticado consultar en pantalla un resumen consolidado de ingresos y ventas desglosadas por cada empleado en una fecha seleccionada. |
| **Origen** | Confirmado en entrevista con el dueño (Proceso actual 3). |
| **Prioridad** | Imprescindible |
| **Criterio de aceptación** | - Al seleccionar una fecha en el panel de reportes y presionar "Consultar", el sistema recupera las transacciones del día.<br>- El sistema despliega en pantalla la lista de cajeros con el total acumulado en dinero ($MXN$).<br>- La vista incluye el número total de ventas realizadas por cada empleado.<br>- El sistema calcula y muestra el promedio de venta por transacción para cada cajero. |
| **Relacionado con** | RF-008, RNF-CON-001 |

---

#### RF-016 · Cancelar automáticamente apartados de mercancía expirados
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema cancela los apartados no liquidados tras 7 días naturales y reintegra las unidades reservadas al stock disponible. |
| **Origen** | Descubrimiento en entrevista con el dueño (Excepción 1). |
| **Prioridad** | Importante |
| **Criterio de aceptación** | - A las 00:00 horas del octavo día natural tras la creación de un apartado no pagado, el sistema identifica la expiración.<br>- El sistema cambia automáticamente el estado del pedido apartado a *"Expirado"*.<br>- Las unidades que estaban congeladas se reintegran e incrementan el stock disponible para venta directa. |
| **Relacionado con** | RF-011, RF-012, RNF-CON-001 |

---

#### RF-017 · Registrar merma de productos
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema reduce el inventario de un producto por concepto de daño, caducidad o extravío, registrando la justificación. |
| **Origen** | Derivado del control de inventario y pérdidas en tienda. |
| **Prioridad** | Importante |
| **Criterio de aceptación** | - El usuario debe ingresar la cantidad de unidades defectuosas y el motivo de la merma.<br>- Al guardar el registro, el sistema resta las unidades indicadas del inventario total.<br>- El sistema genera un asiento inmutable en la bitácora de mermas asociando la fecha, hora y usuario. |
| **Relacionado con** | RF-006, RF-020, RNF-CON-001 |

---

#### RF-018 · Registrar alta de cliente frecuente
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema registra un nuevo cliente frecuente solicitando su nombre completo y número telefónico de 10 dígitos para habilitar la acumulación y consulta de puntos. |
| **Origen** | Confirmado en entrevista con el dueño (Prerrequisito para acumulación de puntos). |
| **Prioridad** | Imprescindible |
| **Criterio de aceptación** | - Al ingresar un nombre completo y un teléfono válido de 10 dígitos no duplicado, el sistema crea el nuevo registro.<br>- El cliente recién registrado inicia automáticamente con un saldo inicial de 0 puntos.<br>- Si el número telefónico ya existe en el sistema, este bloquea el registro.<br>- Al bloquear el registro por duplicidad, el sistema despliega el mensaje *"El número telefónico ya se encuentra registrado"*. |
| **Relacionado con** | RF-013, RF-021, RNF-USA-001 |

---

#### RF-019 · Registrar proveedor
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema registra un nuevo proveedor ingresando su nombre comercial, teléfono de contacto y dirección. |
| **Origen** | Confirmado en entrevista con el dueño (Gestión de proveedores). |
| **Prioridad** | Imprescindible |
| **Criterio de aceptación** | - El usuario debe ingresar el nombre comercial, teléfono de contacto y dirección del proveedor.<br>- Al guardar con un nombre comercial no registrado previamente, el sistema genera la ficha del proveedor en la base de datos.<br>- Una vez creado, el proveedor queda disponible en el sistema para asignarle productos y precios de compra. |
| **Relacionado con** | RF-007, RF-014, RNF-SEG-001 |

---

#### RF-020 · Generar alerta automática de stock bajo umbral mínimo
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema evalúa en tiempo real las existencias tras cada movimiento de inventario (venta, reserva, merma o ajuste/reabastecimiento) y genera un indicador o notificación de alerta cuando la cantidad disponible es igual o inferior al stock mínimo configurado. |
| **Origen** | Declarante explícito en la Visión del Producto (Sección Alcance) y reglas de inventario. |
| **Prioridad** | Imprescindible |
| **Criterio de aceptación** | - Tras cada movimiento que modifique el stock (RF-009, RF-012, RF-017, RF-024), el sistema compara el nuevo nivel con el stock mínimo del ítem.<br>- Si el stock disponible es igual o menor al umbral mínimo, el sistema asigna automáticamente la marca de *"Stock crítico"* al producto.<br>- Si una modificación incrementa el stock por encima del umbral mínimo, el sistema remueve la alerta visual automáticamente. |
| **Relacionado con** | RF-003, RF-006, RF-009, RF-012, RF-017, RF-023, RF-024 |

---

#### RF-021 · Canjear puntos de cliente frecuente
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema descuenta un monto determinado de puntos acumulados por un cliente para aplicarlo como descuento equivalente en dinero durante el cobro. |
| **Origen** | Declarante explícito en la Visión del Producto (Sección Alcance y Reglas de negocio). |
| **Prioridad** | Importante |
| **Criterio de aceptación** | - Al seleccionar "Canjear puntos" e ingresar una cantidad válida ($\le$ saldo actual), el sistema valida la transacción.<br>- El sistema calcula el descuento monetario equivalente según la regla de conversión.<br>- El sistema resta la cantidad de puntos utilizados del saldo del cliente.<br>- El importe descontado se reduce directamente del total a pagar en la caja. |
| **Relacionado con** | RF-008, RF-013, RF-018, RNF-USA-001 |

---

#### RF-022 · Liquidar pedido apartado
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema registra el pago del saldo pendiente de un pedido apartado, cambiando su estado a "Liquidado/Entregado". |
| **Origen** | Confirmado en entrevista con el dueño (Excepción 1). |
| **Prioridad** | Imprescindible |
| **Criterio de aceptación** | - El sistema permite buscar el apartado vigente mediante su folio o número telefónico del cliente.<br>- Al confirmar el cobro de la cantidad adeudada, el sistema cambia el estado del pedido a *"Liquidado"*.<br>- El sistema registra la venta cobrada asignándola al cajero en turno.<br>- El sistema libera el registro de reserva de la mercancía. |
| **Relacionado con** | RF-008, RF-011, RF-012, RNF-CON-001 |

---

#### RF-023 · Consultar reporte de productos con stock bajo umbral mínimo
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema permite al usuario autenticado consultar en pantalla un listado con todos los productos cuya existencia disponible sea menor o igual a su umbral mínimo, indicando la cantidad faltante para reabastecer. |
| **Origen** | Declarante explícito en la Visión del Producto (Sección Alcance). |
| **Prioridad** | Imprescindible |
| **Criterio de aceptación** | - Al presionar "Ver productos con stock bajo" en el panel de reportes, el sistema filtra los productos con stock $\le$ stock mínimo.<br>- El sistema despliega una tabla con el código de producto, nombre y stock actual.<br>- La vista muestra el stock mínimo configurado para cada producto.<br>- El sistema calcula y despliega las unidades faltantes sugeridas para reabastecer cada ítem. |
| **Relacionado con** | RF-006, RF-020 |

---

#### RF-024 · Modificar stock de productos
| Campo | Contenido |
| :--- | :--- |
| **Descripción** | El sistema permite ajustar o actualizar manualmente la cantidad de unidades disponibles en el inventario de un producto (por reabastecimiento o corrección de inventario físico) ingresando la nueva cantidad o el incremento/decremento directo y el motivo de la modificación. |
| **Origen** | Confirmado en entrevista con el dueño (Ajuste/Reabastecimiento de inventario). |
| **Prioridad** | Imprescindible |
| **Criterio de aceptación** | - Al ingresar la nueva cantidad de stock para un producto válido y guardar, el sistema actualiza el inventario en la base de datos.<br>- El sistema genera un registro automático de la modificación en la bitácora de auditoría.<br>- El sistema evalúa y recalcula inmediatamente las alertas de stock mínimo (RF-020). |
| **Relacionado con** | RF-003, RF-006, RF-020, RNF-CON-001 |

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
| **Métrica** | - Un máximo de 4 clics o confirmaciones de teclado desde que se agrega el último producto al carrito hasta la finalización del cobro y despliegue del recibo en pantalla. |
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
| **Métrica** | - 0% de accesos permitidos a vistas de proveedores o costos desde sesiones con rol de Cajero/Empleado. |
| **Origen** | Confirmado en entrevista con el dueño (Regla estricta no revelada inicialmente). |
| **Prioridad** | Imprescindible |
| **Por qué importa** | El dueño exige confidencialidad absoluta sobre sus márgenes de ganancia y los costos pactados con proveedores para evitar filtraciones o conflictos operativos. |
| **Afecta a** | RF-001, RF-007, RF-014, RF-019 |

---

#### RNF-CON-001 · Registro inmutable de auditoría por transacción
| Campo | Contenido |
| :--- | :--- |
| **Atributo de calidad** | Confiabilidad (Trazabilidad e Integridad de datos) |
| **Descripción** | Todo movimiento de inventario, registro de apartado, ajuste manual de stock o venta almacena automáticamente fecha, hora exacta y el identificador del empleado en turno, impidiendo la modificación posterior del registro. |
| **Métrica** | - 100% de las transacciones guardan marca de tiempo ($1\text{ segundo}$ de precisión) e ID de empleado.<br>- Se bloquean los comandos de alteración (UPDATE/DELETE) en la tabla de bitácora. |
| **Origen** | Derivado del tipo de sistema (Sistemas de Información). |
| **Prioridad** | Imprescindible |
| **Por qué importa** | Elimina la incertidumbre sobre descuadres de caja y discrepancias de mercancía, permitiendo auditorías objetivas sin depender de memorias. |
| **Afecta a** | RF-002, RF-008, RF-009, RF-011, RF-015, RF-016, RF-017, RF-022, RF-024 |

---

## 5. Casos de uso

### 5.1 Relación de Casos de Uso del Sistema

1. **CU-01:** Registrar venta en caja (Caso de uso principal detallado)
2. **CU-02:** Registrar pedido apartado
3. **CU-03:** Liquidar pedido apartado
4. **CU-04:** Dar de alta a cliente frecuente
5. **CU-05:** Acumular puntos de cliente frecuente
6. **CU-06:** Canjear puntos de cliente frecuente
7. **CU-07:** Alta de proveedores
8. **CU-08:** Consultar comparativa de precios de proveedores
9. **CU-09:** Consultar reporte de ventas por empleado
10. **CU-10:** Notificar producto con stock por debajo del umbral especificado
11. **CU-11:** Consultar detalle de productos con stock bajo
12. **CU-12:** Registrar merma de productos
13. **CU-13:** Modificar stock de productos
14. **CU-14:** Cancelar apartado de productos
15. **CU-15:** Descontar stock de producto
16. **CU-16:** Mostrar comprobante en pantalla

---

### 5.2 Detalle de los Casos de Uso del Sistema

#### CU-01 Registrar venta en caja

- **Identificador:** CU-01
- **Título:** Registrar venta en caja
- **Actor principal:** Cajero / Empleado
- **Objetivo:** Registrar la venta de productos a un cliente
- **Precondición:** El cajero ha iniciado sesión en el sistema (RF-001) y se encuentra en la pantalla de Punto de Venta.

##### Escenario Principal (Flujo Feliz):
1. El cajero escanea el código de barras del producto presentado por el cliente.
2. El sistema valida la disponibilidad de stock (RF-010).
3. Una vez validada la disponibilidad el sistema añade el ítem a la lista de venta.
4. Una vez que se añade el item se actualiza el subtotal.
5. El cajero repite el paso 1 para cada producto restante.
6. El cajero presiona el botón "Finalizar Venta".
7. El sistema solicita opcionalmente el número telefónico del cliente para acumular/canjear puntos.
8. Si se registra un número telefónico se continua con el caso de uso **CU-05 Acumular puntos de cliente frecuente**.
9. El cajero selecciona el método de pago en efectivo e ingresa el monto recibido.
10. El cajero confirma la transacción presionando "Cobrar".
11. El sistema continua con el caso de uso **CU-14 Descontar stock de producto**.
12. El sistema evalúa si el nivel de stock activa la alerta automática.
13. El sistema registra la transacción en la bitácora con la fecha, hora e ID de empleado (RNF-CON-001).
14. El sistema calcula el cambio y continua con el caso de uso  **CU-15 Mostrar comprobante en pantalla**..

##### Flujos Alternos:
- **Flujo Alterno 1.1 (Producto sin código o ilegible):**
  1. Si el producto no tiene código visible o no se puede leer.
  2. El cliente presenta otro producto con código legible.
  3. El cajero escanea el nuevo producto recibido y el flujo continúa en el paso 2 del Escenario Principal.

- **Flujo Alterno 1.2.1 (No hay otro producto con código legible):**
  1. Si el cliente no presenta otro producto con código legible.
  3. El cajero escanea el siguiente producto recibido y el flujo continúa en el paso 2 del Escenario Principal.

- **Flujo Alterno 2.1 (Stock insuficiente - RF-010):**
  1. Si el sistema detecta que la cantidad solicitada supera el stock disponible en base de datos.
  2. El sistema bloquea el agregado del ítem a la lista de venta.
  3. El sistema muestra un mensaje de alerta: *"Stock insuficiente. Disponibles: X unidades"*.
  4. El cajero ajusta la cantidad al stock disponible.
  5. El flujo regresa al paso 5 del Escenario Principal.

- **Flujo Alterno 2.4.1 (No hay stock disponible):**
  1. Si no hay stock disponible.
  2. El sistema retira el producto de la venta.
  3. El flujo regresa al paso 5 del Escenario Principal.

- **Flujo Alterno 12.1:**
  1. Si el stock esta por debajo del umbral especificado.
  2. Se continua con el caso de uso **CU-10: Notificar producto con stock por debajo del umbral especificado**

- **Postcondición:** El inventario de los productos vendidos se actualiza en tiempo real, se actualizan las alertas de stock si corresponde, se genera el registro inmutable en la bitácora y la pantalla queda limpia para la siguiente venta.
- **Requisitos que realiza:** RF-008, RF-009, RF-010, RF-020, RNF-USA-001, RNF-CON-001.

---

## 6. Trazabilidad

| Requisito | Origen | Caso de uso | Elemento del prototipo | Estado |
| :--- | :--- | :--- | :--- | :--- |
| **RF-001** | Derivado de seguridad | CU-01, CU-02, CU-03, CU-05, CU-06, CU-07, CU-08, CU-10 | Pantalla Login / Autenticación | Vigente |
| **RF-002** | Derivado de seguridad | CU-01 Registrar venta en caja | Botón / Menú Cerrar Sesión | Vigente |
| **RF-003** | Entrevista 22 sep | CU-10 Modificaciones de stock | Modal Alta de Producto | Vigente |
| **RF-004** | Entrevista 22 sep | CU-10 Modificaciones de stock | Tabla Catálogo / Acciones | Vigente |
| **RF-005** | Entrevista 22 sep | CU-10 Modificaciones de stock | Modal Editar Producto | Vigente |
| **RF-006** | Entrevista 22 sep | CU-01, CU-08, CU-09, CU-10 | Buscador de Inventario / Alerta Stock | Vigente |
| **RF-007** | Entrevista 22 sep | CU-06 Consultar comparativa | Matriz Proveedor-Producto | Vigente |
| **RF-008** | Entrevista 22 sep | CU-01, CU-03 | Pantalla Punto de Venta / Cobro | Vigente |
| **RF-009** | Entrevista 22 sep | CU-01 Registrar venta en caja | Contador de Inventario en BD | Vigente |
| **RF-010** | Entrevista 22 sep | CU-01, CU-02 | Alerta Modal de Stock Insuficiente | Vigente |
| **RF-011** | Entrevista 22 sep | CU-02 Registrar pedido apartado | Pantalla Módulo de Apartados | Vigente |
| **RF-012** | Entrevista 22 sep | CU-02 Registrar pedido apartado | Indicador Stock Reservado | Vigente |
| **RF-013** | Entrevista 22 sep | CU-04 Acumulación/Canje | Modal Cliente Frecuente en Caja | Vigente |
| **RF-014** | Entrevista 22 sep | CU-06 Consultar comparativa | Pantalla Comparativa de Precios | Vigente |
| **RF-015** | Entrevista 22 sep | CU-07 Consultar reportes ventas | Dashboard de Reportes / Ventas | Vigente |
| **RF-016** | Entrevista 22 sep | CU-11 Cancelaciones apartados | Tabla de Apartados Expirados | Vigente |
| **RF-017** | Derivado de inventario | CU-09 Gestionar mermas | Formulario de Registro de Merma | Vigente |
| **RF-018** | Entrevista 22 sep | CU-03 Alta cliente frecuente | Modal Alta Rápida de Cliente | Vigente |
| **RF-019** | Visión del producto | CU-05 Alta de proveedores | Modal Registrar Proveedor | Vigente |
| **RF-020** | Visión del producto | CU-01, CU-08, CU-09, CU-10 | Badge / Indicador de Alerta de Stock Crítico | Vigente |
| **RF-021** | Visión del producto | CU-04 Acumulación/Canje | Opción Canje de Puntos en Caja | Vigente |
| **RF-022** | Entrevista 22 sep | CU-03 Liquidar pedido apartado | Modal Cobro / Liquidar Apartado | Vigente |
| **RF-023** | Visión del producto | CU-08 Consultar stock bajo | Reporte de Reabastecimiento / Umbral | Vigente |
| **RF-024** | Entrevista 28 sep | CU-10 Modificaciones de stock | Formulario / Modal Modificar Stock | Vigente |
| **RNF-USA-001** | Entrevista 22 sep | CU-01, CU-03, CU-04 | Flujo de Cobro de 4 pasos | Vigente |
| **RNF-SEG-001** | Entrevista 22 sep | CU-05, CU-06 | Control de Acceso y Login de Dueño | Vigente |
| **RNF-CON-001** | Tipo de Sistema | Todos los casos de uso | Módulo de Bitácora / Auditoría | Vigente |

---

## 7. Registro de cambios

| Fecha | Requisito | Qué cambió | Por qué |
| :--- | :--- | :--- | :--- |
| 22/09/2026 | Todos | Creación inicial de especificación (v1.0). | Borrador intersemestral |
| 24/09/2026 | RF-011 | Se añadieron nombre y teléfono como datos obligatorios | Ajuste tras revisión con el cliente |
| 28/09/2026 | RF-024 | Incorporación del requisito funcional "Modificar stock de productos" (v2.5) | Necesidad de reabastecimiento directo y ajuste manual de inventario |
| 28/09/2026 | Casos de Uso | Reestructuración completa de los Casos de Uso CU-01 al CU-06 (v2.6) | Adaptación a la estructura paso a paso simplificada |
| 29/09/2026 | Casos de Uso | Desglose atómico individualizado de Casos de Uso (CU-01 al CU-11) (v2.7) | Ajuste de estructura a solicitudes específicas de casos de uso separados |
