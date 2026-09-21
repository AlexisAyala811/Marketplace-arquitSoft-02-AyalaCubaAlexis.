# Drivers Arquitectónicos

## Marketplace E-commerce

| ID | Driver arquitectónico | Origen | ¿Por qué influye en la arquitectura? |
|---|---|---|---|
| DA01 | El sistema debe soportar un incremento importante de usuarios durante campañas comerciales o periodos de alta demanda. | AC03 - Escalabilidad | Puede influir en la estrategia de escalamiento, distribución de carga y despliegue del sistema. |
| DA02 | El sistema debe mantener tiempos de respuesta adecuados cuando exista una alta cantidad de usuarios concurrentes. | AC01 - Rendimiento | Puede influir en la comunicación entre componentes, procesamiento de solicitudes y acceso a los datos. |
| DA03 | El sistema debe proteger la información de clientes, vendedores, cuentas, pedidos y operaciones de pago. | AC04 - Seguridad | Puede influir en los mecanismos de autenticación, autorización, control de acceso y protección de datos. |
| DA04 | El sistema debe integrarse con una pasarela de pago externa para procesar las compras. | RC05 - Pasarela de pago | Condiciona la forma de comunicación e integración con servicios externos. |
| DA05 | El sistema debe utilizar una API REST para la comunicación entre el frontend y el backend. | RC03 - API REST | Limita y define la forma de comunicación entre las diferentes partes del sistema. |
| DA06 | El sistema debe integrarse con un servicio externo para gestionar la entrega de pedidos. | RC06 - Servicio de envío | Influye en el diseño de las integraciones externas y en el intercambio de información relacionada con pedidos y entregas. |
| DA07 | El sistema debe controlar el acceso a las funcionalidades según el rol del usuario. | RC08 - Acceso por roles | Influye en la organización de los mecanismos de autorización para clientes, vendedores y administradores. |