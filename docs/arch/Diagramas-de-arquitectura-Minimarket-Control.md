# Diagramas de arquitectura: Minimarket Control

## Diagrama 1: Contexto

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

## Diagrama 2: Contenedores

```mermaid
flowchart TD
    admin(["Dueño / Administrador"])
    cajero(["Cajero / Empleado"])

    subgraph sistema["Minimarket Control"]
        web["App web<br/>TypeScript"]
        api["API del sistema"]
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

## Diagrama 3: Componentes de la API (monolito modular)

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

    api -->|Cada módulo lee y escribe solo sus tablas| db

    style inv fill:#ff5d00,color:#fff
```

## Diagrama 4: Clases

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
        -costoUnitario: decimal
        -fechaRegistro: Date
        +compararCon(otro: CostoDeCompra) int
    }

    class ClienteFrecuente {
        -idCliente: int
        -nombreCompleto: String
        -telefono: String
        -saldoPuntos: int
        +acumularPuntos(importeCobrado: decimal) void
        +canjearPuntos(puntos: int, equivalencia: decimal) decimal
        +consultarSaldo() int
    }

    class Empleado {
        -idEmpleado: int
        -usuario: String
        -contrasena: String
        -rol: String
        +iniciarSesion(contrasena: String) boolean
        +cerrarSesion() void
        +esAdministrador() boolean
    }

    class Venta {
        -idVenta: int
        -fechaHora: DateTime
        -metodoPago: String
        -montoPagado: decimal
        -descuentoPuntos: decimal
        +agregarLinea(producto: Producto, cantidad: int) LineaDeVenta
        +calcularTotal() decimal
        +aplicarCanje(descuento: decimal) void
        +validarPago() boolean
        +calcularCambio() decimal
        +confirmar() void
        +emitirComprobante() String
    }

    class LineaDeVenta {
        -idLineaDeVenta: int
        -cantidad: int
        -precioUnitario: decimal
        +calcularImporte() decimal
    }

    class Apartado {
        -idApartado: int
        -nombreCliente: String
        -telefonoCliente: String
        -estado: String
        -fechaCreacion: Date
        -fechaLimite: Date
        +reservar() void
        +liquidar(telefonoPuntos: String) Venta
        +expirar() void
        +estaVigente() boolean
    }

    class LineaDeApartado {
        -idLineaDeApartado: int
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
        -idRegistro: int
        -fechaHora: DateTime
        -movimiento: String
    }

    Venta "1" *-- "1..*" LineaDeVenta : contiene
    Apartado "1" *-- "1..*" LineaDeApartado : reserva
    LineaDeVenta "*" --> "1" Producto : es de
    LineaDeApartado "*" --> "1" Producto : reserva
    Venta "*" --> "1" Empleado : atendida por
    Venta "*" --> "0..1" ClienteFrecuente : identifica a
    Apartado "0..1" --> "0..1" Venta : se liquida en
    Merma "*" --> "1" Producto : descuenta de
    Merma "*" --> "1" Empleado : registrada por
    CostoDeCompra "*" --> "1" Proveedor : ofrecido por
    CostoDeCompra "*" --> "1" Producto : de
    RegistroDeAuditoria "*" --> "1" Empleado : hecho por
```

## Diagrama 5: Objetos (liquidación del apartado 42)

```mermaid
classDiagram
    class emp1["emp1 : Empleado"] {
        idEmpleado = 2
        usuario = "mcruz"
        rol = "Cajero"
    }
    class cli1["cli1 : ClienteFrecuente"] {
        idCliente = 17
        nombreCompleto = "Rosa Hernández Pérez"
        telefono = "4421234567"
        saldoPuntos = 51
    }
    class ap1["ap1 : Apartado"] {
        idApartado = 42
        nombreCliente = "Rosa Hernández Pérez"
        telefonoCliente = "4421234567"
        estado = "Liquidado"
        fechaCreacion = 2026-11-01
        fechaLimite = 2026-11-08
    }
    class la1["la1 : LineaDeApartado"] {
        idLineaDeApartado = 101
        cantidad = 2
    }
    class la2["la2 : LineaDeApartado"] {
        idLineaDeApartado = 102
        cantidad = 3
    }
    class v1["v1 : Venta"] {
        idVenta = 1315
        fechaHora = 2026-11-04 18:20
        metodoPago = "Efectivo"
        montoPagado = 200.00
        descuentoPuntos = 0.00
    }
    class lv1["lv1 : LineaDeVenta"] {
        idLineaDeVenta = 4051
        cantidad = 2
        precioUnitario = 42.50
    }
    class lv2["lv2 : LineaDeVenta"] {
        idLineaDeVenta = 4052
        cantidad = 3
        precioUnitario = 28.00
    }
    class p1["p1 : Producto"] {
        idProducto = 8
        codigoBarras = "7501000111206"
        nombre = "Aceite vegetal 1 L"
        precioVenta = 42.50
        existencias = 16
        stockMinimo = 6
    }
    class p2["p2 : Producto"] {
        idProducto = 9
        codigoBarras = "7501000222305"
        nombre = "Arroz 1 kg"
        precioVenta = 28.00
        existencias = 12
        stockMinimo = 10
    }
    class ra1["ra1 : RegistroDeAuditoria"] {
        idRegistro = 9023
        fechaHora = 2026-11-04 18:20
        movimiento = "Liquidación del apartado 42"
    }

    ap1 --> la1
    ap1 --> la2
    la1 --> p1
    la2 --> p1
    ap1 --> v1
    v1 --> lv1
    v1 --> lv2
    lv1 --> p1
    lv2 --> p2
    v1 --> emp1
    v1 --> cli1
    ra1 --> emp1
```
