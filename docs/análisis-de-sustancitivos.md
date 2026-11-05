# Análisis de sustantivos: Minimarket Control

## Paso 1. Sustantivos subrayados (sin filtrar)

acceso, administrador, ajuste, alerta, alta, apartado, artículo, asiento, autenticación, baja, base de datos, bitácora, botón, caja, cajero, cambio, cantidad, carrito, catálogo, categoría, cliente, cliente frecuente, cobro, código de barras, comparativa, comprobante, contraseña, costo de compra, credenciales, descuento, desglose, dirección, dueño, empleado, entrega, equivalencia, estado, existencias, fecha, fecha límite, ficha, folio, ganancia, historial, hora, importe, incremento, indicador, interfaz, inventario, ítem, justificación, lista de venta, lote, margen, marca de tiempo, matriz de costos, mensaje, mercancía, merma, método de pago, modal, monto, motivo, movimiento, nombre, nombre comercial, notificación, número telefónico, pago, panel, pantalla, pedido, precio de venta, producto, promedio, prototipo, proveedor, puntos, reabastecimiento, recibo, registro, reporte, reserva, rol, saldo, sesión, sistema, stock, stock crítico, stock mínimo, subtotal, tabla, tarifa, teléfono, token de sesión, total, transacción, turno, umbral, unidad, usuario, venta, venta anónima.

Del alcance y del proceso actual: factura, facturación electrónica, tarjeta, pasarela de pagos, logística, entrega a domicilio, aplicación móvil, libreta, carpeta, hoja de papel, Figma.

## Paso 2. Clases candidatas (10)

Se aplicó el filtro de las cuatro razones: es atributo, es sinónimo de otra, está fuera del sistema o es demasiado vago.

| Clase | Sustantivos de los que sale | Por qué pasó el filtro |
|---|---|---|
| Producto | producto, mercancía, catálogo | Tiene identidad (código único), datos y reglas propias (stock nunca negativo, baja lógica) |
| Proveedor | proveedor, ficha | Tiene nombre comercial único y solo el administrador lo registra o consulta |
| CostoDeCompra | costo de compra, tarifa, matriz de costos | Guarda el costo por proveedor, producto y fecha (RF-007 y RF-014) |
| ClienteFrecuente | cliente frecuente | Tiene teléfono único y un saldo de puntos con reglas de acumulación y canje |
| Empleado | empleado | El sistema guarda sus credenciales y rol (RF-001) y su ID para la bitácora (RNF-CON-001) |
| Venta | venta, cobro, pago, transacción, comprobante | Tiene reglas de cobro: no puede estar vacía y el pago debe cubrir el total |
| LineaDeVenta | artículo, ítem, desglose | Guarda cantidad y precio unitario al momento de vender (RF-008) |
| Apartado | apartado, pedido, reserva | Tiene folio, estados y un plazo de 7 días (RF-011, RF-016 y RF-022) |
| Merma | merma | Exige cantidad válida y motivo obligatorio (RF-017) |
| RegistroDeAuditoria | bitácora, asiento | Es inmutable y guarda fecha, hora e ID del empleado (RNF-CON-001) |

## Paso 3. Qué sabe y qué hace cada clase

| Clase | Sabe | Hace |
|---|---|---|
| Producto | Código de barras único, nombre, categoría, precio de venta, existencias, stock mínimo, estado (activo o inactivo) | Darse de baja, modificarse, descontar o reponer existencias, indicar si está en stock crítico |
| Proveedor | Nombre comercial único, teléfono de contacto, dirección | Registrarse (solo el administrador) y asociarse a productos con un costo |
| CostoDeCompra | Proveedor, producto, costo unitario, fecha de registro | Ordenarse contra otros costos del mismo producto para la comparativa; solo lo ve el administrador |
| ClienteFrecuente | Nombre completo, teléfono único de 10 dígitos, saldo de puntos (inicia en 0) | Acumular 1 punto por cada $10 cobrados, canjear puntos sin pasar del saldo, consultar su saldo |
| Empleado | Identificador, usuario, contraseña, rol (Administrador o Cajero) | Iniciar y cerrar sesión; según su rol, cobrar o consultar reportes |
| Venta | Fecha y hora, empleado que la atendió, cliente frecuente (opcional), sus líneas, total, monto pagado, método de pago | Validar que no esté vacía y que el pago cubra el total, aplicar el canje de puntos, calcular el cambio, confirmarse y emitir comprobante |
| LineaDeVenta | Producto, cantidad, precio unitario al momento de la venta | Calcular su importe |
| Apartado | Folio único, nombre y teléfono del cliente, productos y cantidades reservados, estado (Pendiente de liquidación, Liquidado o Expirado), fecha límite (7 días) | Reservar mercancía, liquidarse (genera una venta cobrada) y expirar a las 00:00 del octavo día devolviendo las unidades al stock |
| Merma | Producto, cantidad (mayor a 0 y sin superar el stock), motivo (daño, caducidad o extravío), fecha, hora, empleado | Restar unidades del inventario y quedar registrada |
| RegistroDeAuditoria | Fecha, hora (precisión de 1 s), ID del empleado, movimiento realizado | Guardarse automáticamente por cada movimiento relevante; nunca se modifica ni se borra |

Casos para comentar en clase: RegistroDeAuditoria, LineaDeVenta y CostoDeCompra hacen muy poco, porque son sobre todo datos. Proveedor está cerca de lo mismo.

## Paso 4. Sustantivos descartados y por qué

| Razón | Descartados | Qué son realmente |
|---|---|---|
| Es un atributo | nombre, nombre comercial, dirección, teléfono, número telefónico | Datos de Proveedor y ClienteFrecuente |
| | Código de barras, categoría, precio de venta, stock mínimo, umbral, existencias, stock, unidad, lote | Datos de Producto |
| | Stock crítico, indicador, alerta, notificación | Estado de Producto (el marcador "Stock crítico") |
| | Puntos, saldo, equivalencia | Datos de ClienteFrecuente (la equivalencia es un parámetro del dueño) |
| | Rol, contraseña, credenciales, sesión, token de sesión | Datos de Empleado |
| | Fecha, hora, marca de tiempo, fecha límite, folio, estado, cantidad, motivo, justificación, concepto, monto, importe, método de pago | Datos de Venta, Apartado, Merma o RegistroDeAuditoria |
| | Subtotal, total, cambio, descuento, promedio, ganancia, margen | Datos calculados |
| Es sinónimo de otra | cobro, pago, transacción, comprobante, recibo, venta anónima | Venta |
| | Artículo, ítem, lista de venta, carrito, desglose | LineaDeVenta |
| | Mercancía | Producto |
| | Pedido, reserva | Apartado |
| | Bitácora, asiento | RegistroDeAuditoria |
| | Tarifa, matriz de costos | CostoDeCompra |
| | Ficha | Proveedor |
| | Cliente | ClienteFrecuente (el cliente anónimo no se guarda) |
| | Turno | Sesión activa del Empleado |
| Está fuera del sistema | dueño, administrador, cajero, usuario | Actores del diagrama de contexto |
| | Factura, facturación electrónica, tarjeta, pasarela de pagos | Documentos y sistemas externos |
| | Logística, entrega a domicilio, aplicación móvil | Servicios que el sistema no gestiona |
| | Libreta, carpeta, hoja de papel, Figma | Papel y herramientas que el sistema reemplaza o usa de fuera |
| Es demasiado vago | alta, baja, ajuste, corrección, incremento, reabastecimiento, acceso, autenticación, entrega | Operaciones de otras clases, sin datos propios |
| | Reporte, comparativa | Vistas derivadas, sin datos propios |
| | Inventario, catálogo, movimiento, historial, registro | Términos generales: son el conjunto de productos o un grupo de eventos |
| | Sistema, prototipo, interfaz, pantalla, panel, botón, modal, tabla, mensaje, base de datos | Partes de la interfaz o de la implementación |
| | Caja | Solo nombra el lugar de cobro, no guarda datos |

## Casos dudosos para discutir

- **Empleado:** El cajero y el administrador son actores externos, pero el sistema sí guarda sus credenciales, rol e ID para la bitácora y el reporte por empleado (RF-001, RF-015 y RNF-CON-001). Por eso Empleado se mantiene como clase y los roles quedan como su atributo.
- **Sesión:** Se trató como atributo de Empleado, porque es su estado dentro del sistema. Si se promueve a clase, serían 11 candidatas. En diseño aparece en el módulo de inicio de sesión.
- **Alerta:** Se descartó como atributo (el estado "Stock crítico" de Producto). Evaluar el stock tras cada movimiento sigue siendo una operación, y en diseño reaparece como el observador del patrón Observer.
- **Caja:** Para convertirla en clase necesitaría su propio requisito.
