# Decisiones Arquitectónicas

## Marketplace E-commerce

Las siguientes decisiones registran las elecciones que estructuran la solución y los drivers que las motivan.

| ID | Decisión arquitectónica | Driver relacionado | Justificación | Resultado |
|---|---|---|---|---|
| ADR-001 | Monolito modular | DA01 - Escalabilidad<br>DA08 - Mantenibilidad | Organizar las funcionalidades en módulos independientes dentro de una misma aplicación desplegable. | Módulos de Catálogo, Carrito, Pedidos, Pagos y Usuarios. |
| ADR-002 | Clean Architecture | DA08 - Mantenibilidad | Separar las reglas del negocio de los detalles tecnológicos. | Capas de Dominio, Aplicación, Infraestructura y Presentación. |
| ADR-003 | Estrategia de caché | DA02 - Rendimiento | Reducir consultas repetitivas a la fuente de datos para información de consulta frecuente. | Caché para información de consulta frecuente. |
| ADR-004 | Integración de pagos mediante interfaces y adaptadores | DA04 - Integración con pagos | Desacoplar los casos de uso de la implementación y del proveedor de pagos. | Contrato de pagos y adaptador para la pasarela externa. |

> **Nota sobre trazabilidad:** se utiliza DA08 para mantenibilidad, ya que en el análisis del sistema DA06 corresponde a la integración con el servicio de envíos. La estrategia de caché es una decisión propuesta para atender el driver de rendimiento (DA02).

