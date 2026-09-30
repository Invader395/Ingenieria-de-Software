# Visión del producto

---

**Autor:** Julián Guerrero Martínez  
**Fecha de la última versión:** 30 de septiembre de 2026  
**Repositorio:** https://github.com/Invader395/Ingenieria-de-Software.git

---

## 1. Descripción del sistema

**Nombre del sistema:** Minimarket Control

**Descripción:** Programa para la computadora de la tienda que ayuda al dueño a saber en todo momento qué mercancía hay en los estantes, a cuánto se le compró cada producto a cada proveedor y a consultar las ventas diarias de cada empleado. Además, permite anotar a los clientes frecuentes para regalarles puntos por sus compras y por la liquidación de sus apartados, guardar los encargos que hacen por adelantado para que solo pasen a recogerlos y anotar qué empleado atendió cada venta.

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

### Dentro del alcance
- Autenticación y control de acceso por roles (Administrador y Cajero).
- Registro, modificación, baja y ajuste manual de stock de productos organizados por categoría.
- Actualización automática de las existencias al momento de la venta y registro de mermas.
- Alertas automáticas de stock mínimo y reporte de productos con stock igual o inferior al umbral mínimo configurado por producto.
- Catálogo de proveedores con datos de contacto e historial de precios de compra por producto para que el dueño pueda comparar qué proveedor da el mejor precio.
- Registro de ventas realizadas en caja, asociadas automáticamente al empleado en turno.
- Registro de clientes frecuentes por teléfono, con acumulación de puntos en ventas y en la liquidación de apartados, canje de puntos y consulta de saldo; también se permiten ventas y liquidaciones de apartados sin registro ni acumulación de puntos.
- Creación, consulta, liquidación y entrega de pedidos apartados, con reserva inmediata de la mercancía por un plazo máximo de 7 días naturales y cancelación automática al vencer.
- Reporte de ventas diarias por empleado (solo Administrador).
- Bitácora de auditoría de ventas, movimientos de inventario y modificaciones.

---

### Explícitamente fuera del alcance
- Procesamiento de pagos en línea mediante tarjetas de crédito, débito o pasarelas externas.
- Generación y timbrado de facturación electrónica automática.
- Servicio de logística, entrega a domicilio o aplicaciones móviles para clientes.

---

**Por qué queda fuera:** El servicio de entrega a domicilio, la facturación electrónica y el procesamiento de pagos en línea quedan fuera debido a que el problema central del negocio es el control interno de inventario, proveedores y caja. Incluir logística de entregas, facturación electrónica o pasarelas de pago requeriría más tiempo de desarrollo, costos de servidores externos y mantenimiento, por motivos ajenos a lo que se requiere en el negocio.

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
| **Control de acceso** | Se deben restringir las funciones del sistema según el rol del usuario (por ejemplo, separar las opciones del dueño de las del cajero). | Los empleados podrían ver información confidencial como los costos de compra a proveedores o el reporte de ventas por empleado, o alterar registros sin que quede constancia. |

**Reglas de negocio que ya identifiqué:**
1. El precio de compra de los productos no es fijo, es decir, varía según el proveedor que ofrezca la mejor tarifa esa semana, afectando el cálculo del inventario y las compras.
2. En el momento en que un pedido se aparta, se deben reservar las existencias de inmediato para que no se vendan en mostrador, pero el pago y la entrega se realizan posteriormente en la tienda, dentro de un plazo máximo de 7 días naturales; si no se liquida, el apartado se cancela y la mercancía regresa a la venta general.
3. Los clientes frecuentes deben estar registrados en el sistema para poder acumular puntos en sus compras y en la liquidación de sus apartados, y canjearlos en sus compras. El alta puede hacerse en el momento de la venta o de la liquidación.

---

## 5. Ciclo de vida elegido

**Modelo elegido:** Enfoque Ágil (Scrum)

**Por qué le conviene a este proyecto:**  
Le conviene porque organiza el desarrollo en ciclos cortos (iteraciones) donde se entregan versiones funcionales del software desde las primeras semanas. En el minimarket, el riesgo principal es construir un punto de venta lento o complejo que entorpezca la atención al cliente. Al usar un enfoque ágil, podemos construir primero el módulo básico de cobro e inventario, ponerlo a prueba en caja para ajustar la usabilidad según la retroalimentación real del empleado y el dueño, e ir agregando de forma gradual los módulos de apartados, clientes frecuentes y comparación de proveedores sin detener la operación del negocio.

### Alternativas descartadas

**Alternativa 1:** Modelo en Cascada  
**Por qué la descarté:** Porque obliga a definir todo desde el primer día y avanzar en una sola línea estricta sin volver atrás. Si cometemos un error al entender cómo funciona el negocio o si el dueño necesita cambiar algo a mitad del camino, no nos daríamos cuenta hasta el final y corregirlo sería muy costoso.

**Alternativa 2:** Prototipado rápido  
**Por qué la descarté:** Aunque sirve para mostrar pantallas provisionales al cliente de forma rápida, corremos el riesgo de que esas versiones incompletas y desechables se terminen usando como la base real del sistema, dejando de lado el análisis profundo de los riesgos principales del negocio.
