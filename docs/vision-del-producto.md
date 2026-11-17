# Visión del producto

---

**Autor:** Julián Guerrero Martínez  
**Fecha de la última versión:** 30 de septiembre de 2026  
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
| **Cajero / Empleado** | Marcar ventas de forma rápida, registrar puntos de clientes, consultar e identificar pedidos apartados rápidamente e iniciar sesión en su turno para que sus ventas queden asociadas a él. | Cometer errores al cobro, tardarse mucho con un cliente en fila o que el sistema sea complicado de usar. |

**Un conflicto entre usuarios:** El dueño necesita que el empleado que realiza la venta capture obligatoriamente el nombre del proveedor, lote del productos y los datos del cliente para mantener un control exhaustivo. Sin embargo, el empleado considera que este exceso de pasos lo vuelven más lento a la hora de procesar el cobro frente a la fila de clientes que esperan en la tienda.

**Solución adoptada:** El sistema registra automáticamente al empleado, la fecha y la hora de cada venta a partir de su sesión activa, por lo que en caja solo se capturan los productos y, opcionalmente, el teléfono del cliente.

---

## 3. Alcance

### Dentro del alcance del sistema
- **Autenticación y roles:** Inicio y cierre de sesión para Administrador y Cajero; una sesión por caja, con permisos según el rol.
- **Punto de venta:** Registro de ventas con lector de códigos de barras o búsqueda, validación de existencias, cobro en efectivo o registro del pago por tarjeta y comprobante con desglose correcto de productos, cantidades, precios, descuentos y total.
- **Inventario:** Alta, baja lógica y modificación de productos; movimientos de stock, mermas, reabastecimiento y alertas de existencias iguales o inferiores al mínimo.
- **Proveedores:** Registro de proveedores, costos de compra históricos por producto y proveedor, y comparativas de precios, exclusivamente para el Administrador.
- **Clientes frecuentes:** Alta y consulta por teléfono, acumulación, consulta y canje de puntos durante el cobro.
- **Pedidos apartados:** Registro con nombre y teléfono del cliente, reserva de mercancía, liquidación y expiración automática después de siete días naturales, liberando existencias.
- **Reportes y auditoría:** Ventas diarias por empleado, reporte de stock crítico y bitácora inmutable de operaciones relevantes.
- **Implementación prevista:** Aplicación web en TypeScript, API Node.js y base de datos PostgreSQL instaladas para el uso de la tienda, con hasta tres cajas compartiendo inventario.

**Cambio en evaluación (SC-001):** Se propone una consulta específica de apartados pendientes que vencen hoy o mañana. Se documenta como `RF-026` propuesto y no se considera aprobado ni implementado hasta resolver la solicitud.

### Explícitamente fuera del alcance
- Facturación electrónica y timbrado de CFDI.
- Pasarelas externas para autorizar o procesar pagos bancarios; la venta registra el método de pago sin sustituir la terminal bancaria.
- Entregas a domicilio, logística de reparto y aplicación móvil para consumidores.
- Administración de múltiples sucursales o comercialización del producto como plataforma multiempresa.

**Delimitación:** El prototipo interactivo es una herramienta de validación de interfaz; no restringe el alcance funcional del sistema. El diseño contempla backend, persistencia y reglas de negocio verificables, aunque su implementación se complete por iteraciones.

---

## 4. Tipo de sistema y restricciones

**Tipo de sistema:** Sistema de Información (software a la medida, de uso local en el minimarket)

**Por qué es de ese tipo:** Porque registra, consulta y procesa datos operativos de inventario, ventas, clientes frecuentes, proveedores y apartados de un minimarket.

**Atributos de calidad que impone:**

| Atributo | Por qué importa en mi caso | Qué pasa si no se cumple |
|---|---|---|
| **Usabilidad** | El cajero y los empleados necesitan operar el punto de venta de forma fácil y rápida durante la atención en mostrador. | El cobro se vuelve lento, se generan filas de espera y los empleados pueden optar por no usar el sistema y anotar en papel. |
| **Integridad de los datos** | La información de las existencias, compras a proveedores y puntos acumulados debe ser exacta y constante. | Se vende mercancía inexistente, se registran costos de compra erróneos o se pierden los puntos acumulados por los clientes. |
| **Trazabilidad** | Es necesario saber exactamente qué empleado realizó cada venta y cuándo se modificó el inventario o se registró un apartado. | No se puede evaluar el desempeño de los empleados ni identificar discrepancias o pérdidas de mercancía en caja. |
| **Control de acceso** | Se deben restringir las funciones del sistema según el rol del usuario (por ejemplo, separar las opciones del dueño de las del cajero). | Los empleados podrían ver información confidencial como los costos de compra a proveedores o el reporte de ventas por empleado, o alterar registros sin que quede constancia. |

**Reglas de negocio que ya identifiqué:**
1. El precio de compra de los productos no es fijo, varía según el proveedor que ofrezca la mejor tarifa esa semana.
2. La acumulación de puntos requiere que el cliente proporcione su teléfono antes de finalizar la venta o al liquidar un apartado; la identificación es opcional si no desea puntos.
3. El canje de puntos reduce efectivamente el total a cobrar según la equivalencia definida por el Administrador y el saldo del cliente.
4. El comprobante refleja los productos y las cantidades confirmados, sin incluir artículos retirados antes del cobro.
5. Los apartados vencen a las 00:00 del octavo día natural desde su creación y liberan el stock reservado si no fueron liquidados.
6. Los costos de compra, márgenes y reportes de ventas por empleado son exclusivos del Administrador.

---

## 5. Ciclo de vida elegido

**Modelo elegido:** Enfoque Ágil (Scrum)

**Por qué le conviene a este proyecto:**  
Le conviene porque permite construir y validar por iteraciones los flujos críticos de caja, inventario, apartados y proveedores. Cada incremento puede incluir interfaz, reglas de negocio, persistencia y pruebas; los prototipos sirven para revisar la usabilidad antes de desarrollar cada flujo, sin reemplazar su implementación.

### Alternativas descartadas

**Alternativa 1:** Modelo en Cascada  
**Por qué la descarté:** Porque obliga a definir todo desde el primer día y avanzar en una sola línea estricta sin volver atrás. Si cometemos un error al entender cómo funciona el negocio o si el dueño necesita cambiar algo a mitad del camino, no nos daríamos cuenta hasta el final y corregirlo sería muy costoso.

**Alternativa 2:** Prototipado rápido  
**Por qué la descarté:** Aunque sirve para mostrar pantallas provisionales al cliente de forma rápida, corremos el riesgo de que esas versiones incompletas y desechables se terminen usando como la base real del sistema, dejando de lado el análisis profundo de los riesgos principales del negocio.
