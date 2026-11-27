
# Cambios respecto a la Entrega 1

> Los principales cambios del diseño de nuestro sistema se ha visto en la actualización y ampliación de los **requisitos funcionales** y **no funcionales**.

> Columna **N/M** indica si el requisito es "***Nuevo***" o "***Modificado***".

#### Requisitos funcionales

| ID    | N/M | Cambio                                                                                                        | Motivo                                                                                                | Impacto en Entrega 2 | Validación / pendiente               |
| ----- | --- | ------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------- | -------------------- | ------------------------------------ |
| FR-13 | M   | Contemplar además de las bajas de clientes, las pausas de las suscripciones de su cuenta                      | Funcionalidad que se pedía en la transcripción                                                        | ER y clases          | Confirmar con el cliente si procede. |
| FR-14 | M   | Definición de más concreta de cómo actúa el sistema ante bajas teniendo aún impagos                           | Evitar ambigüedad, definición vaga                                                                    | ER, clases y CU-08   | Confirmado.                          |
| FR-17 | N   | Contemplar la división y asignación de clientes por departamentos de la empresa TurbineH                      | Funcionalidad que se pedía en la transcripción                                                        | Clases y CU-08       | Confirmado.                          |
| FR-20 | M   | Indicar que una factura rectificativa no elimina ni sustituye la original. Se usan números de serie distintos | Evitar ambigüedad, definición vaga                                                                    | CU-03                | Confirmado.                          |
| FR-30 | M   | Especificar que la asignación de estado de pago de una factura se hace mediante un menú con opciones          | Desacoplar error humano para que no se tenga que indicar a mano el estado                             | CU-03                | Confirmado.                          |
| FR-37 | N   | Contemplar facturas multi-idioma                                                                              | Funcionalidad que se pedía en la transcripción                                                        | Clases y CU-03       | Confirmado.                          |
| FR-38 | N   | Contemplar facturas extraordinarias                                                                           | Funcionalidad que se pedía en la transcripción                                                        | Clases y CU-03       | Confirmado.                          |
| FR-42 | M   | Tener en cuenta impacto de descuentos extraordinarios en cuotas de facturación de clientes                    | Mantener coherencia con otros requisitos                                                              | -                    | Confirmar con el cliente si procede. |
| FR-43 | M   | Inmutabilidad de cuotas anuales de clientes incluso si cambian de rango entre medias                          | Funcionalidad que se pedía en la transcripción                                                        | -                    | Confirmado.                          |
| FR-45 | N   | Contemplar descuentos extraordinarios y precios pactados por la empresa                                       | Funcionalidad que se pedía en la transcripción                                                        | -                    | Confirmado.                          |
| FR-47 | M   | Aclaración de implementación de pasarela Stripe/Revolut como simulada                                         | Evitar ambigüedad, definición vaga                                                                    | Clases y CU-02       | Confirmado.                          |
| FR-48 | M   | Comprobaciones de verificación de facturas antes de marcar como pagada                                        | Añadir verificación de datos de facturas para garantizar su actualización automática correcta         | CU-02                | Confirmar con el cliente si procede. |
| FR-51 | N   | Acción ante incoherencia de importe de pago de facturas                                                       | Especificar cómo actúa el sistema si se registra un pago con importe distinto al que lista su factura | Arquitectura         | Confirmar con el cliente si procede. |
| FR-52 | N   | Contemplar deudas incobrables                                                                                 | Funcionalidad que se pedía en la transcripción                                                        | Clases y CU-06       | Confirmado.                          |
| FR-53 | N   | Contemplar condonaciones de deudas                                                                            | Funcionalidad que se pedía en la transcripción                                                        | Clases y CU-06       | Confirmado.                          |
| FR-61 | M   | Tener en cuenta notificaciones a usuarios del sistema en caso de incoherencia de pagos de facturas            | Mantener coherencia con otros requisitos                                                              | Arquitectura y CU-02 | Confirmar con el cliente si procede. |
| FR-63 | N   | Bandeja de notificaciones de usuarios                                                                         | Proporcionar forma de consulta de solicitudes e incidencias de sus clientes asignados                 | Arquitectura         | Confirmar con el cliente si procede. |
| FR-86 | M   | Especificar las funcionalidades del dashboard financiero                                                      | Evitar ambigüedad, definición vaga                                                                    | Clases y CU-06       | Confirmar con el cliente si procede. |
| FR-87 | M   | Especificar reporte de pagos condonados e incobrables en el dashboard                                         | Mantener coherencia con otros requisitos                                                              | Clases y CU-06       | Confirmar con el cliente si procede. |
| FR-89 | N   | Funciones de búsqueda filtrada del dashboard                                                                  | Proporcionar forma de consulta eficaz                                                                 | Clases y CU-06       | Confirmar con el cliente si procede. |
| FR-95 | N   | Sistema de logs                                                                                               | Trazabilidad de acciones de usuarios dentro del sistema                                               | Arquitectura         | Confirmar con el cliente si procede. |
| FR-96 | N   | Flujo de reintentos automáticos del sistema ante fallos recuperables (hasta un máximo establecido)            | Dotar al sistema de una cierta capacidad de resistencia ante errores                                  | Clases               | Confirmar con el cliente si procede. |

#### Requisitos no funcionales

| ID     | N/M | Cambio                                                                                                              | Motivo                                                                      | Impacto en Entrega 2 | Validación / pendiente               |
| ------ | --- | ------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------- | -------------------- | ------------------------------------ |
| NFR-04 | M   | Especificar características y capacidades de la interfaz web                                                        | Evitar ambigüedad, definición vaga                                          | -                    | Confirmado.                          |
| NFR-08 | M   | Especificación de mecanismos automáticos fiables y  errores detectables                                             | Evitar ambigüedad, definición vaga                                          | -                    | Confirmar con el cliente si procede. |
| NFR-09 | M   | Especificación de sistema de notificaciones fiable y errores detectables                                            | Evitar ambigüedad, definición vaga                                          | -                    | Confirmar con el cliente si procede. |
| NFR-10 | M   | Especificación de niveles de permiso y flujo de acceso controlado del sistema                                       | Evitar ambigüedad, definición vaga                                          | -                    | Confirmado.                          |
| NFR-13 | M   | Limitación de acceso a datos personales de clientes a los usuarios adecuados                                        | Aclarar restricción de acceso a datos de clientes                           | -                    | Confirmado.                          |
| NFR-14 | M   | Conservación temporal de objetos del sistema (clientes, usuarios y facturas) acordes al marco legal correspondiente | Aclarar la conservación de información del sistema según la normativa legal | -                    | Confirmado.                          |

#### Especificaciones de casos de uso

> Ausentes en la primera entrega debido a un despiste del equipo. Añadidas a la actualización de la primera entrega debidamente, acordes con el diagrama de casos de uso general que sí se presentó.

| ID    | N/M | Cambio                                           | Motivo               | Impacto en Entrega 2                  | Validación / pendiente               |
| ----- | --- | ------------------------------------------------ | -------------------- | ------------------------------------- | ------------------------------------ |
| CU-01 | N   | Autenticación de usuario                         | Ausente en entrega 1 | ER, clases, secuencias y arquitectura | Confirmar con el cliente si procede. |
| CU-02 | N   | Consulta del historial de facturas               | Ausente en entrega 1 | ER, clases, secuencias y arquitectura | Confirmar con el cliente si procede. |
| CU-03 | N   | Generación de facturas                           | Ausente en entrega 1 | ER, clases, secuencias y arquitectura | Confirmar con el cliente si procede. |
| CU-04 | N   | Modificación de preferencias de pagos            | Ausente en entrega 1 | ER, clases, secuencias y arquitectura | Confirmar con el cliente si procede. |
| CU-05 | N   | Consulta de documentación de uso del sistema     | Ausente en entrega 1 | ER, clases, secuencias y arquitectura | Confirmar con el cliente si procede. |
| CU-06 | N   | Acceso al dashboard de facturación               | Ausente en entrega 1 | ER, clases, secuencias y arquitectura | Confirmar con el cliente si procede. |
| CU-07 | N   | Gestión de usuarios del sistema                  | Ausente en entrega 1 | ER, clases, secuencias y arquitectura | Confirmar con el cliente si procede. |
| CU-08 | N   | Gestión de clientes                              | Ausente en entrega 1 | ER, clases, secuencias y arquitectura | Confirmar con el cliente si procede. |
| CU-09 | N   | Consulta de historial de modificaciones          | Ausente en entrega 1 | ER, clases, secuencias y arquitectura | Confirmar con el cliente si procede. |
| CU-10 | N   | Modificación de configuración global del sistema | Ausente en entrega 1 | ER, clases, secuencias y arquitectura | Confirmar con el cliente si procede. |

---

### Anotaciones

> Anotaciones que tomó el equipo tras la primera entrega, en la que basamos la mayoría de cambios listados arriba.

- Plantear en algún diagrama/presentación explicativa un **flujo general del proceso de facturas** (e.g. Perritos y Hienitas).

- Hacer hincapié en la **interfaz móvil** (especificar la adaptabilidad del sistema) + diseñar interfaz web en ordenador?

- División por **departamentos** de clientes.

- Especificar un poco más acerca del **dashboard financiero** del sistema.

- Indicar explícitamente que la **pasarela** con Stripe/Revolut será **simulada**.

- **Privacidad** un poco más específica? requisitos funcionales que tengan que ver con ella?

- **Importante**: cancelar una deuda -> no marcarla como PAGADA  (falseamiento de datos financieros) -> un estado distinguido del resto (e.g. condonada/anulada).

- Añadir ejemplos de **"edge-cases" prácticos** (como los Perritos).

- Contemplar **descuentos extraordinarios**, precios pactados o facturas fuera de lo habitual que necesitan aprobación (revisión manual de admin/superadmin).

- Distinguir **seguimiento de deudas** del estado de la suscripción del cliente para evitar generar facturas durante una pausa o baja del cliente.

- Facturas extraordinarias.

- Facturas multi-idioma.

- Campo adicional de factura para indicar si está rectificando a una (relacionarla claramente) ó incluir en el propio formato del identificador (e.g. 178239203-1, donde el "-1" indica que es una primera rectificación sobre la original, 178239203).

- **Factura incobrable**/irresoluble, en casos especiales en los que por motivos legales (e.g. quiebra del cliente) una factura no vaya a poder ser pagada -> Turbine debe encargarse personalmente de contactar con la empresa.

- **Comprobación de pagos** con la pasarela simulada Stripe. ¿Qué hacer cuando haya pagos no identificados, incoherencia de datos o eventos repetidos? -> respuesta automática (e.g. descartar eventos repetidos) / mandar a revisión manual de admin/superadmin (pago no identificado/datos incoherentes en factura).

- **Notificación a usuarios del sistema** por impagos, incidencias, aprobaciones, revisiones... ¿Tener una **bandeja de notificaciones** para que vea sus tareas pendientes?

- **Fallos en procesos automatizados** -> probar un número determinado de reintentos y si sigue sin haber éxito, notificar a admins/superadmin.

