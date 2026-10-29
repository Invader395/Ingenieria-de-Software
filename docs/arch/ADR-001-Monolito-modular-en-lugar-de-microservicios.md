# ADR-001: Monolito modular en lugar de microservicios

## Estado
Aceptada, 27 de octubre de 2026

## Contexto
Minimarket Control es un sistema de información instalado en la tienda, hecho a la medida para un solo dueño. La entrega actual es un prototipo en Figma, sin backend ni base de datos funcional.

Lo construye y mantiene una sola persona. La tienda tiene tres cajas y en cada una solo puede haber un empleado a la vez, por lo que el sistema debe soportar hasta tres sesiones abiertas de forma simultánea, una por caja. Todas las cajas trabajan contra el mismo inventario: la especificación contempla que el stock cambie en otra caja antes de cobrar (FA-01.9) y exige validar existencias en tiempo real (RF-010). Como cada venta registra automáticamente al empleado, la fecha y la hora a partir de su sesión activa (RF-001, RF-008 y RNF-CON-001), cada caja mantiene su propia sesión. El servidor es de la propia tienda, no de un proveedor.

El sistema se organiza en cuatro módulos de negocio, además del inicio de sesión de los empleados y auditoría:
- Punto de venta y clientes: Venta y Cliente frecuente (incluye puntos).
- Inventario y mermas: Producto y Merma.
- Proveedores y compras: Proveedor, con acceso solo para el administrador.
- Pedidos apartados: Apartado.

Las fronteras entre módulos están claras porque las reglas del negocio ya están definidas en la entrevista y la especificación. Adicionalmente, ningún requisito pide escalabilidad ni aislamiento entre negocios: RNF-SEG-001 separa roles dentro de una misma tienda, no distintas sucursales.

Se evaluó separar el módulo de pedidos apartados en un servicio aparte, porque es el que tiene una tarea en segundo plano: cancelar automáticamente los apartados expirados (RF-016). También se consideró una arquitectura completa de microservicios.

## Decisión
Construimos un monolito modular: un solo desplegable, con los módulos separados por carpetas e interfaces, y con la regla de que ningún módulo lee las tablas de otro, solo usa su interfaz. Dos casos concretos:
- Venta descuenta stock llamando a la interfaz de Inventario, no escribiendo en sus tablas (RF-009).
- Apartados reserva y libera stock por la misma vía (RF-012 y RF-016).

El aviso de stock bajo se implementa con el patrón Observer: Inventario notifica a las alertas cada vez que cambian las existencias o el stock mínimo de un producto. Los movimientos que notifican son venta (RF-009), reserva de apartado (RF-012), expiración de apartado (RF-016), merma (RF-017), ajuste de stock (RF-024) y cambio del stock mínimo (RF-005). Las alertas comparan el nuevo nivel contra el umbral (RF-020) y retiran la marca de stock crítico cuando el stock lo vuelve a superar.

## Consecuencias
Un solo despliegue, una sola base de datos y un solo registro de errores, que es lo que una sola persona puede operar hoy. Con una sola base de datos, las tres cajas comparten transacciones, lo que simplifica validar el stock en tiempo real antes de cobrar. Evitamos el costo operativo de los microservicios (red entre servicios, despliegues múltiples, monitoreo distribuido), que no se justifica para una tienda.

No podemos escalar módulos por separado. Con una tienda y tres cajas no hace falta, pero si el negocio creciera a varias sucursales o se ofreciera como servicio, habría que revisarlo. En ese caso también habría que rehacer lo que hoy asume un solo negocio: identificador de tienda, parámetros propios (7 días de apartado, equivalencia de puntos, número de cajas), aislamiento de datos, control de acceso entre clientes, respaldos y zonas horarias.

Si algún día hay que separar un módulo, el primero será Pedidos apartados, porque ya tiene límites propios y su tarea en segundo plano es lo que se aísla con menos fricción. Los puntos de cliente frecuente se quedan en Punto de venta: consultar el saldo durante el cobro como llamada de red empeoraría justo el tiempo de la pantalla de caja. La señal para separarlo es que el recibo tarde más de 1 segundo en mostrarse tras confirmar la venta (RNF-USA-001) de forma sostenida, mientras corre la revisión de apartados expirados. Como los límites ya están puestos, el corte sería por donde ya cortamos.

El riesgo real es que, por atajo, un módulo lea las tablas de otro y se pierda el límite. Es especialmente delicado con los costos de compra, que solo el administrador puede ver.
