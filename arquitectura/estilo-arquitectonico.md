# Estilo Arquitectónico

## Marketplace E-commerce

### Estilo seleccionado: monolito modular en capas

El backend del Marketplace se organiza como un **monolito modular**: usuarios, vendedores, catálogo, carrito y pedidos son módulos funcionales independientes dentro de una sola aplicación Node.js con Express. Cada módulo separa las responsabilidades en rutas y controladores, servicios de negocio y repositorios. Los módulos se despliegan juntos y comparten el acceso a una base de datos PostgreSQL.

El frontend web consume el backend mediante una API REST sobre HTTPS. El backend también se integra con una pasarela de pagos y un servicio de envíos mediante sus API. Esta propuesta mantiene una estructura sencilla para desplegar y operar el sistema, mientras que la separación por módulos facilita localizar cambios y evolucionar las funcionalidades.

La decisión responde especialmente a los drivers de API REST (DA05), seguridad y acceso por roles (DA03 y DA07), e integración con servicios externos (DA04 y DA06). La separación modular y por capas también respalda la mantenibilidad (DA08). El monolito es una decisión inicial: una carga creciente puede requerir escalar instancias y, si los límites funcionales lo justifican, extraer módulos en el futuro.

## Diagrama de arquitectura

```mermaid
flowchart TB
	subgraph ACTORES[Actores]
		direction LR
		Cliente[Cliente]
		Vendedor[Vendedor]
		Administrador[Administrador]
	end

	subgraph FRONTEND[Cliente web]
		Web["Navegador<br/>HTML / CSS / JavaScript"]
	end

	subgraph BACKEND["Monolito modular · Node.js 20 LTS · Express"]
		direction TB

		Middleware["Middlewares transversales<br/>CORS · express.json() · autenticación JWT<br/>validación de entrada · manejo de errores · logger"]

		subgraph PRESENTACION["1. Presentación · rutas y controladores"]
			direction LR
			RUsuarios["usuarios.routes.js<br/>usuarios.controller.js"]
			RVendedores["vendedores.routes.js<br/>vendedores.controller.js"]
			RCatalogo["catalogo.routes.js<br/>catalogo.controller.js"]
			RCarrito["carrito.routes.js<br/>carrito.controller.js"]
			RPedidos["pedidos.routes.js<br/>pedidos.controller.js"]
		end

		subgraph NEGOCIO["2. Lógica de negocio · servicios"]
			direction LR
			SUsuarios["usuarios.service.js<br/>registro · login · roles"]
			SVendedores["vendedores.service.js<br/>alta de tiendas · validación"]
			SCatalogo["catalogo.service.js<br/>productos · categorías · stock"]
			SCarrito["carrito.service.js<br/>ítems · totales"]
			SPedidos["pedidos.service.js<br/>checkout · estados · pago · envío"]
		end

		subgraph DATOS["3. Datos · repositorios"]
			direction LR
			DUsuarios["usuarios.repository.js"]
			DVendedores["vendedores.repository.js"]
			DCatalogo["catalogo.repository.js"]
			DCarrito["carrito.repository.js"]
			DPedidos["pedidos.repository.js"]
		end

		ORM["Acceso a datos compartido<br/>Sequelize · modelos · pool de conexiones"]

		Middleware --> RUsuarios
		Middleware --> RVendedores
		Middleware --> RCatalogo
		Middleware --> RCarrito
		Middleware --> RPedidos

		RUsuarios --> SUsuarios
		RVendedores --> SVendedores
		RCatalogo --> SCatalogo
		RCarrito --> SCarrito
		RPedidos --> SPedidos

		SUsuarios --> DUsuarios
		SVendedores --> DVendedores
		SCatalogo --> DCatalogo
		SCarrito --> DCarrito
		SPedidos --> DPedidos

		DUsuarios --> ORM
		DVendedores --> ORM
		DCatalogo --> ORM
		DCarrito --> ORM
		DPedidos --> ORM

		SPedidos -. "consulta catálogo y stock" .-> SCatalogo
		SPedidos -. "obtiene ítems y totales" .-> SCarrito
	end

	subgraph EXTERNOS[Sistemas externos]
		direction LR
		Pagos["Pasarela de pagos<br/>API del proveedor"]
		Envios["Servicio de envíos<br/>API del proveedor"]
	end

	BaseDatos[("PostgreSQL<br/>marketplace_db")]

	Cliente --> Web
	Vendedor --> Web
	Administrador --> Web
	Web -->|"HTTPS · JSON · /api/v1"| Middleware
	ORM -->|"SQL · TCP 5432"| BaseDatos
	SPedidos -->|"HTTPS / REST"| Pagos
	SPedidos -->|"HTTPS / REST"| Envios

	classDef actor fill:#fff,stroke:#64748b,color:#172033
	classDef frontend fill:#e8f1ff,stroke:#3b6fb6,color:#172033
	classDef middleware fill:#e8e9fb,stroke:#6566a8,color:#172033
	classDef presentation fill:#e6efff,stroke:#4d78b8,color:#172033
	classDef business fill:#e7f2e4,stroke:#56814c,color:#172033
	classDef data fill:#fff0d8,stroke:#b88735,color:#172033
	classDef shared fill:#f9e8c7,stroke:#b88735,color:#172033
	classDef external fill:#f2f2f2,stroke:#777,color:#172033
	classDef database fill:#e5f2ef,stroke:#408579,color:#172033

	class Cliente,Vendedor,Administrador actor
	class Web frontend
	class Middleware middleware
	class RUsuarios,RVendedores,RCatalogo,RCarrito,RPedidos presentation
	class SUsuarios,SVendedores,SCatalogo,SCarrito,SPedidos business
	class DUsuarios,DVendedores,DCatalogo,DCarrito,DPedidos data
	class ORM shared
	class Pagos,Envios external
	class BaseDatos database
```

Las flechas continuas representan solicitudes y dependencias principales; las flechas punteadas representan llamadas entre servicios de módulos durante un flujo de negocio. Los actores utilizan la misma aplicación web, y el middleware transversal procesa las solicitudes antes de que alcancen las rutas de cada módulo.

## Responsabilidades y reglas

- **Presentación:** expone los endpoints REST y traduce las solicitudes HTTP a operaciones del módulo. Los controladores delegan el trabajo en los servicios; no contienen reglas de negocio ni consultas directas a la base de datos.
- **Lógica de negocio:** implementa las reglas de cada módulo. Los servicios coordinan operaciones entre módulos mediante sus servicios, sin acceder directamente a los controladores o repositorios de otro módulo.
- **Datos:** los repositorios encapsulan las consultas y el acceso a modelos mediante Sequelize. Todos los módulos usan la misma base de datos, pero cada repositorio es responsable de los datos de su módulo.
- **Seguridad transversal:** autenticación JWT, autorización por rol y validación de entrada se aplican en el backend antes de ejecutar las operaciones protegidas.
- **Integraciones:** los servicios de pedidos coordinan el pago y el envío a través de API externas; los detalles del proveedor no deben filtrarse a controladores ni repositorios.
- **Despliegue:** los módulos forman una sola aplicación y se ejecutan en un único proceso por instancia. La comunicación con PostgreSQL y los proveedores externos ocurre a través de sus interfaces definidas.

## Consecuencias de la decisión

La estructura en capas hace explícito el flujo desde la API hasta la persistencia, y los límites de módulo reducen el acoplamiento durante el desarrollo y las pruebas. Al compartir proceso y base de datos, el sistema es más sencillo de desplegar que una arquitectura de microservicios y evita llamadas de red entre módulos internos.

Como contrapartida, los módulos se escalan y despliegan juntos, y comparten los recursos del proceso y de la base de datos. Las dependencias entre servicios deben mantenerse acotadas para que el monolito pueda evolucionar sin convertirse en un bloque difícil de modificar.
