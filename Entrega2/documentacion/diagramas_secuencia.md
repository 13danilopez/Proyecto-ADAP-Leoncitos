# Diagramas UML de secuencia

## Selección de escenarios

Para modelar el comportamiento dinámico de la plataforma interna de facturación y cobros de **TurbineH** (`LeoncitosProgram`), se han seleccionado los escenarios correspondientes a los **10 casos de uso especificados** (`CU-01` a `CU-10`), representados en los diagramas de secuencia del proyecto StarUML. Cada diagrama integra en un mismo flujo el **camino principal**, los **flujos alternativos/opcionales** (fragmentos `alt`, `opt` y `loop`) y el **manejo de excepciones y errores** (fallos de autenticación, validación de permisos, errores de acceso a base de datos y problemas en la actualización de la configuración global), garantizando la trazabilidad de auditoría (`LogActividad`) y la notificación de incidencias (`NotificacionInterna`).

## Descripción de escenarios

### CU-01 — Autenticación de usuario

1. El `Actor` solicita iniciar sesión (`solicitarInicioSesion()`) y `LeoncitosManager` le muestra los campos de correo corporativo y contraseña (`mostrarCampos(email, contraseña)`).

2. Dentro de un bucle (`loop`) hasta lograr autenticarse, el `Actor` envía sus credenciales (`iniciarSesion(email, contraseña)`) y `LeoncitosManager` delega la validación de dominio (`@turbineh`) y credenciales en `Usuario` (`autenticar()`).

3. **Si son válidas (`alt`):** `Usuario` devuelve `True`, `LeoncitosManager` registra el acceso en `LogActividad` (`registrarAccion()`) y concede acceso al sistema (`concederAcceso()`).

4. **Si son incorrectas (`else`):** `Usuario` devuelve `False`, se registra el intento fallido en `LogActividad` (`registrarAccion()`) y se muestra el error `"Usuario o contraseña incorrectos"`.

### CU-02 — Consulta del historial de facturas

1. El `Actor` elige entre consultar el historial de un cliente concreto (`consultarHistorialFactura(cliente)`) o el historial global de sus clientes asignados (`consultarHistorialFactura()`).

2. **Si hay error de BD o el cliente no existe (`alt`):** `LeoncitosManager` crea una incidencia en `NotificacionInterna` (`crear(errorConsulta)`), registra el fallo en `LogActividad` (`registrarAccion()`) y devuelve un listado vacío con el error (`mostrarError(historialVacío)`).

3. **Si la consulta es correcta (`else`):** se muestra el listado de facturas (`mostrarListadoFacturas()`). De forma opcional (`opt`), el usuario puede filtrar por estado, tipo y fecha (`filtrar(estado, tipo, fecha)`).

4. Tras seleccionar una factura (`seleccionarFactura(factura)`), el usuario puede opcionalmente (`opt`) descargar su PDF (`descargarPDF(factura)`) o comprobar su estado de pago (`consultarEstadoPago(factura)`), en cuyo caso `LeoncitosManager` consulta a la `Entidad Bancaria` (`consultarPago()`) y verifica el estado interno en `Factura` (`getEstado()`). Finalmente, se registra la consulta en `LogActividad` (`registrarAccion()`).

### CU-03 — Generación de facturas

1. El `Actor` abre el generador desde el perfil de un cliente (`abrirGeneradorFactura(cliente)`) y `LeoncitosManager` comprueba los permisos en `Usuario` (`isAllowed()`). Si no tiene permisos (`alt`), devuelve `errorAcceso()`.

2. Con permisos (`else`), se muestra el formulario con los datos autocompletados del cliente (`mostrarFormulario(datosCliente)`). El usuario elige entre crear una factura rectificativa vinculada a la original (`generarFacturaRectificativa(facturaOriginal)`) o una puntual/extraordinaria (`generarFacturaPuntual() / generarFacturaExtraordinaria()`).

3. Opcionalmente (`opt`), si cierra sin emitir, se guarda como borrador en `Factura` (`guardarBorrador()`).

4. Al emitir (`emitirFacturaIndiv()`), se comprueba si requiere aprobación (`reqAprobacion()`). En caso afirmativo (`opt`), se crea una solicitud en `NotificacionInterna` (`crear(solicitudAprobacion)`) y se verifica su aprobación (`isAprobada()`).

5. **Si falla el guardado en BD (`alt`):** se crea una alerta en `NotificacionInterna` (`crear(errorEmision)`), se audita en `LogActividad` (`registrarAccion()`) y se notifica `"Emisión cancelada"`.

6. **Si se guarda correctamente (`else`):** `LeoncitosManager` avisa al `Sistema Notificaciones` (`enviarNotificacionesCobro()`), registra la emisión en `LogActividad` (`registrarAccion()`) y confirma al usuario (`confirmarEmision()`).

### CU-04 — Modificación de preferencias de pagos

1. El `Actor` solicita actualizar las condiciones de pago o tarifa de un cliente (`modificarPreferenciasDePago(cliente, datos)`).

2. `LeoncitosManager` verifica los permisos mediante `Usuario.isAllowed()`. Si devuelve `False`, retorna `errorAcceso()`.

3. Si devuelve `True`, en caso de tratarse de un descuento extraordinario o precio pactado (`opt`), se genera una solicitud en `NotificacionInterna` (`crear(solicitudAprobacion)`).

4. `LeoncitosManager` actualiza los datos en `Cliente` (`actualizarPreferencias()`), deja traza en `LogActividad` (`registrarAccion()`) y confirma los cambios al usuario (`confirmarCambios()`).

### CU-05 — Consulta de documentación de uso del sistema

1. El `Actor` selecciona la pestaña de ayuda/documentación desde el menú principal (`accederDocumentacion()`).

2. `LeoncitosManager` recupera y muestra la guía de uso del sistema (`mostrarDocumentacion()`), sin alterar el estado ni los registros del sistema.

### CU-06 — Acceso al dashboard de facturación

1. El `Actor` (Administrador/SuperAdministrador/Lector) solicita acceder al dashboard (`accederDashboard()`) y `LeoncitosManager` consulta el rol del usuario en `Usuario` (`getRole()`), que devuelve el rol correspondiente (`rol`).

2. **Si es Administrador o SuperAdministrador (`alt`):** `LeoncitosManager` genera el informe financiero (`generarReporteDashboard()`), registra la consulta en `LogActividad` (`registrarAccion()`) y devuelve el informe al usuario (`mostrarReporteFinanciero()`).

3. **Si es Lector (`else`):** `LeoncitosManager` deniega el acceso al dashboard y comunica el resultado al usuario (`accesoDenegado()`), sin generar el informe financiero.

### CU-07 — Gestión de usuarios del sistema

1. El `Actor` (Administrador/SuperAdministrador) elige entre crear un nuevo usuario (`darAltaUsuario(email, pass, rol)`) o editar uno existente (`alt`).

2. **Si crea un usuario (`alt`):** `LeoncitosManager` genera una nueva instancia de `Usuario` mediante un mensaje de creación (`create`). De forma opcional (`opt`), el administrador puede asignar clientes al usuario recién creado (`asignarClientes(usuario, listaClientes)`).

3. **Si edita un usuario existente (`else`):** `LeoncitosManager` solicita el listado de usuarios (`listarUsuarios()`), muestra los resultados (`mostrarListado()`) y permite modificar los datos del usuario seleccionado (`modificarUsuario(usuario, nuevosDatos)`).

4. **Si se produce un error al guardar en BD (`alt`):** `LeoncitosManager` crea una incidencia en `NotificacionInterna` (`crear(errorActualizacion)`), registra el fallo en `LogActividad` (`registrarAccion()`) y muestra el mensaje `"Operación cancelada"` (`mostrarError()`).

5. **Si el guardado es correcto (`else`):** `LeoncitosManager` registra la operación en `LogActividad` (`registrarAccion()`) y confirma el resultado al usuario (`confirmarOperacion()`).

### CU-08 — Gestión de clientes

1. El `Actor` (Administrador/SuperAdministrador) elige entre editar o dar de baja un cliente existente, crear un cliente manualmente o importar clientes mediante un archivo CSV (`alt`).

2. **Si edita o da de baja un cliente (`alt`):** `LeoncitosManager` solicita el listado de clientes (`listarClientes()`), muestra los resultados (`mostrarListadoClientes()`) y permite modificar los datos o solicitar la baja (`modificarCliente() / darBajaCliente()`). De forma opcional (`opt`), se pueden gestionar los contactos del cliente existente (`añadirContacto() / quitarContacto()`) mediante la instancia `clienteExistente:Cliente`.

3. **Si crea un cliente manualmente (`else`):** el actor introduce los datos correspondientes (`darAltaCliente(datos)`) y `LeoncitosManager` genera una nueva instancia `nuevoCliente:Cliente` mediante un mensaje de creación (`crearCliente(datos)`).

4. **Si importa clientes mediante CSV (`else`):** el actor selecciona y envía el archivo (`importarClientesCSV(archivo)`). Si el formato es inválido (`alt`), `LeoncitosManager` muestra el error `"Formato CSV incorrecto"`. Si el formato es válido (`else`), ejecuta internamente el proceso de importación y creación de clientes (`crearClientesDesdeCSV(archivo)`).

5. **Si la operación es válida, pero se produce un error al guardar en BD (`alt`):** `LeoncitosManager` crea una incidencia en `NotificacionInterna` (`crear(errorCliente)`), registra el fallo en `LogActividad` (`registrarAccion()`) y muestra el mensaje `"Operación cancelada"`.

6. **Si la operación es válida y el guardado es correcto (`else`):** `LeoncitosManager` registra la operación en `LogActividad` (`registrarAccion()`) y confirma el resultado al usuario (`confirmarGuardado()`).

### CU-09 — Consulta de historial de modificaciones

1. El `Actor` (Administrador/SuperAdministrador) solicita consultar el historial de modificaciones (`consultarHistorialModificaciones()`).

2. `LeoncitosManager` solicita los registros almacenados a `LogActividad` (`getLog()`), recibe el listado de modificaciones (`listaLogs`) y muestra los resultados ordenados cronológicamente (`mostrarLogsCronologicos()`).

3. De forma opcional (`opt`), el usuario puede filtrar los registros por usuario y rango de fechas (`filtrarLogs(usuario, rangoFechas)`). `LeoncitosManager` devuelve los resultados correspondientes (`mostrarLogsFiltrados()`).

4. Finalmente, `LeoncitosManager` registra la consulta realizada en `LogActividad` (`registrarAccion()`).

### CU-10 — Modificación de configuración global

1. El `Actor` (SuperAdministrador) accede a la configuración global (`accederConfiguracionGlobal()`) y `LeoncitosManager` muestra los parámetros actuales del sistema (`mostrarParametros()`).

2. El actor solicita modificar los parámetros (`actualizarConfiguracionGlobal(nuevosParametros)`). `LeoncitosManager` pausa internamente las acciones del sistema (`pausarAccionesSistema()`) y solicita a `ConfiguracionGlobal` aplicar los nuevos valores (`aplicarCambios()`).

3. **Si se produce un error al actualizar (`alt`):** `ConfiguracionGlobal` devuelve el resultado de error (`error`), `LeoncitosManager` solicita restaurar la configuración original (`restaurarConfiguracionOriginal()`), rehabilita el sistema (`rehabilitarSistema()`), registra el fallo en `LogActividad` (`registrarAccion()`) y notifica el error al Superadministrador (`notificarError()`).

4. **Si la actualización es correcta (`else`):** `ConfiguracionGlobal` devuelve el resultado satisfactorio (`ok`), `LeoncitosManager` rehabilita el sistema (`rehabilitarSistema()`), registra la modificación en `LogActividad` (`registrarAccion()`) y confirma la actualización al Superadministrador (`confirmarActualizacion()`).

## Trazabilidad

| Diagrama (`.mdj`) | Caso de uso | Participantes / responsabilidades | 
| ----- | ----- | ----- | 
| `CU-01 — Autenticación de usuario` | **CU-01** | `Actor` (inicia solicitud y envía credenciales), `LeoncitosManager` (coordina el flujo de login y errores), `Usuario` (valida dominio corporativo y credenciales con `autenticar()`), `LogActividad` (registra accesos válidos e intentos fallidos con `registrarAccion()`). | 
| `CU-02 — Consulta del historial de facturas` | **CU-02** | `Actor` (consulta, filtra, descarga PDF o pide estado), `LeoncitosManager` (ejecuta `consultarHistorialFactura()` y `consultarEstadoPago()`), `Factura` (provee su estado con `getEstado()`), `Entidad Bancaria` (pasarela simulada consultada con `consultarPago()`), `NotificacionInterna` (registra errores de BD), `LogActividad` (audita consultas y descargas). | 
| `CU-03 — Generación de facturas` | **CU-03** | `Actor` (rellena y emite la factura), `LeoncitosManager` (orquesta creación normal/rectificativa y emisión), `Usuario` (verifica permisos con `isAllowed()`), `Factura` (guarda borrador, evalúa `reqAprobacion()` e `isAprobada()`), `NotificacionInterna` (gestiona solicitudes de aprobación y errores de BD), `Sistema Notificaciones` (envía avisos de cobro con `enviarNotificacionesCobro()`), `LogActividad` (audita la emisión/errores). | 
| `CU-04 — Modificación de preferencias de pagos` | **CU-04** | `Actor` (solicita cambios de tarifa/condiciones), `LeoncitosManager` (coordina `modificarPreferenciasDePago()`), `Usuario` (valida autorización con `isAllowed()`), `Cliente` (actualiza sus condiciones y rango), `NotificacionInterna` (solicita aprobación si hay descuento extraordinario), `LogActividad` (registra la modificación). | 
| `CU-05 — Consulta de documentación de uso` | **CU-05** | `Actor` (solicita la ayuda), `LeoncitosManager` (recupera y muestra la documentación estática del sistema). | 
| `CU-06 — Acceso al dashboard de facturación` | **CU-06** | `Actor` (solicita acceder al dashboard), `LeoncitosManager` (coordina el acceso y genera el informe financiero mediante `generarReporteDashboard()`), `Usuario` (proporciona el rol mediante `getRole()`), `LogActividad` (registra las consultas autorizadas con `registrarAccion()`). |
| `CU-07 — Gestión de usuarios del sistema` | **CU-07** | `Actor` (crea o modifica usuarios y asigna clientes), `LeoncitosManager` (coordina `darAltaUsuario()`, `listarUsuarios()`, `modificarUsuario()` y `asignarClientes()`), `Usuario` (representa las cuentas creadas), `NotificacionInterna` (registra incidencias durante el guardado), `LogActividad` (audita las operaciones realizadas). |
| `CU-08 — Gestión de clientes` | **CU-08** | `Actor` (edita, da de baja, crea o importa clientes), `LeoncitosManager` (coordina `listarClientes()`, `darAltaCliente()`, `modificarCliente()`, `darBajaCliente()` e `importarClientesCSV()`), `Cliente` (representa las instancias existentes y nuevas y gestiona sus contactos), `NotificacionInterna` (registra errores de guardado), `LogActividad` (audita las operaciones realizadas). |
| `CU-09 — Consulta de historial de modificaciones` | **CU-09** | `Actor` (solicita el historial y aplica filtros), `LeoncitosManager` (coordina `consultarHistorialModificaciones()` y la aplicación de filtros), `LogActividad` (proporciona los registros mediante `getLog()` y registra las consultas mediante `registrarAccion()`). |
| `CU-10 — Modificación de configuración global` | **CU-10** | `Actor` (SuperAdministrador que solicita los cambios), `LeoncitosManager` (coordina la actualización, pausa y rehabilitación del sistema), `ConfiguracionGlobal` (aplica los nuevos parámetros y permite restaurar la configuración original), `LogActividad` (registra las actualizaciones y los errores). |

## Consistencia

Los diez diagramas de secuencia mantienen una correspondencia directa con el **diagrama de clases** (`LeoncitosProgram`) y la **arquitectura modular en 3 capas** del sistema, representando de forma conjunta los principales procesos de autenticación, gestión de clientes y usuarios, facturación, consultas, auditoría y configuración global. Se presentan únicamente las siguientes diferencias de nivel de abstracción justificadas por la notación UML:

1. **Correspondencia de clases y operaciones:** Los participantes internos (`LeoncitosManager`, `Usuario`, `Cliente`, `Factura`, `NotificacionInterna`, `LogActividad` y `ConfiguracionGlobal`) mantienen una correspondencia con las clases definidas en el diagrama de clases (`LeoncitosProgram`). Las operaciones principales representadas en los diez escenarios, como `iniciarSesion()`, `autenticar()`, `isAllowed()`, `consultarHistorialFactura()`, `consultarEstadoPago()`, `generarFacturaRectificativa()`, `generarFacturaPuntual()`, `generarFacturaExtraordinaria()`, `emitirFacturaIndiv()`, `modificarPreferenciasDePago()`, `actualizarPreferencias()`, `getRole()`, `darAltaUsuario()`, `modificarUsuario()`, `asignarClientes()`, `darAltaCliente()`, `modificarCliente()`, `darBajaCliente()`, `consultarHistorialModificaciones()`, `getLog()` y `actualizarConfiguracionGlobal()`, representan las responsabilidades e interacciones previstas para las distintas clases del sistema. Las operaciones auxiliares específicas de los diagramas de secuencia se justifican por el mayor nivel de detalle de estos escenarios.

2. **Abstracción de la capa de presentación (`Actor`):** En los primeros cinco diagramas se representa al usuario y a la interfaz web de forma unificada mediante el participante `Actor`, omitiendo las clases intermedias de vistas y formularios para centrar el foco en la lógica de negocio (`LeoncitosManager`) y las entidades del dominio. En los diagramas CU-06 a CU-10 se identifican los actores mediante sus roles (Administrador, SuperAdministrador y Lector, según el escenario), permitiendo representar de forma más específica las restricciones de acceso y las operaciones que puede realizar cada perfil.

3. **Separación de actores y sistemas externos:** Entidades externas al núcleo de dominio como la **Entidad Bancaria** (pasarela de pagos simulada, requisito `FR-43`) y el **Sistema Notificaciones** (envío automático de correos/WhatsApp, requisitos `FR-47` a `FR-52`) aparecen como líneas de vida independientes en `CU-02` y `CU-03` para evidenciar el desacoplamiento mediante adaptadores definido en la arquitectura, aunque en el diagrama de clases su lógica se encapsula tras las operaciones `consultarEstadoPago()` y `enviarNotificacionesCobro()` de `LeoncitosManager`.

4. **Métodos auxiliares de flujo y persistencia:** Operaciones de apoyo como `mostrarCampos()`, `mostrarFormulario()`, `guardarBorrador()`, `crear()`, `mostrarListado()`, `mostrarReporteFinanciero()`, `mostrarListadoClientes()`, `mostrarLogsCronologicos()`, `mostrarLogsFiltrados()`, `mostrarParametros()`, `confirmarOperacion()`, `confirmarGuardado()` o `mostrarError()` representan interacciones internas de interfaz, respuestas del sistema, operaciones auxiliares o procesos de persistencia que permiten describir el comportamiento de cada escenario sin necesidad de representar todos los detalles internos de implementación en el diagrama de clases.

5. **Representación de flujos alternativos, opcionales y repetitivos:** Los diez diagramas utilizan fragmentos combinados de UML para representar las distintas posibilidades de ejecución. Los fragmentos `alt` permiten diferenciar, entre otros casos, los resultados de autenticación (`CU-01`), los errores y resultados de consulta (`CU-02`), la validación de permisos y emisión de facturas (`CU-03`), la autorización para modificar preferencias (`CU-04`), el control de acceso al dashboard (`CU-06`), la creación o edición de usuarios (`CU-07`), las distintas operaciones de gestión de clientes y la validación de archivos CSV (`CU-08`), así como los resultados de actualización de la configuración global (`CU-10`). Por otro lado, los fragmentos `opt` representan operaciones opcionales como el filtrado y descarga de facturas (`CU-02`), el guardado de borradores y las solicitudes de aprobación (`CU-03` y `CU-04`), la asignación de clientes a usuarios (`CU-07`), la gestión de contactos (`CU-08`) y el filtrado del historial de modificaciones (`CU-09`). Finalmente, el fragmento `loop` de `CU-01` representa la repetición del proceso de autenticación hasta obtener un resultado satisfactorio.

6. **Trazabilidad de auditoría, gestión de errores y recuperación:** Los diagramas contemplan diferentes mecanismos para garantizar el control de las operaciones. `LogActividad` registra los accesos e intentos de autenticación (`CU-01`), las consultas y operaciones relacionadas con facturas (`CU-02` y `CU-03`), las modificaciones de preferencias (`CU-04`), las consultas autorizadas al dashboard (`CU-06`), las operaciones de gestión de usuarios y clientes (`CU-07` y `CU-08`), las consultas del propio historial (`CU-09`) y los cambios en la configuración global (`CU-10`). Asimismo, `NotificacionInterna` permite registrar incidencias y errores durante las operaciones de consulta, emisión de facturas y gestión de usuarios y clientes (`CU-02`, `CU-03`, `CU-07` y `CU-08`). En particular, `CU-10` contempla la recuperación de la configuración original mediante `restaurarConfiguracionOriginal()` cuando falla una actualización, garantizando que el sistema vuelva a estar operativo. Por su parte, `CU-05` representa una consulta informativa de documentación que no modifica el estado ni los registros del sistema.