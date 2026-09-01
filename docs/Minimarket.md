# Visión del producto

---

**Autor:** Julián Guerrero Martínez  
**Fecha de la última versión:** 01 de septiembre de 2026  
**Repositorio:** https://github.com/Invader395/Ingenieria-de-Software.git

---

## 1. Descripción del sistema

**Nombre del sistema:** Minimarket Control

**Descripción:** Programa para la computadora de la tienda que ayuda al dueño a saber en todo momento qué mercancía hay en los estantes, a cuánto se le compró cada producto a cada proveedor y a registrar las ventas diarias totales al final del día. Además, permite anotar a los clientes frecuentes para regalarles puntos por sus compras, guardar los encargos que hacen por adelantado para que solo pasen a recogerlos y anotar qué empleado atendió cada venta.

---

## 2. Problema y usuarios

**El problema:** El negocio pierde dinero y tiempo porque el dueño no sabe exactamente cuánta mercancía tiene guardada ni a cuál proveedor le conviene comprarle cada semana. Además, hay confusión al momento de cobrar los pedidos guardados con anticipación y no hay forma de saber qué tan bien trabaja cada empleado.

**Cómo se resuelve hoy sin el sistema:** El dueño cuenta los productos y posteriormente lo compara con las ventas de cada empleado. Para reabastecer el inventario busca entre las facturas del mes pasado para comparar los precios de los proveedores. Los productos apartados por los clientes se anotan en una hoja de papel que a veces se traspapela, y la puntualidad o ventas de los empleados se juzgan únicamente por lo que recuerda el dueño y la impresión general que tiene de cada uno.

**Usuarios del sistema:**

| Tipo de usuario | Qué necesita del sistema | Qué le preocupa |
|---|---|---|
| **Dueño / Administrador** | Ver la ganancia real, saber cuándo pedir más mercancía, consultar precios de proveedores y revisar el rendimiento de los empleados. | Que los empleados vean los costos a los que compra la mercancía y los proveedores o que el sistema sea tan lento que entorpezca la atención en caja. |
| **Cajero / Empleado** | Marcar ventas de forma rápida, registrar puntos de clientes, consultar e identificar pedidos apartados rápidamente y registrar su turno. | Cometer errores al cobro, tardarse mucho con un cliente en fila o que el sistema sea complicado de usar. |

**Un conflicto entre usuarios:** El dueño necesita que el empleado que realiza la venta capture obligatoriamente el nombre del proveedor, lote del productos y los datos del cliente para mantener un control exhaustivo. Sin embargo, el empleado considera que este exceso de pasos lo vuelven más lento a la hora de procesar el cobro frente a la fila de clientes que esperan en la tienda.

---

## 3. Alcance

### Dentro del alcance
- Registro de productos por categoría.
- Actualización de las existencias disponibles por producto al momento de la venta.
- Catálogo de proveedores con el registro de precios de compra por producto para que el dueño pueda comparar que proveedor da el mejor precio.
- Registro de ventas realizadas en caja por el empleado en turno.
- Opción de registro de clientes frecuentes y acumulación/canje de puntos.
- Opciones para crear, consultar y/o marcar como pagados/entregados los pedidos apartados por clientes.
- Reporte de ventas diarias por empleado y reporte de productos con stock debajo de un umbral especificado.

---

### Explícitamente fuera del alcance
- Pagos en línea mediante tarjetas de crédito/débito o cualquier forma de pago externa.
- Facturación electrónica automática.
- Servicio de entrega a domicilio o aplicación móvil para los clientes.

---

**Por qué queda fuera:** El servicio de entrega a domicilio y la facturación electrónica quedan fuera debido a que el problema central del negocio es el control interno de inventario, proveedores y caja. Incluir la logística de entregas o facturación electrónica requeriría más tiempo de desarrollo, costos de servidores externos y mantenimiento, por motivos ajenos a lo que se requiere en el negocio.

---

## 4. Tipo de sistema y restricciones

**Tipo de sistema:** Sistemas de Información (Software a la medida)

**Por qué es de ese tipo:** Porque es un sistema que registra, consulta y gestiona los datos y procesos de una organización, específicamente el inventario, los precios de proveedores, las ventas en punto de venta y los clientes frecuentes del minimarket.

**Atributos de calidad que impone:**

| Atributo | Por qué importa en mi caso | Qué pasa si no se cumple |
|---|---|---|
| **Usabilidad** | El cajero y los empleados necesitan operar el punto de venta de forma fácil y rápida durante la atención en mostrador. | El cobro se vuelve lento, se generan filas de espera y los empleados pueden optar por no usar el sistema y anotar en papel. |
| **Integridad de los datos** | La información de las existencias, compras a proveedores y puntos acumulados debe ser exacta y constante. | Se vende mercancía inexistente, se registran costos de compra erróneos o se pierden los puntos acumulados por los clientes. |
| **Trazabilidad** | Es necesario saber exactamente qué empleado realizó cada venta y cuándo se modificó el inventario o se registró un apartado. | No se puede evaluar el desempeño de los empleados ni identificar discrepancias o pérdidas de mercancía en caja. |
| **Control de acceso** | Se deben restringir las funciones del sistema según el rol del usuario (por ejemplo, separar las opciones del dueño de las del cajero). | Los empleados podrían ver información confidencial como los costos de compra a proveedores o alterar registros del sistema. |

**Reglas de negocio que ya identifiqué:**
1. El precio de compra de los productos no es fijo, es decir, varía según el proveedor que ofrezca la mejor tarifa esa semana, afectando el cálculo del inventario y las compras.
2. En el momento en que un pedido se aparta, se deben reservar las existencias de inmediato para que no se vendan en mostrador, pero el pago y la entrega se realizan posteriormente en la tienda.
3. Los clientes frecuentes deben estar registrados previamente en el sistema para poder acumular y canjear puntos en sus compras o apartados.

---

## 5. Ciclo de vida elegido

**Modelo elegido:** Modelo en Espiral

**Por qué le conviene a este proyecto:**  
Le conviene porque este modelo avanza mediante vueltas constantes guiadas por una sola pregunta clave: ¿qué es lo que más nos podría hundir? En el minimarket hay varios riesgos que podrían arruinar el proyecto, como que el sistema sea tan lento que haga filas en la caja, que se confundan los apartados de los clientes o que el dueño no tenga tiempo para revisar avances. Al trabajar en espiral, identificamos estos problemas y limitaciones desde el principio antes de gastar tiempo y dinero en construir algo que después sea difícil de modificar o no sirva en la vida real.

### Alternativas descartadas

**Alternativa 1:** Modelo en Cascada  
**Por qué la descarté:** Porque obliga a definir todo desde el primer día y avanzar en una sola línea estricta sin volver atrás. Si cometemos un error al entender cómo funciona el negocio o si el dueño necesita cambiar algo a mitad del camino, no nos daríamos cuenta hasta el final y corregirlo sería muy costoso.

**Alternativa 2:** Prototipado rápido  
**Por qué la descarté:** Aunque sirve para mostrar pantallas provisionales al cliente de forma rápida, corremos el riesgo de que esas versiones incompletas y desechables se terminen usando como la base real del sistema, dejando de lado el análisis profundo de los riesgos principales del negocio.
