# Diagramas de arquitectura: Minimarket Control

## Diagrama 1: Contexto C4 (nivel 1)

```mermaid
flowchart TD
    admin(["Dueño / Administrador"])
    cajero(["Cajero / Empleado"])
    sis["Minimarket Control"]
    lector["Lector de códigos de barras"]

    admin -->|Administra y consulta reportes| sis
    cajero -->|Cobra y atiende apartados| sis
    lector -->|Envía el código leído| sis

    style sis fill:#ff5d00,color:#fff
```

## Diagrama 2: Contenedores C4 (nivel 2)

```mermaid
flowchart TD
    admin(["Dueño / Administrador"])
    cajero(["Cajero / Empleado"])

    subgraph sistema["Minimarket Control"]
        web["App web<br/>TypeScript"]
        api["API del sistema<br/>Node.js"]
        db[("Base de datos<br/>PostgreSQL")]
    end

    lector["Lector de códigos de barras"]

    cajero -->|Usa| web
    admin -->|Usa| web
    web -->|HTTPS/JSON| api
    api -->|Lee y escribe| db
    lector -->|Código por USB| web

    style api fill:#ff5d00,color:#fff
```

## Diagrama 3: Componentes C4 de la API (nivel 3, monolito modular)

```mermaid
flowchart TD
    web["App web"]

    subgraph api["API del sistema"]
        aut["Inicio de sesión por rol de empleado<br/>Administrador y cajero, una sesión por caja"]
        pos["Ventas, cliente frecuente y puntos<br/>Cobro y comprobante"]
        inv["Inventario y merma de productos<br/>Existencias, stock mínimo y ajustes"]
        prov["Proveedores y costos de compra<br/>Solo administrador"]
        apart["Pedidos apartados<br/>Reserva y expiración a 7 días"]
        alert["Alerta de stock bajo<br/>Interfaz del Observer"]
        aud["Bitácora de cambios<br/>Registro inmutable"]
    end

    db[("Base de datos")]

    web -->|Inicia sesión| aut

    aut -->|Da acceso con sesión activa| pos
    aut -->|Da acceso con sesión activa| inv
    aut -->|Da acceso con sesión activa| apart
    aut -->|Da acceso solo al administrador| prov
    aut -->|Registra inicio y cierre de sesión| aud

    pos -->|Consulta existencias y descuenta stock| inv
    pos -->|Registra la venta| aud
    apart -->|Reserva y libera stock| inv
    apart -->|Cobra la liquidación| pos
    apart -->|Registra expiraciones| aud
    inv -->|Notifica cambios de stock| alert
    inv -->|Registra mermas y ajustes| aud
    alert -->|Muestra stock crítico| web
    prov -->|Consulta productos| inv

    aut -->|Lee usuarios y sesiones propias| db
    pos -->|Lee y escribe ventas y clientes| db
    inv -->|Lee y escribe productos y mermas| db
    prov -->|Lee y escribe proveedores y costos| db
    apart -->|Lee y escribe apartados| db
    aud -->|Inserta registros inmutables| db

    style inv fill:#ff5d00,color:#fff
```

## Diagrama 4: Clases UML (diseño vigente)

```mermaid
classDiagram
    class Producto {
        -idProducto: int
        -codigoBarras: String
        -nombre: String
        -categoria: String
        -precioVenta: decimal
        -existencias: int
        -stockMinimo: int
        -activo: boolean
        +hayDisponibilidad(unidades: int) boolean
        +descontar(unidades: int) void
        +reponer(unidades: int) void
        +ajustarStock(nuevaCantidad: int, motivo: String) void
        +modificarDatos(nombre: String, categoria: String, precioVenta: decimal, stockMinimo: int) void
        +darDeBaja() void
        +estaEnStockCritico() boolean
    }

    class Proveedor {
        -idProveedor: int
        -nombreComercial: String
        -telefono: String
        -direccion: String
        +asignarCosto(producto: Producto, costoUnitario: decimal) CostoDeCompra
    }

    class CostoDeCompra {
        -idCostoDeCompra: int
        -idProveedor: int
        -idProducto: int
        -costoUnitario: decimal
        -fechaRegistro: Date
        +compararCon(otro: CostoDeCompra) int
    }

    class ClienteFrecuente {
        -idClienteFrecuente: int
        -nombreCompleto: String
        -telefono: String
        -saldoPuntos: int
        +acumularPuntos(importeCobrado: decimal) void
        +canjearPuntos(puntos: int, equivalencia: decimal) decimal
        +consultarSaldo() int
    }

    class Empleado {
        -idEmpleado: int
        -nombreCompleto: String
        -usuario: String
        -contrasena: String
        -rol: Rol
        +iniciarSesion(contrasena: String) boolean
        +cerrarSesion() void
        +esAdministrador() boolean
    }

    class Rol {
        <<enumeration>>
        Administrador
        Cajero
    }

    class Venta {
        -idVenta: int
        -fechaHora: DateTime
        -metodoPago: String
        -montoPagado: decimal
        -descuentoPuntos: decimal
        +agregarProducto(producto: Producto, cantidad: int) DetalleDeVenta
        +calcularTotal() decimal
        +aplicarCanje(descuento: decimal) void
        +validarPago() boolean
        +calcularCambio() decimal
        +confirmar() boolean
        +emitirComprobante() String
    }

    class DetalleDeVenta {
        -idVenta: int
        -idProducto: int
        -cantidad: int
        -precioUnitario: decimal
        +calcularImporte() decimal
    }

    class Apartado {
        -idApartado: int
        -nombreCliente: String
        -telefonoCliente: String
        -estado: EstadoApartado
        -fechaCreacion: Date
        -fechaLimite: Date
        +reservar() void
        +liquidar(telefonoPuntos: String) Venta
        +expirar() void
        +estaVigente() boolean
    }

    class EstadoApartado {
        <<enumeration>>
        Pendiente
        Liquidado
        Expirado
    }

    class DetalleDeApartado {
        -idApartado: int
        -idProducto: int
        -cantidad: int
        +liberar() void
    }

    class Merma {
        -idMerma: int
        -cantidad: int
        -motivo: String
        -fechaHora: DateTime
        +validar() boolean
        +aplicarAlInventario() void
    }

    class RegistroDeAuditoria {
        <<inmutable>>
        -idRegistroDeAuditoria: int
        -fechaHora: DateTime
        -movimiento: String
        -actor: String
        +crear(movimiento: String, actor: String): RegistroDeAuditoria
    }

    Venta "1" *-- "1..*" DetalleDeVenta : contiene
    Apartado "1" *-- "1..*" DetalleDeApartado : reserva

    DetalleDeVenta "*" --> "1" Producto : es de
    DetalleDeApartado "*" --> "1" Producto : reserva

    CostoDeCompra "*" --> "1" Proveedor : ofrecido por
    CostoDeCompra "*" --> "1" Producto : de

    Venta "*" --> "1" Empleado : atendida por
    Venta "*" --> "0..1" ClienteFrecuente : identifica a

    Apartado "0..1" --> "0..1" Venta : se liquida en

    Merma "*" --> "1" Producto : descuenta de
    Merma "*" --> "1" Empleado : registrada por

    RegistroDeAuditoria "*" --> "0..1" Empleado : hecho por

    note for Venta "Total a pagar = suma de (cantidad × precioUnitario) de cada DetalleDeVenta − descuentoPuntos"

    note for DetalleDeVenta "idVenta e idProducto identifican la fila. El precioUnitario se copia de Producto.precioVenta al agregar el producto."

    note for DetalleDeApartado "idApartado e idProducto identifican la fila. Registra la cantidad de unidades reservadas."

    note for CostoDeCompra "idCostoDeCompra identifica cada registro histórico. Los mismos proveedor y producto pueden tener costos registrados en distintas fechas."

    note for RegistroDeAuditoria "Se crea una sola vez y no puede modificarse ni eliminarse. actor es Empleado con ID o Sistema para procesos automáticos."
```

## Diagrama 5: Objetos UML (ejemplo de apartado liquidado)

```mermaid
classDiagram
    class emp1["emp1 : Empleado"] {
        idEmpleado = 2
        nombreCompleto = "Marcos Cruz Ledesma"
        usuario = "mcruz"
        contrasena = "********"
        rol = Cajero
    }

    class cli1["cli1 : ClienteFrecuente"] {
        idClienteFrecuente = 17
        nombreCompleto = "Rosa Hernández Pérez"
        telefono = "4421234567"
        saldoPuntos = 51
    }

    class ap1["ap1 : Apartado"] {
        idApartado = 42
        nombreCliente = "Rosa Hernández Pérez"
        telefonoCliente = "4421234567"
        estado = Liquidado
        fechaCreacion = "2026-11-01"
        fechaLimite = "2026-11-08"
    }

    class da1["da1 : DetalleDeApartado"] {
        idApartado = 42
        idProducto = 8
        cantidad = 2
    }

    class da2["da2 : DetalleDeApartado"] {
        idApartado = 42
        idProducto = 9
        cantidad = 3
    }

    class v1["v1 : Venta"] {
        idVenta = 1315
        fechaHora = "2026-11-04 18:20"
        metodoPago = Efectivo
        montoPagado = 200.00
        descuentoPuntos = 0.00
    }

    class dv1["dv1 : DetalleDeVenta"] {
        idVenta = 1315
        idProducto = 8
        cantidad = 2
        precioUnitario = 42.50
    }

    class dv2["dv2 : DetalleDeVenta"] {
        idVenta = 1315
        idProducto = 9
        cantidad = 3
        precioUnitario = 28.00
    }

    class p1["p1 : Producto"] {
        idProducto = 8
        codigoBarras = "7501000111206"
        nombre = "Aceite vegetal 1 L"
        categoria = Abarrotes
        precioVenta = 42.50
        existencias = 16
        stockMinimo = 6
        activo = true
    }

    class p2["p2 : Producto"] {
        idProducto = 9
        codigoBarras = "7501000222305"
        nombre = "Arroz 1 kg"
        categoria = Abarrotes
        precioVenta = 28.00
        existencias = 12
        stockMinimo = 10
        activo = true
    }

    class ra1["ra1 : RegistroDeAuditoria"] {
        idRegistroDeAuditoria = 9023
        fechaHora = "2026-11-04 18:20"
        movimiento = "Liquidación del apartado 42"
    }

    ap1 --> da1
    ap1 --> da2

    da1 --> p1
    da2 --> p2

    ap1 --> v1

    v1 --> dv1
    v1 --> dv2

    dv1 --> p1
    dv2 --> p2

    v1 --> emp1
    v1 --> cli1

    ra1 --> emp1
```

## Diagrama 6: Secuencia UML del caso de uso principal CU-01 (registrar venta)

```mermaid
sequenceDiagram
    actor Cajero
    participant Web as Aplicación web
    participant Venta
    participant Producto
    participant ClienteFrecuente
    participant RegistroDeAuditoria

    Note over Cajero,Web: Precondición - sesión iniciada y rol autorizado para cobrar
    Cajero->>Web: Escanear producto e indicar cantidad
    Web->>+Venta: agregarProducto(producto, cantidad)
    Venta->>+Producto: hayDisponibilidad(cantidad)
    Producto-->>-Venta: disponibilidad
    Venta-->>-Web: DetalleDeVenta o aviso de stock

    Cajero->>Web: Confirmar cobro
    Web->>+Venta: validarPago()
    Venta-->>-Web: pagoValido
    Web->>+Venta: confirmar()
    Note over Venta,Producto: Revalidación y descuento atómicos antes de confirmar
    Venta->>+Producto: hayDisponibilidad(cantidad)
    Producto-->>-Venta: disponibilidadActual

    alt Existencias suficientes y pago válido
        Venta->>+Producto: descontar(cantidad)
        Producto-->>-Venta: existenciasActualizadas
        opt Cliente frecuente identificado
            Venta->>+ClienteFrecuente: acumularPuntos(importeCobrado)
            ClienteFrecuente-->>-Venta: puntosActualizados
        end
        Venta->>+RegistroDeAuditoria: crear(movimiento, actor)
        RegistroDeAuditoria-->>-Venta: registroInmutable
        Venta-->>Web: ventaConfirmada
        Web->>Venta: emitirComprobante()
        Venta-->>Web: comprobante
        Web-->>Cajero: Mostrar comprobante en pantalla
    else Stock insuficiente o pago inválido
        Venta-->>Web: ventaRechazada
        Web-->>Cajero: Mostrar motivo y permitir corregir
    end
    deactivate Venta
```

## Diagrama 7: Estados UML del pedido Apartado

```mermaid
stateDiagram-v2
    [*] --> Pendiente : registrar pedido / reservar mercancía
    Pendiente --> Liquidado : liquidar [pago confirmado antes del vencimiento] / registrar venta
    Pendiente --> Expirado : inicio del octavo día [sin liquidar] / expirar y liberar stock
    Liquidado --> [*]
    Expirado --> [*]
```
