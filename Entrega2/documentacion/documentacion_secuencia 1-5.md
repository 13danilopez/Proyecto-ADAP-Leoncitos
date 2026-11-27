# Diagramas UML de secuencia

## Selección de escenarios

Para modelar el comportamiento dinámico de la plataforma interna de facturación y cobros de **TurbineH** (`LeoncitosProgram`), se han seleccionado los escenarios correspondientes a los **5 primeros casos de uso especificados** (`CU-01` a `CU-05`), implementados en el archivo del proyecto StarUML (`Collaboration1` a `Collaboration5`). Cada diagrama integra en un mismo flujo el **camino principal**, los **flujos alternativos/opcionales** (fragmentos `alt` y `opt`) y el **manejo de excepciones y errores** (fallos de autenticación, validación de permisos mediante `isAllowed()` y errores de acceso a base de datos), garantizando la trazabilidad de auditoría (`LogActividad`) y la notificación de incidencias (`NotificacionInterna`).

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

## Trazabilidad

| Diagrama (`.mdj`) | Caso de uso | Participantes / responsabilidades | 
| ----- | ----- | ----- | 
| `CU-01 — Autenticación de usuario` | **CU-01** | `Actor` (inicia solicitud y envía credenciales), `LeoncitosManager` (coordina el flujo de login y errores), `Usuario` (valida dominio corporativo y credenciales con `autenticar()`), `LogActividad` (registra accesos válidos e intentos fallidos con `registrarAccion()`). | 
| `CU-02 — Consulta del historial de facturas` | **CU-02** | `Actor` (consulta, filtra, descarga PDF o pide estado), `LeoncitosManager` (ejecuta `consultarHistorialFactura()` y `consultarEstadoPago()`), `Factura` (provee su estado con `getEstado()`), `Entidad Bancaria` (pasarela simulada consultada con `consultarPago()`), `NotificacionInterna` (registra errores de BD), `LogActividad` (audita consultas y descargas). | 
| `CU-03 — Generación de facturas` | **CU-03** | `Actor` (rellena y emite la factura), `LeoncitosManager` (orquesta creación normal/rectificativa y emisión), `Usuario` (verifica permisos con `isAllowed()`), `Factura` (guarda borrador, evalúa `reqAprobacion()` e `isAprobada()`), `NotificacionInterna` (gestiona solicitudes de aprobación y errores de BD), `Sistema Notificaciones` (envía avisos de cobro con `enviarNotificacionesCobro()`), `LogActividad` (audita la emisión/errores). | 
| `CU-04 — Modificación de preferencias de pagos` | **CU-04** | `Actor` (solicita cambios de tarifa/condiciones), `LeoncitosManager` (coordina `modificarPreferenciasDePago()`), `Usuario` (valida autorización con `isAllowed()`), `Cliente` (actualiza sus condiciones y rango), `NotificacionInterna` (solicita aprobación si hay descuento extraordinario), `LogActividad` (registra la modificación). | 
| `CU-05 — Consulta de documentación de uso` | **CU-05** | `Actor` (solicita la ayuda), `LeoncitosManager` (recupera y muestra la documentación estática del sistema). | 

## Consistencia

Los diagramas de secuencia mantienen una correspondencia directa con el **diagrama de clases** (`LeoncitosProgram`) y la **arquitectura modular en 3 capas** del sistema, presentando únicamente las siguientes diferencias de nivel de abstracción justificadas por la notación UML:

1. **Correspondencia exacta de clases y operaciones:** Todos los participantes internos (`LeoncitosManager`, `Usuario`, `Cliente`, `Factura`, `NotificacionInterna`, `LogActividad`) y las llamadas principales (`iniciarSesion()`, `autenticar()`, `isAllowed()`, `consultarHistorialFactura()`, `consultarEstadoPago()`, `generarFacturaRectificativa()`, `generarFacturaPuntual()`, `generarFacturaExtraordinaria()`, `emitirFacturaIndiv()`, `reqAprobacion()`, `isAprobada()`, `modificarPreferenciasDePago()`, `enviarNotificacionesCobro()`, `registrarAccion()`) coinciden nominalmente con los definidos en el `.mdj`.

2. **Abstracción de la capa de presentación (`Actor`):** En los diagramas de secuencia se representa al usuario y a la interfaz web de forma unificada mediante el participante `Actor`, omitiendo las clases intermedias de vista/formularios para centrar el foco en la lógica de negocio (`«control» LeoncitosManager`) y las entidades del dominio.

3. **Separación de actores/sistemas externos:** Entidades externas al núcleo de dominio como la **Entidad Bancaria** (pasarela de pagos simulada, requisito `FR-43`) y el **Sistema Notificaciones** (envío automático de correos/WhatsApp, requisitos `FR-47` a `FR-52`) aparecen como líneas de vida independientes en `CU-02` y `CU-03` para evidenciar el desacoplamiento mediante adaptadores definido en la arquitectura, aunque en el diagrama de clases su lógica se encapsula tras las operaciones `consultarEstadoPago()` y `enviarNotificacionesCobro()` de `LeoncitosManager`.

4. **Métodos auxiliares de flujo y persistencia:** Operaciones de apoyo como `mostrarCampos()`, `mostrarFormulario()`, `guardarBorrador()` o `crear()` representan interacciones internas de interfaz, ciclo de vida de objetos o persistencia que en el diagrama de clases se abstraen dentro de las responsabilidades generales de `LeoncitosManager` y de los constructores/accesores implícitos de cada entidad.