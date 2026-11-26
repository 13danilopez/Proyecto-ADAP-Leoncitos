
# Especificación de casos de uso

## Índice

| Identificador | Nombre                                           |
| ------------- | ------------------------------------------------ |
| **CU-01**     | Autenticación de usuario                         |
| **CU-02**     | Consulta del historial de facturas               |
| **CU-03**     | Generación de facturas                           |
| **CU-04**     | Modificación de preferencias de pagos            |
| **CU-05**     | Consulta de documentación de uso del sistema     |
| **CU-06**     | Acceso al dashboard de facturación               |
| **CU-07**     | Gestión de usuarios del sistema                  |
| **CU-08**     | Gestión de clientes                              |
| **CU-09**     | Consulta de historial de modificaciones          |
| **CU-10**     | Modificación de configuración global del sistema |

---

## CU-01 - Autenticación de usuario

### 1. Identificación

| Campo                     | Descripción                                                                                                                                               |
| ------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Identificador**         | CU-01                                                                                                                                                     |
| **Nombre**                | Autenticación de usuario                                                                                                                                  |
| **Objetivo**              | Proceso que sigue un usuario del sistema para autenticarse (iniciar sesión) con su correo corporativo y contraseña proporcionada para acceder al sistema. |
| **Actores participantes** | Cualquier usuario del sistema (Lector, Administrador, Superadministrador).                                                                                |

---

### 2. Precondiciones

- **PRE-1**: El usuario está registrado en el sistema.
- **PRE-2**: El usuario posee su correo corporativo y su contraseña proporcionada por un admin.

---

### 3. Flujo principal

1. **Actor:** usuario solicita iniciar sesión en el sistema.
2. **Sistema:** muestra los campos a introducir para el inicio de sesión: correo del dominio de TurbineH y contraseña personal.
3. **Actor:** usuario introduce los datos de su cuenta.
4. **Sistema:** confirma los datos del usuario en la base de usuarios registrados y le concede acceso al sistema.

---

### 4. Flujos alternativos

No se contemplan flujos alternativos para este caso de uso. El usuario sólo puede acceder al sistema únicamente a través del proceso de autenticación impuesto.

---

### 5. Flujos de excepción

#### FE-1 — Fallo de autenticación

- **Se produce en:** pasos 3-4 del flujo principal.
- **Condición**: el usuario introduce sus datos de forma errónea.

1. **Actor**: está registrado pero introduce sus datos erróneos, ó resulta que directamente no está registrado en el sistema.
2. **Sistema**: comprueba si los datos introducidos coinciden con los de algún usuario ya registrado en la base de datos.
3. **Sistema**: no encuentra coincidencia.
4. **Sistema**: limpia el campo de la contraseña y deja el correo como se había introducido.
5. **Sistema**: muestra un mensaje de error "Usuario o contraseña incorrectos" y permite al usuario autenticarse de nuevo.

- **Resultado:** se registra un intento de autenticación fallido en el sistema y el usuario puede volver a intentar autenticarse.

---

### 6. Postcondiciones

- **POST-1**: Se le autoriza el acceso al sistema al usuario.
- **POST-2**: El acceso del usuario queda registrado en los logs del sistema.

---

## CU-02 - Consulta del historial de facturas

### 1. Identificación

| Campo                     | Descripción                                                                                   |
| ------------------------- | --------------------------------------------------------------------------------------------- |
| **Identificador**         | CU-02                                                                                         |
| **Nombre**                | Consulta del historial de facturas                                                            |
| **Objetivo**              | El usuario quiere realizar consultas a través del historial de facturas del sistema.          |
| **Actores participantes** | Cualquier usuario del sistema (Lector, Administrador, Superadministrador) y Entidad Bancaria. |

---

### 2. Precondiciones

- **PRE-1**: El usuario está registrado en el sistema.
- **PRE-2**: El usuario ha iniciado sesión (identifiación-autenticación-autorización).
- **PRE-3**: El cliente está registrado en el sistema.

---

### 3. Flujo principal

1. **Actor:** accede al perfil del cliente del que quiere consultar su historial de facturas.
2. **Sistema:** muestra al usuario el perfil del cliente.
3. **Actor:** accede al historial de facturas del clientes a través de una opción en el menú. 
4. **Sistema:** accede a las facturas asignadas al cliente concreto a través de una consulta a la base de datos del sistema.
5. **Sistema**: muestra al usuario el historial de facturas del cliente. En caso de no tener facturas registradas, se muestra el historial vacío.
6. **Actor**: accede a las facturas listadas.

---

### 4. Flujos alternativos

#### FA-1 — Consulta del historial de facturas global

- **Se produce en:** paso 1 del flujo principal.
- **Condición:** el usuario quiere acceder a las facturas de todos sus usuarios asignados.

1. **Actor**: accede al historial global (conjunto de clientes asignados) desde su menú principal.
2. **Sistema**: accede a las facturas de todos los clientes asignados al usuario a través de una consulta a la base de datos del sistema.
3. **Sistema**: muestra al usuario el historial de facturas global.

- **Continuación:** el caso de uso continúa en el paso 6 del flujo principal.

#### FA-2 — Descarga de facturas

- **Se produce en:** paso 6 del flujo principal.
- **Condición:** el usuario quiere descargar la factura.

1. **Actor**: hace click en la opción de descarga dentro del menú visualizador de la factura.
2. **Sistema**: exporta los datos de la factura a un formato PDF estandarizado.
3. **Sistema**: inicia una descarga del PDF en el sistema del usuario.

- **Continuación:** es una ampliación desde el último paso del caso de uso.

#### FA-3 — Filtrado de facturas

- **Se produce en:** paso 5 del flujo principal.
- **Condición:** el usuario quiere filtrar las facturas del cliente.

1. **Actor**: filtra los datos en el buscar en función de una serie de categorías: estado (pagada, pendiente, impagada), tipo (recurrente, puntual) y fecha.
2. **Sistema**: muestra las facturas acordes con las categorías indicadas.

- **Continuación:** el caso de uso continúa en el paso 6 del flujo principal.

#### FA-4 — Comprobación de estado de pago

- **Se produce en:** paso 6 del flujo principal.
- **Condición:** el usuario quiere comprobar el estado de pago de la factura.

1. **Sistema**: hace una consulta rápidamente a la entidad bancaria con la que se registró dicha factura.
2. **Sistema**: muestra el estado actual del pago de la factura en un recuadro informativo.

- **Continuación:** es una ampliación desde el último paso del caso de uso.

---

### 5. Flujos de excepción

#### FE-1 — Consulta de base de datos errónea

- **Se produce en:** paso 4 del flujo principal.  
- **Condición:** el cliente que se está buscando en la base de datos no se encuentra.

1. **Sistema**: busca las facturas asignadas al cliente concreto a través de una consulta a la base de datos del sistema.
2. **Sistema**: no existe el cliente indicado en la base de datos.
3. **Sistema**: muestra al usuario el historial de facturas vacío junto a un mensaje indicando el error en la consulta.

- **Resultado:** se registra un error de consulta de la base de datos en el sistema y se notifica a los administradores.

---

### 6. Postcondiciones

- **POST-1**: El acceso al historial de facturas y las distintas interacciones con ella (descarga y consulta de estado de pago) por parte del usuario queda registrado en los logs del sistema.

---

## CU-03 - Generación de facturas

### 1. Identificación

| Campo                     | Descripción                                                                                                            |
| ------------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| **Identificador**         | CU-03                                                                                                                  |
| **Nombre**                | Generación de facturas                                                                                                 |
| **Objetivo**              | El usuario quiere generar una nueva factura para un cliente determinado.                                               |
| **Actores participantes** | Cualquier usuario del sistema (Lector, Administrador, Superadministrador) y el Sistema Automatizado de Notificaciones. |

---

### 2. Precondiciones

- **PRE-1**: El usuario está registrado en el sistema.
- **PRE-2**: El usuario ha iniciado sesión (identifiación-autenticación-autorización).
- **PRE-3**: El cliente está registrado en el sistema.

---

### 3. Flujo principal

1. **Actor:** accede al perfil del cliente del cual quiere generar una nueva factura.
2. **Sistema:** muestra al usuario el perfil del cliente.
3. **Actor:** accede a la función de creación de una nueva factura mediante el menú.
4. **Sistema:** muestra al usuario el generador de facturas.
5. **Sistema**: autocompleta los datos básicos de la factura respectivos al cliente (ej: datos personales).
6. **Actor**: puede rellenar/seleccionar el resto de datos pertinentes de la factura (ej: tipo de factura, factura rectificadora, importe de la factura...).
7. **Sistema**: mantiene periódicamente la factura que está siendo editada como borrador.
8. **Actor**: completa y emite la factura.
9. **Sistema**: registra la factura emitida en la base de datos y elimina el borrador.
10. **Sistema**: informa al sistema automatizado de notificaciones para que notifique al cliente de su nueva factura.
11. **Sistema de notificaciones**: notificará de forma automática periódicamente al cliente respecto a la nueva factura emitida, ya sea por pago o por impago.

---

### 4. Flujos alternativos

#### FA-1 — Factura incompleta no emitida

- **Se produce en:** paso 7 del flujo principal.
- **Condición:** el usuario no termina de rellenar los datos de la factura por lo que no la emite.

1. **Actor**: cierra la factura que estaba creando sin haberla completado/emitido.
2. **Sistema**: guarda de nuevo las últimas modificaciones como borrador.
3. **Actor**: vuelve a acceder al generador de facturas para ese mismo cliente.
4. **Sistema**: carga y muestra el estado en el que se quedó la factura tras el útlimo borrador.

- **Continuación:** el caso de uso continúa en el paso 8 del flujo principal.

#### FA-2 — Factura rectificadora

- **Se produce en:** paso 6 del flujo principal.
- **Condición:** se quiere crear una factura rectificadora.

1. **Actor**: indica el tipo de factura como "Rectificadora".
2. **Actor**: indica cuál es la factura que está rectificando.
3. **Sistema**: autocompleta los datos con los de la factura que está siendo rectificada.
4. **Sistema**: se asegura de asignarle al número de serie un sufijo indicador del la rectificación actual (ej: 12345678 -> 12345678-1).
5. **Actor**: rectifica los datos que sean necesarios permitidos (legalmente modificables).

- **Continuación:** el caso de uso continúa en el paso 8 del flujo principal.

---

### 5. Flujos de excepción

#### FE-1 — Emisión de factura fallida

- **Se produce en:** paso 9 del flujo principal.
- **Condición:** el sistema no puede guardar la factura creada en la base de datos.

1. **Sistema**: intenta asignar la nueva factura al cliente en la base de datos.
2. **Sistema**: se da algún tipo de error al intentar acceder/guardar los datos.
3. **Sistema**: cancela la emisión de la factura y no comunica con el sistema notificador de pagos.
4. **Sistema**: notifica al usuario un mensaje de error indicando que la emisión de la factura ha sido cancelada por un error.

- **Resultado:** se registra un error de registro de factura en la base de datos en el sistema y se notifica a los administradores.

---

### 6. Postcondiciones

- **POST-1**: El historial de facturas del cliente queda actualizado en la base de datos con la nueva factura.
- **POST-2**: El sistema de notificaciones notificará automáticamente al cliente respecto a la nueva factura emitida.
- **POST-3**: La emisión de la factura queda registrada en los logs del sistema.

---

## CU-04 - Modificación de preferencias de pagos

### 1. Identificación

| Campo                     | Descripción                                                                                   |
| ------------------------- | --------------------------------------------------------------------------------------------- |
| **Identificador**         | CU-04                                                                                         |
| **Nombre**                | Modificación de preferencias de pagos                                                         |
| **Objetivo**              | Capacidad de los usuarios del sistema para configurar las preferencias de pago de un cliente. |
| **Actores participantes** | Cualquier usuario del sistema (Lector, Administrador, Superadministrador).                    |

---

### 2. Precondiciones

- **PRE-1**: El usuario está registrado en el sistema.
- **PRE-2**: El usuario ha iniciado sesión (identifiación-autenticación-autorización).
- **PRE-3**: El cliente está registrado en el sistema.

---

### 3. Flujo principal

1. **Actor**: accede a su apartado de clientes asignados desde su menú principal.
2. **Sistema**: muestra al usuario su listado de clientes.
3. **Actor**: hace click en la opción "Configurar preferencias de pago" del cliente.
4. **Sistema**: muestra al usuario una ventana con esa única característica configurable del cliente.
5. **Actor**: modifica las preferencias de pago del cliente.
6. **Actor**: guarda los cambios.
7. **Sistema**: actualiza los datos del cliente respectivamente.

---

### 4. Flujos alternativos

No se contemplan flujos alternativos para este caso de uso.

---

### 5. Flujos de excepción

No se contemplan flujos de excepción para este caso de uso.

---

### 6. Postcondiciones

- **POST-1**: Las preferencias de pago del cliente modificado quedan actualizadas.
- **POST-2**: La modificación de las preferencias de pago de un cliente por parte de un usuario quedará registrada en el log del sistema.

---

## CU-05 - Consulta de documentación de uso del sistema

### 1. Identificación

| Campo                     | Descripción                                                                                            |
| ------------------------- | ------------------------------------------------------------------------------------------------------ |
| **Identificador**         | CU-05                                                                                                  |
| **Nombre**                | Consulta de documentación de uso del sistema                                                           |
| **Objetivo**              | El usuario tiene dudas sobre el funcionamiento del sistema y quiere consultar la documentación de uso. |
| **Actores participantes** | Cualquier usuario del sistema (Lector, Administrador, Superadministrador).                             |

---

### 2. Precondiciones

- **PRE-1**: El usuario está registrado en el sistema.
- **PRE-2**: El usuario ha iniciado sesión (identifiación-autenticación-autorización).

---

### 3. Flujo principal

1. **Actor**: accede a la pestaña de "Documentación de Uso del sistema" a través de su menú principal.
2. **Sistema**: muestra la Documentación de Uso al usuario.

---

### 4. Flujos alternativos

No se contemplan flujos alternativos para este caso de uso.

---

### 5. Flujos de excepción

No se contemplan flujos de excepción para este caso de uso.

---

### 6. Postcondiciones

El sistema no se ve alterado tras las acciones dadas en este caso de uso.

---

## CU-06 - Acceso al dashboard de facturación

### 1. Identificación

| Campo                     | Descripción                                                                                                                      |
| ------------------------- | -------------------------------------------------------------------------------------------------------------------------------- |
| **Identificador**         | CU-06                                                                                                                            |
| **Nombre**                | Acceso al dashboard de facturación                                                                                               |
| **Objetivo**              | El usuario privilegiado quiere consultar información relevante a la facturación de TurbineH a través del dashboard implementado. |
| **Actores participantes** | Administradores y Superadministrador.                                                                                            |

---

### 2. Precondiciones

- **PRE-1**: El usuario está registrado en el sistema.
- **PRE-2**: El usuario ha iniciado sesión (identifiación-autenticación-autorización).
- **PRE-3**: El usuario tiene permisos de acceso al dashboard.

---

### 3. Flujo principal

1. **Actor:** accede al dashboard desde su menú principal.
2. **Sistema**: muestra un report de los datos financieros en los últimos días, semanas, meses o año.

---

### 4. Flujos alternativos

No se contemplan flujos alternativos para este caso de uso.

---

### 5. Flujos de excepción

No se contemplan flujos de excepción para este caso de uso.

---

### 6. Postcondiciones

- **POST-1**: El acceso al dashboard por parte del usuario queda registrado en los logs del sistema.

---

## CU-07 - Gestión de usuarios del sistema

### 1. Identificación

| Campo                     | Descripción                                                                        |
| ------------------------- | ---------------------------------------------------------------------------------- |
| **Identificador**         | CU-07                                                                              |
| **Nombre**                | Gestión de usuarios del sistema                                                    |
| **Objetivo**              | El usuario privilegiado quiere registrar y/o gestionar a un usuario en el sistema. |
| **Actores participantes** | Administradores y Superadministrador.                                              |

---

### 2. Precondiciones

- **PRE-1**: El usuario está registrado en el sistema.
- **PRE-2**: El usuario ha iniciado sesión (identifiación-autenticación-autorización).
- **PRE-3**: El usuario tiene permisos de gestión de usuarios.

---

### 3. Flujo principal

1. **Actor**: accede al buscador de usuarios registrados del sistema (acceso a todos, ya que es un usuario privilegiado).
2. **Sistema:** accede a todos los usuarios a través de una consulta a la base de datos del sistema.
3. **Sistema**: muestra al usuario privilegiado el listado de todos los usuarios.
4. **Actor**: busca/filtra el usuario que busca.
5. **Actor**: hace click en la opción de "Editar usuario".
6. **Sistema**: muestra los datos con los que el usuario aparece registrado en el sistema.
7. **Actor**: modifica los datos del usuario y/o sus clientes asignados.
8. **Actor**: guarda los cambios efectuados.
9. **Sistema**: accede a la base de datos del sistema y actualiza los datos del sistema.

---

### 4. Flujos alternativos

#### FA-1 — Creación de un nuevo usuario

- **Se produce en:** paso 1 del flujo principal.
- **Condición:** el usuario privilegiado no quiere editar un usuario existente sino que quiere registrar uno nuevo.

1. **Actor:** accede a la función de "Creacción de nuevo usuario" desde su menú principal.
2. **Sistema:** muestra al usuario el creador de usuarios.
3. **Actor**: puede rellenar los datos de registro del nuevo usuario (correo corporativo de TurbineH y contraseña) y asignarle directamente una lista de clientes.
4. **Actor**: completa los campos y crea el nuevo usuario.
5. **Sistema**: registra al nuevo usuario en el sistema y lo guarda en la base de datos.

- **Continuación:** se sigue un flujo completamente distinto/independiente al original.

---

### 5. Flujos de excepción

#### FE-1 — Modificación de usuario fallida

- **Se produce en:** paso 8 del flujo principal.
- **Condición:** el sistema no logra actualizar correctamente los datos del usuario modificado.

1. **Sistema**: trata de actualizar la base de datos del sistema pero se produce un error.
2. **Sistema**: cancela la acción.
3. **Sistema**: notifica al usuario privilegiado con un mensaje de error indicando que la creación del usuario se ha cancelado.

- **Resultado:** se registra un error de actualización de datos de usuario en el sistema y se notifica a los administradores.

---

### 6. Postcondiciones

- **POST-1**: El nuevo usuario queda registrado en la base de datos del sistema.
- **POST-2**: La modificación de datos de un usuario por parte de un usuario privilegiado quedará registrada en el log del sistema.

---

## CU-08 - Gestión de clientes

### 1. Identificación

| Campo                     | Descripción                                                                        |
| ------------------------- | ---------------------------------------------------------------------------------- |
| **Identificador**         | CU-08                                                                              |
| **Nombre**                | Gestión de clientes                                                                |
| **Objetivo**              | El usuario privilegiado quiere registrar y/o gestionar a un cliente en el sistema. |
| **Actores participantes** | Administradores y Superadministrador.                                              |

---

### 2. Precondiciones

- **PRE-1**: El usuario está registrado en el sistema.
- **PRE-2**: El usuario ha iniciado sesión (identifiación-autenticación-autorización).
- **PRE-3**: El usuario tiene permisos de gestión de clientes.

---

### 3. Flujo principal

1. **Actor**: accede al buscador de clientes registrados del sistema (acceso a todos, ya que es un usuario privilegiado).
2. **Sistema:** accede a todos los clientes a través de una consulta a la base de datos del sistema.
3. **Sistema**: muestra al usuario el listado de todos los clientes.
4. **Actor**: busca/filtra el cliente que busca y accede a su perfil.
5. **Actor**: hace click en la opción de "Editar cliente" en el menú del cliente.
6. **Sistema**: muestra los datos con los que el cliente aparece registrado en el sistema.
7. **Actor**: modifica los datos del cliente y/o puede desactivarlo.
8. **Actor**: guarda los cambios efectuados.
9. **Sistema**: accede a la base de datos del sistema y actualiza los datos del sistema.

---

### 4. Flujos alternativos

#### FA-1 — Creación de un nuevo cliente

- **Se produce en:** paso 1 del flujo principal.
- **Condición:** el usuario no quiere editar un cliente existente sino que quiere registrar uno nuevo.

1. **Actor:** accede a la función de "Creacción de nuevo cliente" desde su menú principal.
2. **Sistema:** muestra al usuario el creador de clientes.
3. **Actor**: puede rellenar los datos del nuevo cliente manualmente.
4. **Actor**: confirma y crea el nuevo cliente.
5. **Sistema**: registra al nuevo cliente en el sistema y lo guarda en la base de datos.

- **Continuación:** se sigue un flujo completamente distinto/independiente al original.

#### FA-2 — Importación de clientes con archivo CSV

- **Se produce en:** paso 1 del flujo principal.
- **Condición:** el usuario no quiere editar un cliente existente sino que quiere registrar uno nuevo empleando un archivo CSV.

1. **Actor:** accede a la función de "Creacción de nuevo cliente" desde su menú principal.
2. **Sistema:** muestra al usuario el creador de clientes.
3. **Actor**: accede a la opción "Importar cliente con CSV".
4. **Actor**: selecciona el archivo CSV estandarizado con los datos del cliente a importar.
5. **Sistema**: extrae los contenidos del archivo CSV y rellena los campos automáticamente.
6. **Actor**: confirma y crea el nuevo cliente.
7. **Sistema**: registra al nuevo cliente en el sistema y lo guarda en la base de datos.

- **Continuación:** se sigue un flujo completamente distinto/independiente al original.

---

### 5. Flujos de excepción

#### FE-1 — Modificación de cliente fallida

- **Se produce en:** paso 8 del flujo principal.
- **Condición:** el sistema no logra actualizar correctamente los datos del cliente modificado.

1. **Sistema**: trata de actualizar la base de datos del sistema pero se produce un error.
2. **Sistema**: cancela la acción.
3. **Sistema**: notifica al usuario privilegiado con un mensaje de error indicando que la creación del cliente se ha cancelado.

- **Resultado:** se registra un error de actualización de datos de cliente en el sistema y se notifica a los administradores.

#### FE-2 — Importación CSV fallida

- **Se produce en:** paso 4 del flujo alternativo FA-2.
- **Condición:** el formato del archivo CSV proporcionado es incorrecto.

1. **Sistema**: trata de extraer los datos del cliente del archivo CSV pero hay errores en el formato.
2. **Sistema**: cancela la importación de datos.
3. **Sistema**: notifica al usuario privilegiado con un mensaje de error indicando que el formato del archivo CSV es incorrecto.

- **Resultado:** el usuario puede revisar el CSV y intentar volver a importarlo.

---

### 6. Postcondiciones

- **POST-1**: El nuevo cliente queda registrado en la base de datos del sistema.
- **POST-2**: La modificación de datos de un cliente por parte de un usuario privilegiado quedará registrada en el log del sistema.

---

## CU-09 - Consulta de historial de modificaciones

### 1. Identificación

| Campo                     | Descripción                                                                                 |
| ------------------------- | ------------------------------------------------------------------------------------------- |
| **Identificador**         | CU-09                                                                                       |
| **Nombre**                | Consulta de historial de modificaciones                                                     |
| **Objetivo**              | El usuario privilegiado quiere consultar el historial de modificaciones del sistema (logs). |
| **Actores participantes** | Administradores y Superadministrador.                                                       |

---

### 2. Precondiciones

- **PRE-1**: El usuario está registrado en el sistema.
- **PRE-2**: El usuario ha iniciado sesión (identifiación-autenticación-autorización).
- **PRE-3**: El usuario tiene permisos de acceso al historial de modificaciones.

---

### 3. Flujo principal

1. **Actor:** accede al historial de modificaciones desde su menú principal.
2. **Sistema**: muestra un report de todas las modificaciones en el sistema ordenadas en orden cronológico.
3. **Actor**: puede filtrarlas según el usuario que la efectuó y por rangos de fechas.

---

### 4. Flujos alternativos

No se contemplan flujos alternativos para este caso de uso.

---

### 5. Flujos de excepción

No se contemplan flujos de excepción para este caso de uso.

---

### 6. Postcondiciones

- **POST-1**: El acceso al historial de modificaciones por parte del usuario privilegiado queda registrado en el propio log del sistema.

---

## CU-10 - Modificación de configuración global del sistema

### 1. Identificación

| Campo                     | Descripción                                                                                                                      |
| ------------------------- | -------------------------------------------------------------------------------------------------------------------------------- |
| **Identificador**         | CU-10                                                                                                                            |
| **Nombre**                | Modificación de configuración global del sistema                                                                                 |
| **Objetivo**              | El superadministrador quiere modificar aspectos configurables del sistema (definidos en los requisitos funcionales del sistema). |
| **Actores participantes** | Superadministrador.                                                                                                              |

---

### 2. Precondiciones

- **PRE-1**: El usuario está registrado en el sistema.
- **PRE-2**: El usuario ha iniciado sesión (identifiación-autenticación-autorización).
- **PRE-3**: El usuario tiene el máximo nivel de privilegio.

---

### 3. Flujo principal

1. **Actor**: accede al apartado de configuración global desde su menú principal.
2. **Sistema:** muestra todos los aspectos configurables del sistema.
3. **Actor**: puede modificarlos según su criterio.
4. **Actor**: guarda los cambios efectuados.
5. **Sistema**: pausa todas las acciones en el sistema hasta que todos los datos que se puedan ver afectados por la nueva configuración se hayan actualizado (ej: modificación de series numéricas de las facturas).
6. **Sistema**: rehabilita el sistema para los demás usuarios.

---

### 4. Flujos alternativos

No se contemplan flujos alternativos para este caso de uso.

---

### 5. Flujos de excepción

#### FE-1 — Actualización de configuración fallida

- **Se produce en:** paso 5 del flujo principal.
- **Condición:** se produce algún error que no permite la actualización correcta de todos los datos del sistema.

1. **Sistema**: cancela la acción y restaura todos los cambios.
2. **Sistema**: notifica al superadministrador con un mensaje de error indicando que la nueva configuración no ha podido aplicarse e indica en qué ha fallado.

- **Resultado:** El proceso de actualización se aborta y la configuración original se restaura.

---

### 6. Postcondiciones

- **POST-1**: Los nuevos cambios son aplicados.
- **POST-2**: Los datos del sistema se han actualizado acorde con la nueva configuración.