
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
| ...   | ...                                                                                        | ...                  |


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
| ...   | ...                     | ...                                                                                                            | ...                  |


## 4. Requisitos del sistema

### 4.1. Requisitos funcionales

> Los requisitos funcionales describen comportamientos, servicios u operaciones que debe proporcionar el sistema. Siempre que sea posible, deben formularse de manera clara, precisa y verificable.

| ID    | Requisito                                                                                                                                                       | Relacionado con |
| ----- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------- |
|       | **GESTIÓN DE DATOS**                                                                                                                                            |                 |
| FR-01 | Almacenar a los clientes con sus datos necesarios (razón social, nombre de empresa, país, contacto, ...)                                                        | UR-...          |
| FR-02 | Poder retirar de la base de datos a clientes registrados                                                                                                        | UR-...          |
| FR-03 | Tener un historial de las facturas anteriores de cada cliente                                                                                                   | UR-...          |
|       | ...                                                                                                                                                             |                 |
|       | **SISTEMA DE NOTIFICACIÓN AL USUARIO**                                                                                                                          |                 |
| FR-04 | Notificar al cliente recordando que tiene que pagar su suscripción al 3er y 5o día de confirmarlo                                                               | UR-...          |
| FR-05 | Notificar al cliente de su impago tras el 5o día, con una frecuencia de 2-3 días                                                                                | UR-...          |
| FR-06 | Notificaciones vía correo electrónico                                                                                                                           | UR-...          |
|       | ...                                                                                                                                                             |                 |
|       | **SISTEMA DE LOG-IN DE EMPLEADOS**                                                                                                                              |                 |
| FR-07 | Sistema de Log-in con correo corporativo y contraseña proporcionados por TurbineH                                                                               |                 |
|       | ...                                                                                                                                                             |                 |
|       | **FUNCIONES DE LECTORES**                                                                                                                                       |                 |
| FR-08 | Lectores deben poder generar facturas de una forma semi-automática, haciendo uso de una plantilla preestablecida                                                |                 |
| FR-09 | Lectores deben tener acceso a todos los datos relevantes de sus clientes asignados (información de entidad, suscripciones actuales, historial de facturas, ...) |                 |
| FR-10 | Lectores deben poder modificar los plazos y los métodos de pago                                                                                                 |                 |
| FR-11 | Lectores deben tener acceso al panel dashboard de informes de facturación                                                                                       |                 |
|       | ...                                                                                                                                                             |                 |
|       | **FUNCIONES DE ADMINISTRADORES**                                                                                                                                |                 |
| FR-12 | Administradores pueden generar perfiles de nuevos clientes                                                                                                      |                 |
| FR-13 | Administradores pueden generar y gestionar facturas asignadas de los perfiles de usuarios lectores                                                              |                 |
|       | ...                                                                                                                                                             |                 |
|       | **FUNCIONES DEL SUPERADMINISTRADOR**                                                                                                                            |                 |
| FR-14 | Superadministrador tiene acceso a opciones de configuración a nivel del sistema (máximo nivel de privilegios)                                                   |                 |
| ...   | ...                                                                                                                                                             | ...             |


### 4.2. Requisitos no funcionales

> Los requisitos no funcionales describen propiedades de calidad, restricciones o condiciones que debe satisfacer el sistema. Cuando sea posible, deberán incluir algún criterio que permita determinar si el requisito se cumple.

| ID     | Categoría                 | Requisito                                                                                                                      | Criterio verificable                                                                                                        |
| ------ | ------------------------- | ------------------------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- |
| NFR-01 | Usabilidad                | Las operaciones administrativas habituales deberán poder realizarse de forma rápida y sencilla                                 | Una operación habitual como generar o consultar una factura debería poder completarse aproximadamente en menos de un minuto |
| NFR-02 | Portabilidad / Usabilidad | La aplicación deberá poder utilizarse desde distintos tipos de dispositivo                                                     | Las principales funcionalidades deberán ser utilizables desde ordenador, tableta y teléfono móvil                           |
| NFR-03 | Escalabilidad             | El sistema deberá poder gestionar un crecimiento significativo del número de clientes                                          | El diseño no deberá asumir un número pequeño o fijo de clientes y deberá permitir gestionar potencialmente miles de ellos   |
| NFR-04 | Seguridad                 | El sistema deberá proteger la confidencialidad de los datos personales, fiscales<br>y financieros mediante controles de acceso | ?                                                                                                                           |
| NFR-05 | Seguridad                 | El sistema deberá implementar un cifrado de los datos del usuario                                                              | ?                                                                                                                           |
| NFR-06 | Legal                     | Preservación a largo plazo de los datos de los usuarios                                                                        | El diseño deberá poder guardar los datos de un usuario hasta 5 años                                                         |
| NFR-07 | Usabilidad / Legal        | Las facturas generadas deben estar estandarizadas                                                                              | Las facturas generadas deben estar estandarizadas                                                                           |
| ...    | ...                       | ...                                                                                                                            | ...                                                                                                                         |


## 5. Reglas de negocio

> Reglas del dominio o de la organización que condicionan el comportamiento del sistema pero que no representan por sí mismas una funcionalidad.

| ID    | Regla |
| ----- | ----- |
| RB-01 | ...   |
| RB-02 | ...   |


## 6. Suposiciones y decisiones de análisis

> En esta sección se documentarán las decisiones tomadas por el equipo cuando la información proporcionada por el cliente no sea suficiente para determinar unívocamente un requisito.

| ID | Suposición o decisión | Justificación | Requisitos afectados |
|---|---|---|---|
| A-01 | ... | ... | FR-... |
| A-02 | ... | ... | ... |


## 7. Dudas, ambigüedades y cuestiones pendientes

> Se incluirán aquí aspectos de la información proporcionada por el cliente que sean ambiguos, incompletos, contradictorios o que requieran aclaración.

| ID | Cuestión | Origen | Impacto | Estado / resolución |
|---|---|---|---|---|
| Q-01 | Se hace distincion entre administrador y lector en la platadorma de pago? es decir, nosotros debemos tener en cuenta entre lector y administrador | Entrevista | ... | Pendiente |
| Q-02 | ... | ... | ... | Resuelta: ... |


## 8. Trazabilidad

> La tabla permitirá comprobar la relación entre las necesidades de negocio, los requisitos de usuario y los requisitos del sistema.

| Requisito de negocio | Requisito de usuario | Requisito funcional / no funcional |
|---|---|---|
| BR-01 | UR-01 | FR-01, FR-02 |
| BR-02 | UR-03 | FR-07, NFR-02 |


## 9. Glosario

> Definiciones de términos más técnicos usados en la documentación.

| Término            | Definición                                                                                                                                                   |
| ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Factura recurrente | Tipo de factura configurada para que se notifique periódicamente al usuario de su pago (en este caso, mensual). Se puede ver como una "suscripción mensual". |
| ...                | ...                                                                                                                                                          |
