┌───────────────────────────────────────────────┐
│                PRESENTACIÓN                   │
│                                               │
│  Aplicación Web                               │
│  Interfaz Cliente                             │
│  Interfaz Vendedor                            │
│  Interfaz Administrador                       │
│  API REST                                     │
└───────────────────────┬───────────────────────┘
                        ↓
┌───────────────────────────────────────────────┐
│              LÓGICA DE NEGOCIO                │
│                                               │
│  Usuarios                                     │
│  Vendedores                                   │
│  Productos                                    │
│  Categorías                                   │
│  Carrito                                      │
│  Pedidos                                      │
│  Inventario / Stock                           │
│  Pagos                                        │
│  Envíos                                       │
└───────────────────────┬───────────────────────┘
                        ↓
┌───────────────────────────────────────────────┐
│                    DATOS                      │
│                                               │
│              Base de datos                    │
└───────────────────────────────────────────────┘


# Arquitectura Inicial del Sistema

## Marketplace E-commerce

La arquitectura del Marketplace E-commerce se organiza inicialmente mediante una arquitectura de tres capas: presentación, lógica de negocio y datos.

## 1. Capa de Presentación

La capa de presentación permite la interacción de los diferentes usuarios con el sistema.

Está conformada por:

- Aplicación web.
- Interfaz para clientes.
- Interfaz para vendedores.
- Interfaz para administradores.
- API REST para la comunicación con la lógica de negocio.

Los clientes podrán buscar productos, gestionar su carrito, realizar pedidos y consultar sus compras.

Los vendedores podrán registrar productos, actualizar información, gestionar stock y consultar sus ventas.

Los administradores podrán gestionar usuarios, vendedores, productos, categorías y pedidos.

## 2. Capa de Lógica de Negocio

La capa de lógica de negocio contiene las reglas y funcionalidades principales del Marketplace.

Los principales módulos son:

- Usuarios.
- Vendedores.
- Productos.
- Categorías.
- Carrito de compra.
- Pedidos.
- Inventario y stock.
- Pagos.
- Envíos.

Esta capa procesa las solicitudes provenientes de la capa de presentación y aplica las reglas correspondientes antes de acceder a los datos.

## 3. Capa de Datos

La capa de datos es responsable del almacenamiento y consulta de la información utilizada por el sistema.

La base de datos almacenará información relacionada con:

- Usuarios.
- Vendedores.
- Productos.
- Categorías.
- Carritos.
- Pedidos.
- Inventario.
- Pagos.
- Direcciones de entrega.

## Sistemas Externos

El Marketplace podrá comunicarse con sistemas externos necesarios para completar algunas operaciones:

- Pasarela de pago.
- Servicio de envío.
- Servicio de facturación.

Estos servicios serán consumidos desde la lógica de negocio mediante interfaces de integración.


## Diagrama de Arquitectura en Capas

```mermaid
flowchart TD

    subgraph PRESENTACION["PRESENTACIÓN"]
        Web["Aplicación Web"]
        API["API REST"]
    end

    subgraph NEGOCIO["LÓGICA DE NEGOCIO"]
        Usuarios["Usuarios"]
        Vendedores["Vendedores"]
        Productos["Productos"]
        Categorias["Categorías"]
        Carrito["Carrito"]
        Pedidos["Pedidos"]
        Inventario["Inventario / Stock"]
        Pagos["Pagos"]
        Envios["Envíos"]
    end

    subgraph DATOS["DATOS"]
        BD["Base de Datos"]
    end

    subgraph EXTERNOS["SISTEMAS EXTERNOS"]
        Pasarela["Pasarela de Pago"]
        ServicioEnvio["Servicio de Envío"]
        Facturacion["Servicio de Facturación"]
    end

    Web --> API
    API --> NEGOCIO
    NEGOCIO --> BD

    Pagos --> Pasarela
    Envios --> ServicioEnvio
    Pedidos --> Facturacion