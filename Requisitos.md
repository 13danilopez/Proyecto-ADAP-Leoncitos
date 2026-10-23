# Requisitos

## 1. Contexto y alcance

### 1.1. Objetivo del sistema

> Breve descripción del problema que se pretende resolver y del objetivo general del sistema.

- **Problema**: Reside principalmente en la carga de trabajo administrativo que supone gestionar manualmente la facturación y seguimiento de cobros de TurbineH, el cual ahora mismo se realiza de manera manual y requiere estar pendiente de los plazos de los clientes y de las fechas, modificaciones, recordatorios por impago, etc.

- **Objetivo**: Generar una plataforma simple, ágil e intuitiva que automatice la gestión de pagos y cobros de la empresa. El enfoque de la plataforma debe ser de uso exclusivo interno e independiente de BrainOS, y los clientes no deben tener acceso a ella. Se hace especial énfasis en garantizar la fiabilidad de los procesos automáticos (facturación, seguimiento de cobros), la trazabilidad de las modificaciones realizadas ("historial"), y la modularidad de la implementación software para facilitar futuras ampliaciones.

### 1.2. Alcance

> Descripción de qué responsabilidades corresponden al sistema y, cuando sea relevante, qué elementos quedan explícitamente fuera de su alcance.

- **Dentro de nuestro alcance contemplamos**:
	- La gestión de los usuarios registrados por parte de los administradores.
	- La gestión de los importes de las facturas según el rango de facturación del cliente.
	- La generación automática y veloz de facturas.
	- La gestión automática de las notificaciones de pago/impago.
	- Un dashboard que muestre informes de contabilidad (facturación semanal, mensual y anual).
	- El acceso a funciones de configuración del sistema y control de permisos (máximo privilegio) por parte del superadministrador.
	- Un mecanismo de trazabilidad de las modificaciones de datos de usuarios y facturas.
	- La gestión de los datos de la aplicación a través de una base de datos externa a la de TurbineH (ellos implementarán la suya).

- **Fuera de nuestro alcance quedan**: 
	- La creación de una pasarela de pago propia (gestión del cobro).
	- La integración de las cuentas de Stripe o Revolut.
	- La implementación de la IA de BrainOS en la plataforma (automatización sin IA implementada).
	- Un sistema de servicio de atención al cliente (comunicación a través del contacto de la empresa).
	- La conexión de la plataforma con la base de datos de la empresa y la plataforma de pago que usan.
	- El desarrollo de una GUI avanzadas que se adapten a una estética determinada.

### 1.3. Actores y partes interesadas

> Identificar las personas, organizaciones o sistemas que interactúan con el sistema o tienen algún interés relevante en su funcionamiento.

La organización principal que va a interactuar con este sistema es la propia empresa de TurbineH.

| Actor / stakeholder                         | Descripción                                                            | Intereses principales                                                                     |
| ------------------------------------------- | ---------------------------------------------------------------------- | ----------------------------------------------------------------------------------------- |
| Personal administrativo                     | Personal de Turbine encargado de gestionar clientes, facturas y cobros | Realizar las tareas administrativas de forma rápida y disponer de información actualizada |
| Superadministrador                          | Usuario con máximo nivel de privilegios                                | Gestionar configuración del sistema y permisos de otros usuarios                          |
| Administrador y lector                      | Usuarios con permisos sobre la gestión de los clientes                 | Gestionar clientes y facturas                                                             |
| Sistema externo de pagos (Stripe y Revolut) | Servicio externo en el que se reflejan los cobros                      | Proporcionar información que permita determinar si una factura ha sido pagada             |
| Cliente de Turbine                          | Empresa a la que Turbine presta sus servicios y factura periódicamente | Recibir correctamente sus facturas y comunicaciones relacionadas con los pagos            |
| Personal de contacto/soporte                | Personal de Turbine encargado del soporte al usuario                   | Comunicarse fácilmente a través de los medios acordados (e-mail y WhatsApp)               |


## 2. Requisitos de negocio

> Los requisitos de negocio describen los objetivos, necesidades o resultados que la organización pretende conseguir mediante el desarrollo del sistema. No describen todavía funcionalidades concretas del software.

| ID    | Requisito                                                                                  | Fuente               |
| ----- | ------------------------------------------------------------------------------------------ | -------------------- |
| BR-01 | Disponer de una plataforma de facturas y cobros propia de la empresa                       | Cliente / entrevista |
| BR-02 | Reducir el tiempo del personal administrativo en las operaciones                           | Cliente / entrevista |
| BR-03 | Agilización de los pagos                                                                   | Cliente / entrevista |
| BR-04 | Autogestión fiable de las facturas para minimizar posibles errores por gestión manual      | Decisión de equipo   |
| BR-05 | Facilitar la trazabilidad de las operaciones de pago                                       | Cliente / entrevista |
| BR-06 | Disponer de información sobre la facturación económica de la empresa semanal/mensual/anual | Cliente / entrevista |
| BR-07 | Poder disponer de un modelo escalable según el número de usuarios                          | Decisión de equipo   |


## 3. Requisitos de usuario

> Los requisitos de usuario describen las necesidades, objetivos o servicios que los punto de vista del usuario, evitando entrar todavía en detalles internos de implementación.

| ID    | Actor                   | Requisito                                                                                                      | Fuente               |
| ----- | ----------------------- | -------------------------------------------------------------------------------------------------------------- | -------------------- |
| UR-01 | Personal administrativo | Poder generar y gestionar facturas de los clientes sin tener que realizar manualmente cada facturación mensual | Cliente / entrevista |
| UR-02 | Personal administrativo | Poder conocer qué facturas están pagadas y cuáles siguen pendientes                                            | Cliente / entrevista |
| UR-03 | Administrador           | Poder gestionar los usuarios (lectores) del sistema y determinar a qué clientes puede acceder cada uno         | Cliente / entrevista |
| UR-04 | Personal administrativo | Poder iniciar sesión (no registrarse) con su correo corporativo y contraseña                                   | Cliente / entrevista |
| UR-05 | Superadministrador      | Poder modificar la configuración general del sistema                                                           | Cliente / entrevista |
| UR-06 | Administrador           | Poder dar de alta a clientes                                                                                   | Cliente / entrevista |
| UR-07 | Personal administrativo | Poder consultar un historial de facturas y descargarlas                                                        | Cliente / entrevista |
| UR-08 | Lector                  | Poder crear facturas (puntuales, por fecha y recurrentes) y consultar su estado                                | Cliente / entrevista |
| UR-09 | Personal administrativo | Tener acceso al dashboard informativo del sistema                                                              | Cliente / entrevista |


## 4. Requisitos del sistema

### 4.1. Requisitos funcionales

> Los requisitos funcionales describen comportamientos, servicios u operaciones que debe proporcionar el sistema. Siempre que sea posible, deben formularse de manera clara, precisa y verificable.

| ID    | Requisito                                                                                                                                                       | Relacionado con |
| ----- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------- |
|       | **GESTIÓN DE CLIENTES**                                                                                                                                         |                 |
| FR-01 | El sistema permitirá dar de alta un cliente (empresa) con sus datos comerciales y fiscales: nombre, nombre comercial, razón social, NIF, dirección fiscal, país, moneda de trabajo y datos de contacto | UR-06 |
| FR-02 | Los datos de contacto serán un email o teléfono                                                                                                                 | UR-06           |
| FR-03 | El sistema deberá poder almacenar varios contactos de la empresa de cada cliente                                                                                 | UR-06           |
| FR-04 | El sistema deberá mostrar varios datos de interés en la "ficha" de cada cliente: inicio de plan, tipo de plan, responsable comercial, condiciones de pago        | UR-03, UR-06    |
| FR-05 | El sistema deberá poder adjuntar otros documentos firmados con el cliente fuera de la plataforma (acuerdo comercial)                                           | UR-06           |
| FR-06 | Todo cliente o dato modificado se guardará inmediatamente y podrá editarse en cualquier momento posterior                                                       | UR-03, UR-06    |
| FR-07 | El sistema asignará a cada cliente un rango de facturación anual, del que depende su cuota                                                                      | UR-06           |
| FR-08 | Al dar de alta un cliente se indicará si su facturación es recurrente o no (puntual), junto con su rango de facturación y la cantidad                          | UR-06           |
| FR-09 | El cliente tendrá un estado (activo, inactivo/pausa, baja solicitada, baja)                                                                                    | UR-06           |
| FR-10 | Distinción entre plan de cliente en pausa (motivo, fecha prevista de reactivación) o de baja (baja de los servicios confirmada por empresa)                    | UR-06           |
| FR-11 | El sistema permitirá etiquetar clientes de forma flexible (estratégico, beta, partner, riesgo de cobro, etc.). Diferente a los estados                         | UR-06           |
| FR-12 | La baja de un cliente será un borrado lógico: sus datos se conservarán durante el plazo que permita la ley                                                     | UR-06           |
| FR-13 | Al solicitar la baja, el sistema cancelará automáticamente la recurrencia y no generará nuevas facturas                                                       | UR-01, UR-06    |
| FR-14 | Si el cliente tiene facturas impagadas, la baja no se completará: se le notificará y se mantendrá el flujo de seguimiento hasta que pague                       | UR-02, UR-06    |
| FR-15 | Cada cliente tendrá un usuario de Turbine asignado como responsable                                                                                             | UR-03           |
| FR-16 | El sistema permitirá importar clientes de forma masiva mediante un archivo CSV                                                                                  | UR-06           |
|       | **GESTIÓN DE FACTURAS**                                                                                                                                         |                 |
| FR-17 | El sistema permitirá crear facturas puntuales para un cliente                                                                                                   | UR-01, UR-08    |
| FR-18 | El sistema permitirá crear facturas recurrentes (mensuales) para un cliente                                                                                    | UR-01, UR-08    |
| FR-19 | El sistema permitirá crear una facturas rectificativas con los datos iniciales de la última factura emitida en caso de no poder modificarla por motivos legales| UR-01           |
| FR-20 | El importe de una factura será editable únicamente en el momento de su creación (si es incorrecto se deberá crear una factura rectificadora)                  | UR-01           |
| FR-21 | El plazo de pago por defecto de una factura será de 5 días desde su emisión                                                                                    | UR-01           |
| FR-22 | Las facturas recurrentes se generarán y enviarán automáticamente entre el día 1 y 5 de cada mes                                                               | UR-01           |
| FR-23 | Si un cliente se da de alta a mitad de mes, se emitirá una factura proporcional a los días restantes, y la siguiente el día 1                                 | UR-01           |
| FR-24 | El sistema permitirá generar automáticamente un PDF de cada factura con un formato estandarizado                                                               | UR-01, UR-07    |
| FR-25 | El contenido del PDF será idéntico al guardado en la aplicación y al enviado al cliente (no pueden haber incoherencias)                                         | UR-07           |
| FR-26 | Las facturas se almacenarán en la base de datos y cada cliente tendrá su historial completo                                                                    | UR-07           |
| FR-27 | Las facturas serán editables antes de la emisión de la misma con cambios trazables                                                                             | UR-01           |
| FR-28 | Las facturas podrán ser descargadas en PDF por los usuarios                                                                                                    | UR-07           |
| FR-29 | Cada factura tendrá un estado (pendiente, pagada, vencida/impagada)                                                                                             | UR-02, UR-08    |
| FR-30 | Una factura podrá modificarse hasta el cierre del trimestre natural de su fecha; después quedará bloqueada                                                    | UR-01           |
| FR-31 | Las facturas serán numeradas con una serie y número identificador dentro de esa serie (configurable por superadministrador)                                    | UR-01, UR-05    |
| FR-32 | Los números de serie de facturas rectificativas y normales deben ser distintos                                                                                  | UR-01           |
| FR-33 | Una factura en borrador (todavía no creada) no consume un número identificador                                                                                  | UR-01           |
| FR-34 | El reenvío de una factura en caso de un error no cambia el identificador de la misma (que no se generen duplicados)                                           | UR-01           |
| FR-35 | El borrador de una factura puede ser eliminado, pero no una factura ya emitida                                                                                | UR-01           |
|       | **BÚSQUEDA Y FILTRADO DE CLIENTES Y FACTURAS**                                                                                                                  |                 |
| FR-36 | Se podrán buscar clientes por nombre y NIF                                                                                                                      | UR-03, UR-06    |
| FR-37 | Se podrán filtrar clientes por país, sector, estado, fecha de alta                                                                                              | UR-03, UR-06    |
| FR-38 | Se podrán filtrar facturas por estado (pagada, pendiente, impagada), tipo (recurrente, puntual) y fecha                                                         | UR-02, UR-07, UR-08 |
|       | **TARIFAS Y RECURRENCIA**                                                                                                                                       |                 |
| FR-39 | La cuota mensual se calculará según una tabla de rangos de facturación anual, configurable por el superadministrador                                           | UR-05           |
| FR-40 | La cuota de un cliente será constante durante todo el año, salvo cambio de rango                                                                                | UR-01           |
| FR-41 | El sistema avisará cada año de la revisión de los rangos de facturación de cada cliente                                                                         | UR-01           |
|       | **SEGUIMIENTO DE COBROS**                                                                                                                                       |                 |
| FR-42 | El sistema consultará automáticamente el sistema de cobros (como mínimo Stripe) para comprobar si una factura está pagada                                     | UR-02           |
| FR-43 | La consulta de cobros se implementará como un módulo simulado e intercambiable, para integrar después Stripe/Revolut reales                                  | UR-02           |
| FR-44 | Al detectar el pago, el sistema marcará la factura como pagada automáticamente                                                                                 | UR-02           |
| FR-45 | Al detectar el pago, el sistema detendrá el flujo de recordatorios                                                                                              | UR-01, UR-02    |
| FR-46 | Superado el plazo sin pago, el sistema marcará la factura como vencida/impagada                                                                                | UR-02           |
|       | **NOTIFICACIONES**                                                                                                                                              |                 |
| FR-47 | Al generar una factura se notificará al cliente adjuntando la factura                                                                                           | UR-01           |
| FR-48 | El sistema enviará un recordatorio de pago al cliente el día 3                                                                                                  | UR-01           |
| FR-49 | El sistema enviará un segundo recordatorio el día 5                                                                                                             | UR-01           |
| FR-50 | Desde el día 5 sin pago, se enviarán avisos de impago cada 2-3 días hasta que se detecte el pago, sin cancelar ni sancionar al cliente                          | UR-01, UR-02    |
| FR-51 | Las notificaciones al cliente se enviarán por correo electrónico                                                                                               | UR-01           |
| FR-52 | Las notificaciones al cliente podrán enviarse también por WhatsApp                                                                                              | UR-01           |
| FR-53 | Toda notificación al cliente incluirá los datos de contacto de soporte (email y teléfono)                                                                       | UR-01           |
| FR-54 | El usuario de Turbine asignado al cliente recibirá una notificación cuando se detecte un pago y en cada aviso de impago                                       | UR-02           |
| FR-55 | El sistema mantendrá un registro de cada notificación enviada (destinatario, canal, fecha, estado)                                                             | UR-01, UR-07    |
|       | **AUTENTICACIÓN**                                                                                                                                              |                 |
| FR-56 | Los usuarios iniciarán sesión con correo corporativo y contraseña                                                                                               | UR-04           |
| FR-57 | El sistema verificará que el correo pertenece al dominio de Turbine y rechazará correos personales o de otras empresas                                         | UR-04           |
| FR-58 | No existirá registro público: las cuentas sólo las crean usuarios autorizados de Turbine, y los clientes como tal no tendrán acceso                           | UR-03, UR-04    |
|       | **FUNCIONES DEL LECTOR**                                                                                                                                        |                 |
| FR-59 | El lector solo verá clientes y facturas que tenga asignados, y no verá datos de los demás                                                                       | UR-03, UR-08    |
| FR-60 | El lector podrá crear facturas                                                                                                                                  | UR-08           |
| FR-61 | El lector podrá editar los datos y propiedades (estado, tipo, fecha) de facturas a excepción del importe                                                        | UR-08           |
| FR-62 | El lector podrá editar los datos, estado, tipo de plan y rango de facturación de sus clientes asignados                                                         | UR-08           |
| FR-63 | El lector podrá consultar el estado de las facturas asignadas y descargarlas en PDF                                                                             | UR-07, UR-08    |
|       | **FUNCIONES DEL ADMINISTRADOR Y GESTIÓN DE USUARIOS**                                                                                                           |                 |
| FR-64 | El administrador podrá dar de alta usuarios del sistema                                                                                                         | UR-03           |
| FR-65 | El administrador asignará el rol (administrador o lector) a cada usuario                                                                                        | UR-03           |
| FR-66 | El administrador asignará a cada lector los clientes que puede ver                                                                                             | UR-03           |
| FR-67 | La asignación de clientes a lectores dispondrá de buscador y filtros (país, sector, estado, fecha de alta)                                                     | UR-03           |
| FR-68 | El administrador podrá editar y desactivar usuarios                                                                                                             | UR-03           |
| FR-69 | El administrador tendrá acceso y capacidad de gestión sobre todos los clientes y facturas                                                                       | UR-01, UR-03, UR-06 |
|       | **FUNCIONES DEL SUPERADMINISTRADOR Y GESTIÓN DE CONFIGURACIÓN DE LA APLICACIÓN**                                                                                 |                 |
| FR-70 | El superadministrador podrá modificar el plazo de pago estándar (5 días)                                                                                       | UR-05           |
| FR-71 | El superadministrador podrá configurar la periodicidad y el calendario de recordatorios e impagos                                                             | UR-05           |
| FR-72 | El superadministrador podrá configurar los rangos de cuotas de los clientes                                                                                     | UR-05           |
| FR-73 | El superadministrador podrá configurar los periodos de conservación y borrado de datos                                                                        | UR-05           |
| FR-74 | El superadministrador podrá gestionar los permisos y roles de los demás usuarios, incluidos los administradores                                                | UR-05           |
| FR-75 | El superadministrador podrá acceder a las copias de seguridad y registros de actividad                                                                        | UR-05           |
| FR-76 | El superadministrador podrá modificar la plantilla estándar de las facturas                                                                                   | UR-05           |
| FR-77 | El superadministrador podrá configurar las series numéricas                                                                                                     | UR-05           |
|       | **DASHBOARD E INFORMES**                                                                                                                                        |                 |
| FR-78 | El sistema ofrecerá un dashboard con informes de facturación semanales, mensuales y anuales                                                                    | UR-09           |
| FR-79 | El dashboard mostrará importes facturados, cobrados, pendientes e impagados                                                                                     | UR-09           |
| FR-80 | El acceso al dashboard se restringirá al rol administrador (lectores no incluidos)                                                                             | UR-03, UR-09    |
|       | **TRAZABILIDAD, COPIAS DE SEGURIDAD Y API**                                                                                                                     |                 |
| FR-81 | El sistema mantendrá un historial de modificaciones de clientes y facturas (quién, cuándo y qué cambió)                                                       | UR-07           |
| FR-82 | El sistema mantendrá un historial de quién consultó qué facturas                                                                                                | UR-07           |
| FR-83 | El sistema registrará los permisos concedidos a cada usuario                                                                                                   | UR-03, UR-05    |
| FR-84 | El sistema generará copias de seguridad periódicas de todos los movimientos (pagos, clientes, permisos, accesos)                                              | UR-05           |
| FR-85 | El sistema expondrá una API para consultar si un cliente tiene facturas pendientes                                                                             | UR-02           |
| FR-86 | El sistema podrá comunicarse con otros sistemas mediante dicha API                                                                                              | UR-02           |
| FR-87 | La API será extensible para futuras consultas externas, por ejemplo desde BrainOS                                                                               | UR-05           |
| FR-88 | El sistema contará con documentación de uso y técnica                                                                                                           | UR-05           |

### 4.2. Requisitos no funcionales

> Los requisitos no funcionales describen propiedades de calidad, restricciones o condiciones que debe satisfacer el sistema. Cuando sea posible, deberán incluir algún criterio que permita determinar si el requisito se cumple.

| ID     | Categoría                 | Requisito                                                                                                                      | Criterio verificable                                                                                                        |
| ------ | ------------------------- | ------------------------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- |
| NFR-01 | Usabilidad                | Las operaciones habituales (crear cliente, crear factura, consultar estado) serán rápidas                                      | Una operación habitual como generar o consultar una factura debería poder completarse aproximadamente en menos de un minuto |
| NFR-02 | Usabilidad                | Se minimizará el número de clics en la interfaz                                                                                | Las flujos de trabajo principales deben completarse en la menor cantidad de pasos/pantallas posible                         |
| NFR-03 | Usabilidad                | La interfaz será simple y no exigirá aprendizaje previo                                                                        | Un usuario nuevo sin formación previa debe poder realizar tareas básicas de forma intuitiva desde el primer uso              |
| NFR-04 | Portabilidad              | Aplicación web responsive utilizable desde móvil, tableta y ordenador                                                          | Las principales funcionalidades deberán ser utilizables y adaptarse visualmente en ordenador, tableta y teléfono móvil    |
| NFR-05 | Escalabilidad             | El sistema soportará miles de clientes y sus facturas                                                                          | El diseño no deberá asumir un número pequeño o fijo de clientes y deberá permitir gestionar potencialmente miles de ellos   |
| NFR-06 | Rendimiento               | Las consultas y operaciones interactivas responderán con rapidez                                                               | El tiempo de respuesta de las vistas principales y búsquedas en la interfaz debe ser inferior a 2 segundos                   |
| NFR-07 | Rendimiento               | La generación mensual masiva de facturas será eficiente                                                                        | El proceso batch automático de facturación mensual no debe degradar el rendimiento general ni bloquear el sistema        |
| NFR-08 | Fiabilidad                | Los procesos automáticos (facturación, consulta de cobros, recordatorios) serán fiables                                        | Ejecución de los procesos automáticos sin pérdida de ejecuciones ni errores no gestionados                                  |
| NFR-09 | Fiabilidad                | La información fluirá sin pérdidas hasta la persona correcta                                                                   | Garantizar la entrega de notificaciones a los destinatarios configurados                                                    |
| NFR-10 | Seguridad                 | El acceso a datos y funciones se controlará por rol y por clientes asignados                                                   | El sistema deberá proteger la confidencialidad mediante controles de acceso estrictos por rol y asignación                |
| NFR-11 | Seguridad                 | Los datos y contraseñas se protegerán con cifrado                                                                              | Las contraseñas deben estar hasheadas (e.g. bcrypt/Argon2) y los datos sensibles/comunicaciones cifrados (TLS, etc.)       |
| NFR-12 | Seguridad                 | Uso exclusivamente interno, sin acceso de clientes                                                                             | Restricción total de acceso externo a clientes y verificación de dominio de correo corporativo                              |
| NFR-13 | Legal                     | Se cumplirá la normativa de protección de datos (RGPD) para datos personales y fiscales                                        | Cumplimiento de la normativa de privacidad y protección de datos vigentes                                                   |
| NFR-14 | Legal                     | Los datos de clientes se conservarán el máximo que permita la ley                                                              | El diseño deberá poder guardar y preservar los datos de un usuario hasta 5 años                                            |
| NFR-15 | Legal                     | Las facturas serán estandarizadas y se protegerá su integridad tras el cierre del trimestre                                    | Inmutabilidad de facturas emitida tras el cierre de su trimestre correspondiente                                            |
| NFR-16 | Mantenibilidad            | La arquitectura será modular para facilitar ampliaciones                                                                       | El diseño software utilizará arquitectura por módulos independientes e intercambiables                                      |
| NFR-17 | Mantenibilidad            | La API será estándar y las integraciones estarán desacopladas                                                                  | Implementación de API siguiendo estándares (REST/OpenAPI) con desacoplamiento total                                         |
| NFR-18 | Mantenibilidad            | El sistema estará documentado para uso humano y por agentes de IA                                                              | Existencia de documentación clara de código, API y usuario final                                                            |
| NFR-19 | Restricciones Técnicas    | Aplicación web independiente de BrainOS, con base de datos propia externa a la de Turbine y, preferentemente, software libre   | Despliegue en base de datos e infraestructura independiente respetando el uso de licencias de software libre               |
| NFR-20 | Trazabilidad/Recuperación | Los registros de actividad serán inalterables y consultables                                                                   | Registro de auditoría (logs) en almacenamiento de solo lectura/inalterables                                                 |
| NFR-21 | Trazabilidad/Recuperación | Se podrán restaurar los datos desde copias de seguridad periódicas                                                              | Existencia de copias de seguridad periódicas comprobadas para restauración de desastres                                     |


## 5. Reglas de negocio

> Reglas del dominio o de la organización que condicionan el comportamiento del sistema pero que no representan por sí mismas una funcionalidad.

| ID    | Regla                                                                                                                                                   |
| ----- | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| RB-01 | Solo se permite el inicio de sesión a usuarios con cuentas de correo de la empresa                                                                      |
| RB-02 | No se permite autorregistro ni acceso a clientes externos                                                                                               |
| RB-03 | Una factura emitida no se puede borrar. Si no se puede modificar por motivos legales o ha cerrado el trimestre, debe emitirse una factura rectificativa |
| RB-04 | La solicitud de baja de un cliente cancela la generación de nuevas facturas recurrentes, pero las facturas impagadas siguen pendientes                  |
| RB-05 | La tarifa mensual de suscripción recurrente viene determinada por el rango de facturación anual del cliente                                             |
| RB-06 | Si un cliente se da de alta en un día distinto al día 1 del mes, el primer cobro se calculará proporcionalmente a los días restantes de dicho mes       |
| RB-07 | Un usuario lector solo puede ver y editar los clientes que le han sido asignados por un administrador                                                   |


## 6. Suposiciones y decisiones de análisis

> En esta sección se documentarán las decisiones tomadas por el equipo cuando la información proporcionada por el cliente no sea suficiente para determinar unívocamente un requisito.

| ID   | Suposición o decisión                                                                                        | Justificación                                                                                                    | Requisitos afectados       |
| ---- | ------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------- | -------------------------- |
| A-01 | Las pasarelas de pago (Stripe/Revolut) se implementarán mediante módulos simulados                           | El cliente indicó que conectar proveedores reales queda fuera del alcance actual de la aplicación                | FR-42, FR-43, FR-51, FR-52 |
| A-02 | El plazo por defecto de conservación de datos es de 5 años.                                                  | Cumplimiento de las obligaciones legales                                                                         | FR-12, NFR-08              |
| A-03 | La revisión de rangos de facturación anual requerirá confirmación humana de un administrador                 | Evitar cambios automáticos no deseados en la facturación debido a posibles errores en la información del cliente | FR-39, FR-41               |
| A-04 | Para calcular el importe de las altas a mitad de mes, se utilizará el número de días reales del mes en curso | Más preciso que una simplificación genérica de 30 días por mes                                                   | FR-23                      |


## 7. Dudas, ambigüedades y cuestiones pendientes

> Se incluirán aquí aspectos de la información proporcionada por el cliente que sean ambiguos, incompletos, contradictorios o que requieran aclaración.

| ID   | Cuestión                                                                                 | Origen     | Impacto                                               | Estado / resolución                                                                                                             |
| ---- | ---------------------------------------------------------------------------------------- | ---------- | ----------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------- |
| Q-01 | ¿Se requiere integración con el sistema Verifactu de la Agencia Tributaria?              | Entrevista | Complejidad técnica y legal en generación de facturas | No se incluye Verifactu en el alcance; las reglas legales complejas pasan por gestoría externa                                  |
| Q-02 | ¿Existe diferenciación entre rol Administrador y Lector?                                 | Entrevista | Definición de roles y permisos                        | Sí. El Administrador gestiona todo el sistema y asignaciones, mientras que el Lector gestiona únicamente los clientes asignados |
| Q-03 | ¿Se requiere implementar autenticación de doble factor (2FA)?                            | Entrevista | Seguridad de autenticación                            | No es obligatorio para la versión inicial, pero el diseño debe permitir su incorporación futura                                 |
| Q-04 | ¿Los clientes de Turbine tendrán acceso a la plataforma para pagar o consultar facturas? | Entrevista | Alcance del sistema y seguridad                       | No. La plataforma es de uso exclusivamente interno para el personal de Turbine                                                  |


## 8. Trazabilidad

> La tabla permitirá comprobar la relación entre las necesidades de negocio, las reglas de negocio, los requisitos de usuario y los requisitos del sistema.

| Requisito de negocio | Regla de negocio | Requisito de usuario | Requisito funcional / no funcional                               |
| -------------------- | ---------------- | -------------------- | ---------------------------------------------------------------- |
| BR-01                | RB-01, RB-02     | UR-01, UR-04, UR-06  | FR-01, FR-17, FR-18, FR-56, NFR-11                               |
| BR-02                | RB-05, RB-06     | UR-01, UR-07, UR-08  | FR-16, FR-22, FR-23, FR-24, FR-36, NFR-01                        |
| BR-03                | RB-03, RB-04     | UR-01, UR-02         | FR-21, FR-39, FR-40, FR-42, FR-44, FR-48, FR-49, FR-50           |
| BR-04                | RB-02, RB-03     | UR-01, UR-05         | FR-19, FR-25, FR-30, FR-31, NFR-05, NFR-09                       |
| BR-05                | RB-03, RB-04     | UR-02, UR-07         | FR-55, FR-81, FR-82, NFR-12                                      |
| BR-06                | x                | UR-09                | FR-78, FR-79, FR-80                                              |
| BR-07                | RB-01, RB-07     | UR-03, UR-05         | FR-57, FR-58, FR-59, FR-64, FR-66, FR-85, NFR-03, NFR-06, NFR-10 |


## 9. Glosario

> Definiciones de términos más técnicos usados en la documentación.

| Término                   | Definición                                                                                                                                                    |
| ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Factura recurrente**    | Factura generada y emitida automáticamente mensualmente calculada con la cuota de suscripción del cliente                          |
| **Factura puntual**       | Factura emitida de forma aislada para cobrar un servicio específico o adicional                                  |
| **Factura rectificativa** | Factura emitida para corregir o anular una factura previamente emitida que no puede ser modificada directamente por temas legales         |
| **Borrado lógico**        | Marcar un registro como inactivo o dado de baja en la base de datos sin eliminar físicamente sus datos, garantizando la conservación histórica por motivos legales
