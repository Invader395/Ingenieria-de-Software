# Visión del producto

---

**Autor:** Julián Guerrero Martínez  
**Fecha de la última versión:** 30 de septiembre de 2026  
**Repositorio:** https://github.com/Invader395/Ingenieria-de-Software.git

---

## 1. Descripción del sistema

**Nombre del sistema:** Minimarket Control

**Descripción:** Prototipo interactivo en Figma diseñado para la computadora de la tienda que define la experiencia visual y de flujo para el control del inventario y punto de venta del minimarket. Para la entrega de clase, el prototipo demuestra los flujos de acceso por usuario, alertas visuales de inventario y la dinámica de cobro en caja con clientes frecuentes y ventas anónimas.

---

## 2. Problema y usuarios

**El problema:** El negocio pierde dinero y tiempo porque el dueño no sabe exactamente cuánta mercancía tiene guardada ni a cuál proveedor le conviene comprarle cada semana. Además, hay confusión al momento de cobrar los pedidos guardados con anticipación y no hay forma de saber qué tan bien trabaja cada empleado.

**Cómo se resuelve hoy sin el sistema:** El dueño cuenta los productos y posteriormente lo compara con las ventas de cada empleado. Para reabastecer el inventario busca entre las facturas del mes pasado para comparar los precios de los proveedores. Los productos apartados por los clientes se anotan en una hoja de papel que a veces se traspapela, y la puntualidad o ventas de los empleados se juzgan únicamente por lo que recuerda el dueño y la impresión general que tiene de cada uno.

**Usuarios del sistema:**

| Tipo de usuario | Qué necesita del sistema | Qué le preocupa |
|---|---|---|
| **Dueño / Administrador** | Ver la ganancia real, saber cuándo pedir más mercancía, consultar precios de proveedores y revisar el rendimiento de los empleados. | Que los empleados vean los costos a los que compra la mercancía y los proveedores o que el sistema sea tan lento que entorpezca la atención en caja. |
| **Cajero / Empleado** | Marcar ventas de forma rápida, registrar puntos de clientes, consultar e identificar pedidos apartados rápidamente e iniciar sesión en su turno para que sus ventas queden asociadas a él. | Cometer errores al cobro, tardarse mucho con un cliente en fila o que el sistema sea complicado de usar. |

**Un conflicto entre usuarios:** El dueño necesita que el empleado que realiza la venta capture obligatoriamente el nombre del proveedor, lote del productos y los datos del cliente para mantener un control exhaustivo. Sin embargo, el empleado considera que este exceso de pasos lo vuelven más lento a la hora de procesar el cobro frente a la fila de clientes que esperan en la tienda.

**Solución adoptada:** El sistema registra automáticamente al empleado, la fecha y la hora de cada venta a partir de su sesión activa, por lo que en caja solo se capturan los productos y, opcionalmente, el teléfono del cliente.

---

## 3. Alcance

### Dentro del alcance (Prototipo en Figma)
- **Inicio de sesión y autenticación:** Pantalla de login e interfaz/alerta para credenciales inválidas.
- **Gestión de inventario y alertas:**
  - Alerta visual de stock bajo.
  - Alerta visual de producto no encontrado durante la búsqueda o escaneo.
- **Punto de venta y atención a clientes:**
  - Opción de registro de cliente frecuente o realización de venta anónima.
  - Opción visual para el canje de puntos acumulados (se muestra el elemento en interfaz, pero no reflejará dinámicamente la aplicación de descuentos/puntos al proceder con la confirmación).
  - Muestra del comprobante de venta finalizada en pantalla.
  - Comprobante de venta finalizada en pantalla para ventas donde existió modificación por retiro de productos (se mantiene una vista estática/general sin reflejar dinámicamente el desglose de productos retirados).

---

### Explícitamente fuera del alcance
- Implementación de código de producción, lógica de backend o base de datos funcional.
- Reflejo dinámico del canje de puntos al confirmar la transacción en el prototipo.
- Actualización dinámica en el comprobante en pantalla de productos retirados o modificados durante la venta.
- Módulos de compras, catálogo completo de proveedores e historial de precios de compra.
- Creación, seguimiento y liquidación de pedidos apartados.
- Generación y consulta de reportes consolidados por empleado o turno.
- Procesamiento de pagos en línea mediante tarjetas de crédito, débito o pasarelas externas.
- Generación y timbrado de facturación electrónica automática.
- Servicio de logística, entrega a domicilio o aplicaciones móviles para clientes.

---

**Por qué queda fuera:** Para efectos de la entrega de clase, el alcance se delimita a un prototipo de alta fidelidad en Figma para validar la usabilidad y navegación en los flujos críticos de caja e inventario. El procesamiento dinámico de datos, facturación, pagos en línea y servicios adicionales quedan fuera por requerir infraestructura externa de servidores y desarrollo de producción ajeno a la meta de evaluación actual.

---

## 4. Tipo de sistema y restricciones

**Tipo de sistema:** Prototipo de Sistema de Información (Software a la medida)

**Por qué es de ese tipo:** Porque proyecta la interfaz de un sistema para registrar, consultar y gestionar datos operativos de inventario, ventas y clientes frecuentes de un minimarket.

**Atributos de calidad que impone:**

| Atributo | Por qué importa en mi caso | Qué pasa si no se cumple |
|---|---|---|
| **Usabilidad** | El cajero y los empleados necesitan operar el punto de venta de forma fácil y rápida durante la atención en mostrador. | El cobro se vuelve lento, se generan filas de espera y los empleados pueden optar por no usar el sistema y anotar en papel. |
| **Integridad de los datos** | La información de las existencias, compras a proveedores y puntos acumulados debe ser exacta y constante. | Se vende mercancía inexistente, se registran costos de compra erróneos o se pierden los puntos acumulados por los clientes. |
| **Trazabilidad** | Es necesario saber exactamente qué empleado realizó cada venta y cuándo se modificó el inventario o se registró un apartado. | No se puede evaluar el desempeño de los empleados ni identificar discrepancias o pérdidas de mercancía en caja. |
| **Control de acceso** | Se deben restringir las funciones del sistema según el rol del usuario (por ejemplo, separar las opciones del dueño de las del cajero). | Los empleados podrían ver información confidencial como los costos de compra a proveedores o el reporte de ventas por empleado, o alterar registros sin que quede constancia. |

**Reglas de negocio que ya identifiqué:**
1. El precio de compra de los productos no es fijo, varía según el proveedor que ofrezca la mejor tarifa esa semana.
2. En el prototipo se contempla la opción de canje de puntos para clientes registrados, aunque la simulación no calcula dinámicamente el descuento final tras la confirmación.
3. El comprobante en pantalla simula la finalización de la venta, incluso cuando se retiran productos durante la transacción, sin alterar dinámicamente la lista final en el prototipo.

---

## 5. Ciclo de vida elegido

**Modelo elegido:** Enfoque Ágil (Scrum)

**Por qué le conviene a este proyecto:**  
Le conviene porque permite priorizar el diseño de los flujos críticos de usuario (como inicio de sesión, cobro y alertas) en iteraciones cortas. Al prototipar en Figma primero, se puede evaluar la usabilidad con el dueño y la dupla evaluadora antes de proceder con fases avanzadas de desarrollo técnico.

### Alternativas descartadas

**Alternativa 1:** Modelo en Cascada  
**Por qué la descarté:** Porque obliga a definir todo desde el primer día y avanzar en una sola línea estricta sin volver atrás. Si cometemos un error al entender cómo funciona el negocio o si el dueño necesita cambiar algo a mitad del camino, no nos daríamos cuenta hasta el final y corregirlo sería muy costoso.

**Alternativa 2:** Prototipado rápido  
**Por qué la descarté:** Aunque sirve para mostrar pantallas provisionales al cliente de forma rápida, corremos el riesgo de que esas versiones incompletas y desechables se terminen usando como la base real del sistema, dejando de lado el análisis profundo de los riesgos principales del negocio.
