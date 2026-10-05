# Escenarios de atributos de calidad del Marketplace

## EQ-01. Rendimiento: consulta de productos

| Elemento | Descripción |
|---|---|
| **Atributo de calidad** | Rendimiento |
| **Fuente** | Cliente |
| **Estímulo** | El cliente busca productos, consulta categorías o revisa el detalle de un producto. |
| **Condición** | El Marketplace funciona normalmente y varios usuarios realizan consultas de forma simultánea. |
| **Respuesta esperada** | El sistema muestra los resultados de búsqueda y los detalles de los productos sin errores inesperados. |
| **Medida** | **Propuesta por validar:** el 95 % de las consultas debe responder en 2 segundos o menos, con menos del 1 % de errores técnicos, durante una prueba con 200 usuarios simultáneos. |
| **Verificación** | Ejecutar pruebas de rendimiento y registrar tiempos de respuesta, errores y número de usuarios simultáneos. |
| **Estado** | Pendiente de validación de las medidas y del entorno de prueba. |

## EQ-02. Disponibilidad: continuidad del servicio

| Elemento | Descripción |
|---|---|
| **Atributo de calidad** | Disponibilidad |
| **Fuente** | Cliente |
| **Estímulo** | El cliente intenta realizar una compra o consultar productos durante un periodo de alta demanda o ante una falla de algún componente. |
| **Condición** | El sistema se encuentra desplegado y cuenta con mecanismos de monitoreo. |
| **Respuesta esperada** | El sistema mantiene disponibles las operaciones que puede atender y comunica los errores de forma controlada cuando un servicio necesario no responde. |
| **Medida** | **Propuesta por validar:** disponibilidad mensual de 99,5 % para las operaciones críticas del Marketplace. |
| **Verificación** | Revisar los registros de monitoreo y calcular los periodos de disponibilidad e interrupción. |
| **Estado** | Pendiente de aprobar la meta y definir las operaciones críticas. |

## EQ-03. Escalabilidad: aumento de usuarios

| Elemento | Descripción |
|---|---|
| **Atributo de calidad** | Escalabilidad |
| **Fuente** | Incremento de solicitudes de clientes |
| **Estímulo** | Aumenta la cantidad de usuarios que utilizan el Marketplace simultáneamente. |
| **Condición** | Se ejecuta una prueba de carga con datos, configuración y solicitudes previamente definidas. |
| **Respuesta esperada** | El sistema soporta el incremento de usuarios sin superar los límites establecidos de tiempo de respuesta y errores. |
| **Medida** | **Propuesta por validar:** soportar hasta 200 usuarios simultáneos, con el 95 % de las respuestas por debajo de 3 segundos y menos del 1 % de errores técnicos. |
| **Verificación** | Incrementar progresivamente la carga y comparar los tiempos de respuesta, errores y uso de recursos. |
| **Estado** | Pendiente de confirmar la cantidad esperada de usuarios y la infraestructura de prueba. |

## EQ-04. Seguridad: acceso a operaciones protegidas

| Elemento | Descripción |
|---|---|
| **Atributo de calidad** | Seguridad |
| **Fuente** | Usuario sin sesión, con credenciales inválidas o sin permisos suficientes |
| **Estímulo** | El usuario intenta acceder a funciones protegidas o realizar una operación no autorizada. |
| **Condición** | Se realizan pruebas sobre las funciones protegidas del Marketplace. |
| **Respuesta esperada** | El sistema rechaza la operación, no ejecuta cambios y no expone información sensible. |
| **Medida** | Rechazar el 100 % de los intentos no autorizados incluidos en las pruebas definidas, sin exposición de datos sensibles. |
| **Verificación** | Ejecutar pruebas de acceso autorizado y no autorizado y revisar las respuestas del sistema. |
| **Estado** | Pendiente de definir completamente los permisos por tipo de usuario. |

## EQ-05. Mantenibilidad: cambios sin afectar otras funciones

| Elemento | Descripción |
|---|---|
| **Atributo de calidad** | Mantenibilidad |
| **Fuente** | Equipo de desarrollo |
| **Estímulo** | Se modifica una regla de negocio o se necesita sustituir una integración externa, como la pasarela de pago. |
| **Condición** | El sistema está organizado en módulos y dispone de pruebas para las funciones afectadas. |
| **Respuesta esperada** | El cambio se concentra en el módulo responsable sin afectar funciones no relacionadas. |
| **Medida** | Las pruebas del módulo modificado y las pruebas de regresión deben aprobarse. Las dependencias entre módulos deben respetar las reglas de arquitectura establecidas. |
| **Verificación** | Revisar los cambios de código, ejecutar pruebas e inspeccionar las dependencias entre módulos. |
| **Estado** | Pendiente de definir las reglas de dependencias y las pruebas obligatorias para los cambios. |

## Resumen de escenarios

| Código | Atributo de calidad | Qué se busca comprobar |
|---|---|---|
| EQ-01 | Rendimiento | Que el Marketplace responda dentro del tiempo establecido. |
| EQ-02 | Disponibilidad | Que las operaciones críticas estén disponibles cuando se necesiten. |
| EQ-03 | Escalabilidad | Que el sistema soporte un aumento de usuarios dentro de los límites definidos. |
| EQ-04 | Seguridad | Que las operaciones protegidas no puedan ejecutarse sin autorización. |
| EQ-05 | Mantenibilidad | Que los cambios puedan realizarse sin afectar innecesariamente otras funciones. |