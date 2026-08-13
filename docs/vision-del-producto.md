# Visión del producto

---

**Autor:** Julián Guerrero Martínez  
**Fecha de la última versión:** 13 de agosto de 2026  
**Repositorio:**  

---

## 1. Descripción del sistema

**Nombre del sistema:** Minimarket Control

**Descripción:** Es un programa para la computadora de la tienda que ayuda al dueño a saber en todo momento qué mercancía hay en los estantes, a cuánto se le compró a cada vendedor y a registrar las ventas diarias. Además, permite guardar los encargos que los clientes hacen por adelantado para que solo pasen a recogerlos y anotar qué empleado atendió cada cobro.

---

## 2. Problema y usuarios

**El problema:** El negocio pierde dinero y tiempo porque no sabe exactamente cuánta mercancía tiene guardada ni a cuál proveedor le conviene comprarle cada semana. Además, hay confusión al momento de cobrar los pedidos guardados con anticipación y no hay forma de saber qué tan bien trabaja cada empleado.

**Cómo se resuelve hoy sin el sistema:** El dueño cuenta los productos al final de cada día. Para reabastecer los productos, el dueño busca entre las antiguas facturas para comparar los precios de los proveedores. Los apartados de los clientes se anotan en notas sueltas de papel que a veces se traspapelan, y la puntualidad o ventas de los empleados se juzgan únicamente por lo que recuerde.

**Usuarios del sistema:**

| Tipo de usuario | Qué necesita del sistema | Qué le preocupa |
|---|---|---|
| **Dueño / Administrador** | Ver la ganancia real, saber cuándo pedir más mercancía, consultar precios de proveedores y revisar el rendimiento de los empleados. | Que los empleados vean los costos a los que compra la mercancía o que el sistema sea tan lento que entorpezca la atención en caja. |
| **Cajero / Empleado** | Marcar ventas de forma rápida, registrar puntos de clientes, consultar e identificar pedidos apartados rápidamente y registrar su turno. | Cometer errores al cobro, tardarse mucho con un cliente en fila o que el sistema sea complicado de usar. |

**Un conflicto entre usuarios:** El dueño necesita que el cajero capture obligatoriamente el nombre del proveedor, lote o datos detallados del cliente al realizar una venta o apartado para mantener un control exhaustivo. Sin embargo, al cajero este exceso de pasos le estorba porque vuelve lento el proceso de cobro frente a una fila de clientes esperando en la tienda.

---

## 3. Alcance

### Dentro del alcance
- Registro y actualización en tiempo real de las existencias de productos por categoría.
- Catálogo de proveedores con registro de precios de compra por producto para comparar quién da el mejor precio.
- Registro de ventas en mostrador asociadas al empleado en turno.
- Módulo de registro de clientes frecuentes y acumulación/canje de puntos.
- Módulo para crear, consultar y marcar como pagados/entregados los pedidos apartados por clientes.
- Reportes simples de ventas diarias por empleado y reporte de productos con stock mínimo.

### Explícitamente fuera del alcance
- Pagos en línea mediante tarjetas de crédito/débito o pasarelas de pago externas.
- Facturación electrónica automática ante el órgano tributario.
- Servicio de entrega a domicilio (delivery) o aplicación móvil para clientes.
- Integración con básculas digitales o lectores de códigos de barras industriales por hardware especializado (se usará entrada manual o lector de código USB básico tipo teclado).

**Por qué queda fuera:** El servicio de entrega a domicilio y la facturación electrónica quedan fuera debido a que el problema central del negocio es el control interno de inventario, proveedores y caja. Incluir logística de entregas o integraciones legales/fiscales complejas requeriría más tiempo de desarrollo, costos de servidores externos y mantenimiento especializado que no justifican el estado actual del negocio.

---

## 4. Tipo de sistema y restricciones

**Tipo de sistema:** De información (Transactional / OLTP)

**Por qué es de ese tipo:** Su función principal es recopilar, almacenar, modificar y recuperar la información diaria de la operación del negocio (compras, ventas, inventarios y clientes) procesando transacciones rápidas y constantes en el punto de venta.

**Atributos de calidad que impone:**

| Atributo | Por qué importa en mi caso | Qué pasa si no se cumple |
|---|---|---|
| **Usabilidad / Velocidad de respuesta** | Las ventas en mostrador deben registrarse en pocos segundos para no generar filas en la tienda. | Los clientes se desesperan por la demora y los empleados optan por no usar el sistema y anotar en papel. |
| **Integridad de datos (Consistencia)** | El inventario de un producto debe descontarse exactamente en el momento en que se confirma la venta o el apartado. | Se pueden vender productos que ya no existen físicamente o prometer apartados sin mercancía disponible. |

**Reglas de negocio que ya identifiqué:**
1. Un mismo producto no tiene un costo de compra fijo; la última compra realizada a un proveedor específico actualiza el precio de referencia de ese proveedor, pero no altera el historial de costos anteriores.
2. Un pedido apartado reserva las unidades del inventario inmediatamente, impidiendo que se vendan en mostrador, pero no genera puntos al cliente sino hasta que el pedido es liquidado y entregado.
3. Si un cliente frecuente no liquida o recoge su pedido apartado en un plazo máximo de 7 días, el pedido se cancela automáticamente y las unidades vuelven a estar disponibles para venta general.

---

## 5. Ciclo de vida elegido

**Modelo elegido:** Modelo Incremental

**Por qué le conviene a este proyecto:** Permite entregar primero un núcleo funcional básico (registro de inventario y caja para ventas) para que el minimarket empiece a operar digitalmente de inmediato. Una vez estabilizado el módulo principal, se pueden añadir los incrementos de apartados, programa de puntos y comparación de proveedores sin detener la operación diaria del negocio. Como el dueño tiene disponibilidad limitada para revisiones, ver entregas funcionales por partes facilita dar retroalimentación sobre la marcha.

### Alternativas descartadas

**Alternativa 1:** Modelo en Cascada (Waterfall)  
**Por qué la descarté:** Requiere definir y congelar todos los requisitos desde el inicio. En un negocio familiar, las dinámicas de venta y reglas de negocio suelen ajustarse sobre la marcha. Si se descubre un fallo o cambio de flujo al final del desarrollo, el costo y tiempo de corrección serían inasumibles.

**Alternativa 2:** Scrum / Agile puro  
**Por qué la descarté:** Scrum requiere una dedicación alta y reuniones constantes (sprints, dailies, plannings) con el Product Owner. En este caso, el dueño del minimarket no dispone del tiempo necesario para involucrarse en ceremonias ágiles semanales con ese nivel de intensidad.

---

## Antes de entregar

Reviso que el documento cumpla lo siguiente:
- [x] La descripción del apartado 1 se entiende sin ser del área
- [x] Hay al menos dos tipos de usuario con necesidades distintas
- [x] Identifiqué un conflicto real entre usuarios
- [x] El alcance dice qué queda fuera, no solo qué queda dentro
- [x] Las exclusiones son específicas, no genéricas
- [x] Identifiqué el tipo de sistema y al menos dos atributos de calidad
- [x] Anoté al menos tres reglas de negocio no obvias
- [x] Justifiqué el ciclo de vida contra dos alternativas descartadas
- [x] El documento está en mi repositorio y se puede leer desde el navegador
- [x] Borré todas las instrucciones en cursiva de la plantilla
