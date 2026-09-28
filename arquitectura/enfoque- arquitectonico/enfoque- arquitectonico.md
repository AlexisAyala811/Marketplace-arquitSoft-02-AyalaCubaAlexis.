# Enfoque Arquitectónico

## Marketplace E-commerce

### Resumen del enfoque

| Elemento | Descripción aplicada al Marketplace |
|---|---|
| Patrón / enfoque arquitectónico | **Clean Architecture (Arquitectura Limpia)** para la aplicación web Angular, integrada con el backend del Marketplace mediante una API REST. |
| Objetivo | Separar responsabilidades y mantener las dependencias de código dirigidas hacia las reglas del dominio. |
| Problema que resuelve | Reduce el acoplamiento entre la interfaz Angular, los casos de uso, las reglas de negocio y los detalles técnicos como HTTP, persistencia, pagos y notificaciones. |
| Capas definidas | **Presentación**, **Aplicación**, **Dominio** e **Infraestructura**. |
| Beneficios | Facilita mantenimiento y pruebas unitarias; permite sustituir implementaciones técnicas sin modificar las reglas del negocio; mejora la organización y separación de responsabilidades. |

### Diagrama de arquitectura

```mermaid
flowchart LR
	Cliente["Usuario<br/>(cliente)"]

	subgraph FRONTEND["Aplicación web · Angular 18 · TypeScript"]
		direction LR

		subgraph PRESENTACION["Presentación · src/app/presentation"]
			direction TB
			CatalogoUI["CatalogoComponent<br/>lista y filtra productos"]
			CarritoState["EstadoCarritoService<br/>ítems · cantidades · totales"]
			CarritoUI["CarritoComponent<br/>resumen y confirmar compra"]
			AppUI["AppComponent<br/>shell de la aplicación"]
			AppUI --> CatalogoUI
			AppUI --> CarritoUI
			CarritoUI --> CarritoState
		end

		subgraph APLICACION["Aplicación · casos de uso"]
			direction TB
			UCatalogo["ConsultarCatalogoCasoUso"]
			UCarrito["AgregarAlCarritoCasoUso"]
			UCompra["RegistrarCompraCasoUso"]
		end

		subgraph DOMINIO["Dominio · núcleo independiente de frameworks"]
			direction TB
			subgraph MODELOS["Modelos y reglas"]
				direction LR
				Producto["Producto<br/>stock · categoría · precio"]
				Carrito["Carrito<br/>ítems · cantidades · total"]
				Pedido["Pedido<br/>estados · cancelación"]
				Politica["politicas.ts<br/>comisión · IVA"]
			end
			subgraph CONTRATOS["Contratos · puertos"]
				direction TB
				PortProductos["RepositorioProductos"]
				PortPedidos["RepositorioPedidos"]
				PortPago["ProcesadorPagos"]
				PortNotificacion["NotificadorCliente"]
			end
		end

		subgraph INFRA["Infraestructura · adaptadores y frameworks"]
			direction TB
			Config["app.config.ts<br/>composition root · inyección"]
			Tokens["tokens.ts<br/>tokens de inyección"]
			RepoProductosHTTP["RepositorioProductosHttp"]
			RepoProductosMem["RepositorioProductosMemoria<br/>pruebas y desarrollo"]
			RepoPedidosHTTP["RepositorioPedidosHttp"]
			PagoSimulado["ProcesadorPagoSimulado"]
			Notificador["NotificadorConsola<br/>o adaptador WhatsApp"]
			Config -. "registra implementaciones" .-> Tokens
			Tokens -. "resuelve puertos" .-> RepoProductosHTTP
			Tokens -.-> RepoPedidosHTTP
			Tokens -.-> PagoSimulado
			Tokens -.-> Notificador
		end

		CatalogoUI --> UCatalogo
		CarritoState --> UCarrito
		CarritoUI --> UCompra

		UCatalogo --> Producto
		UCatalogo --> PortProductos
		UCarrito --> Carrito
		UCompra --> Pedido
		UCompra --> Politica
		UCompra --> PortPedidos
		UCompra --> PortPago
		UCompra --> PortNotificacion

		RepoProductosHTTP -. "implementa" .-> PortProductos
		RepoProductosMem -. "implementa" .-> PortProductos
		RepoPedidosHTTP -. "implementa" .-> PortPedidos
		PagoSimulado -. "implementa" .-> PortPago
		Notificador -. "implementa" .-> PortNotificacion
	end

	subgraph API["Backend Marketplace · API REST · Node.js / Express"]
		direction TB
		Rutas["Endpoints<br/>/api/v1/catalogo<br/>/api/v1/pedidos<br/>/api/v1/pagos"]
		Modulos["Módulos del backend<br/>catálogo · pedidos · pagos<br/>autenticación y autorización"]
		DB[("Base de datos<br/>Marketplace")]
		Rutas --> Modulos
		Modulos --> DB
	end

	Cliente --> CatalogoUI
	RepoProductosHTTP -->|"HTTPS · JSON"| Rutas
	RepoPedidosHTTP -->|"HTTPS · JSON"| Rutas
	PagoSimulado -->|"HTTPS · JSON"| Rutas

	classDef actor fill:#fff,stroke:#64748b,color:#172033
	classDef presentation fill:#e7f0ff,stroke:#4d78b8,color:#172033
	classDef application fill:#e8f3e6,stroke:#56814c,color:#172033
	classDef domain fill:#fff3d6,stroke:#b88735,color:#172033
	classDef ports fill:#fff9e9,stroke:#b88735,color:#172033
	classDef infrastructure fill:#f3eafb,stroke:#8562a3,color:#172033
	classDef api fill:#eeeeee,stroke:#777,color:#172033
	classDef database fill:#e5f2ef,stroke:#408579,color:#172033

	class Cliente actor
	class CatalogoUI,CarritoState,CarritoUI,AppUI presentation
	class UCatalogo,UCarrito,UCompra application
	class Producto,Carrito,Pedido,Politica domain
	class PortProductos,PortPedidos,PortPago,PortNotificacion ports
	class Config,Tokens,RepoProductosHTTP,RepoProductosMem,RepoPedidosHTTP,PagoSimulado,Notificador infrastructure
	class Rutas,Modulos api
	class DB database
```

**Lectura del diagrama:** las flechas continuas muestran el flujo de ejecución entre interfaz, casos de uso y servicios externos. Las flechas punteadas muestran la configuración de dependencias y la implementación de los puertos del dominio por adaptadores de infraestructura. Aunque durante la ejecución un caso de uso utiliza un adaptador, el dominio solo conoce el contrato, no la tecnología que lo implementa.

### Responsabilidades por capa

- **Presentación:** componentes Angular y estado de interfaz. Recibe acciones del usuario, presenta datos y delega operaciones a los casos de uso; no implementa reglas del negocio ni accede directamente a HTTP o persistencia.
- **Aplicación:** casos de uso que coordinan las operaciones del Marketplace, como consultar el catálogo, modificar el carrito y registrar una compra. Depende de modelos y contratos del dominio.
- **Dominio:** entidades, políticas y contratos que expresan las reglas esenciales del negocio. No importa Angular, Express, Sequelize ni bibliotecas de infraestructura.
- **Infraestructura:** implementaciones concretas de los contratos, configuración de inyección, comunicación HTTP, persistencia, pagos y notificaciones. Sus adaptadores pueden sustituirse sin cambiar las reglas del dominio.
- **Backend API REST:** servicio separado del cliente web que autentica y autoriza solicitudes, ejecuta los módulos del Marketplace y persiste la información. El frontend lo consume a través de adaptadores HTTP.

### Reglas de dependencia

1. Las dependencias de código apuntan hacia el dominio: presentación y aplicación pueden usar el dominio; infraestructura implementa sus contratos.
2. Las entidades y políticas del dominio no importan frameworks, servicios externos ni detalles de almacenamiento.
3. Los casos de uso dependen de interfaces del dominio, no de implementaciones concretas como HTTP o una base de datos específica.
4. La composición de implementaciones ocurre en el punto de configuración (`app.config.ts`), mediante tokens de inyección.
5. Las pruebas pueden proporcionar adaptadores en memoria o simulados para verificar casos de uso sin llamadas de red ni servicios reales.

### Beneficios y consideraciones

La separación facilita probar las reglas de negocio de forma aislada, cambiar proveedores técnicos y evolucionar la interfaz sin rehacer el núcleo del sistema. La API REST mantiene desacoplados el cliente Angular y el backend, que pueden desarrollarse y desplegarse de manera independiente.

El costo principal es mantener contratos, adaptadores y configuración de dependencias. Para que la arquitectura aporte valor, las reglas de negocio deben permanecer dentro del dominio y los casos de uso, en lugar de dispersarse en componentes o adaptadores.
