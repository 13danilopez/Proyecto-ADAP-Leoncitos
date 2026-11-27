# Diagramas UML de secuencia

## Selección de escenarios

Para documentar el comportamiento dinámico del sistema **LeoncitosProgram**, se han seleccionado los escenarios más críticos, representativos y con mayor carga lógica de negocio a partir de los casos de uso definidos:

1. **CU-01 — Autenticación de usuario:** Flujo principal de inicio de sesión válido (con validación de dominio corporativo), bucle de reintentos (`loop`) y excepción por credenciales incorrectas (`alt`), incluyendo el registro en auditoría.
2. **CU-02 — Consulta del historial de facturas:** Escenarios alternativos de consulta global o filtrada por cliente (`alt`), manejo de excepciones por error de base de datos o cliente inexistente, y flujos opcionales (`opt`) para filtrado avanzado, descarga de PDF y consulta de estado de pago en tiempo real con la entidad bancaria.
3. **CU-03 — Generación de facturas:** Flujo completo de creación y emisión de facturas (puntuales, extraordinarias y rectificativas), validación previa de permisos (`alt`), guardado en borrador (`opt`), flujo de solicitud de aprobación cuando el importe o tipo lo requiere (`opt`), gestión de errores de persistencia y envío de notificaciones de cobro.
4. **CU-04 — Modificación de preferencias de pagos:** Flujo de actualización de condiciones de facturación y pago de un cliente, validación de permisos (`alt`) y flujo opcional (`opt`) de generación de solicitud de aprobación ante descuentos extraordinarios o precios pactados.
5. **CU-05 — Consulta de documentación de uso:** Flujo principal directo de acceso y visualización de la guía o documentación de ayuda del sistema.

---

## Descripción de los Casos de Uso

### Descripción de CU-01: Autenticación de usuario
1. El **Actor** solicita iniciar sesión en el sistema (`solicitarInicioSesion()`).
2. El controlador `LeoncitosManager` solicita las credenciales mostrando los campos de email y contraseña (`mostrarCampos()`).
3. Dentro de un bucle de intentos (`loop`), el Actor introduce sus credenciales (`iniciarSesion(email, contraseña)`).
4. `LeoncitosManager` delega la validación en la entidad `Usuario` mediante `autenticar()`.
5. **Si las credenciales son válidas y pertenecen al dominio autorizado (`alt`):**
   - `Usuario` devuelve `True`.
   - `LeoncitosManager` registra el inicio de sesión exitoso en `LogActividad` (`registrarAccion()`).
   - Se concede acceso al sistema (`concederAcceso()`).
6. **Si las credenciales son incorrectas (`alt`):**
   - `Usuario` devuelve `False`.
   - `LeoncitosManager` registra el intento fallido en `LogActividad` (`registrarAccion()`).
   - Se notifica el error al Actor (`mostrarError("Usuario o contraseña incorrectos")`).

### Descripción de CU-02: Consulta del historial de facturas
1. El **Actor** solicita consultar el historial de facturas (`alt`), ya sea filtrado por un cliente específico (`consultarHistorialFactura(cliente)`) o a nivel global (`consultarHistorialFactura()`).
2. **Si ocurre un error de base de datos o el cliente no existe (`alt`):**
   - `LeoncitosManager` genera una `NotificacionInterna` (`crear(errorConsulta)`), registra el incidente en `LogActividad` (`registrarAccion()`) y muestra un error de historial vacío al Actor (`mostrarError(historialVacío)`).
3. **Si la consulta es correcta (`alt`):**
   - `LeoncitosManager` devuelve y muestra el listado de facturas (`mostrarListadoFacturas()`).
   - *(Opcional - `opt`)* El Actor aplica filtros por estado, tipo o fecha (`filtrar(estado, tipo, fecha)`) y el sistema actualiza la vista (`mostrarFacturasFiltradas()`).
   - El Actor selecciona una factura concreta (`seleccionarFactura(factura)`).
   - *(Opcional - `opt`)* El Actor solicita descargar el documento (`descargarPDF(factura)`) y recibe el archivo (`archivoPDF`).
   - *(Opcional - `opt`)* El Actor solicita comprobar el estado del pago (`consultarEstadoPago(factura)`). `LeoncitosManager` consulta a la **Entidad Bancaria** (`consultarPago()`), verifica el estado interno en `Factura` (`getEstado()`), muestra la información actualizada al Actor (`mostrarEstadoPago()`) y se registra la acción en `LogActividad`.

### Descripción de CU-03: Generación de facturas
1. El **Actor** abre el generador de facturas para un cliente (`abrirGeneradorFactura(cliente)`).
2. `LeoncitosManager` comprueba los permisos del `Usuario` (`isAllowed()`).
3. **Si no tiene permisos (`alt`):** `Usuario` devuelve `False` y el controlador retorna `errorAcceso()`.
4. **Si tiene permisos (`alt`):**
   - `Usuario` devuelve `True` y se muestra el formulario con los datos del cliente (`mostrarFormulario(datosCliente)`).
   - Según el tipo de factura (`alt`), el Actor invoca `generarFacturaRectificativa(facturaOriginal)` o `generarFacturaPuntual() / generarFacturaExtraordinaria()`.
   - *(Opcional - `opt`)* Si el Actor cierra sin emitir, `LeoncitosManager` guarda el estado actual en `Factura` (`guardarBorrador()`).
   - El Actor procede a emitir la factura (`emitirFacturaIndiv()`).
   - *(Opcional - `opt`)* Si la factura requiere aprobación (`reqAprobacion()`), `LeoncitosManager` crea una `NotificacionInterna` (`crear(solicitudAprobacion)`) y verifica su estado (`isAprobada()`).
   - **Si ocurre un error al guardar en BD (`alt`):** Se crea una `NotificacionInterna` (`crear(errorEmision)`), se audita en `LogActividad` (`registrarAccion()`) y se comunica al Actor (`mostrarError("Emisión cancelada")`).
   - **Si el guardado es correcto (`alt`):** Se envían los avisos mediante el **Sistema Notificaciones** (`enviarNotificacionesCobro()`), se registra la emisión en `LogActividad` (`registrarAccion()`) y se confirma al Actor (`confirmarEmision()`).

### Descripción de CU-04: Modificación de preferencias de pagos
1. El **Actor** solicita modificar las preferencias de pago de un cliente (`modificarPreferenciasDePago(cliente, datos)`).
2. `LeoncitosManager` valida los permisos del `Usuario` (`isAllowed()`).
3. **Si no tiene permisos (`alt`):** `Usuario` responde `False` y `LeoncitosManager` notifica `errorAcceso()`.
4. **Si tiene permisos (`alt`):**
   - `Usuario` responde `True`.
   - *(Opcional - `opt`)* Si la modificación implica un descuento extraordinario o un precio pactado, `LeoncitosManager` genera una `NotificacionInterna` (`crear(solicitudAprobacion)`).
   - `LeoncitosManager` aplica los cambios en la entidad `Cliente` (`actualizarPreferencias()`).
   - Se registra la operación en `LogActividad` (`registrarAccion()`) y se confirma la actualización al Actor (`confirmarCambios()`).

### Descripción de CU-05: Consulta de documentación de uso
1. El **Actor** solicita acceder a la sección de ayuda o documentación del sistema (`accederDocumentacion()`).
2. `LeoncitosManager` recupera y despliega el contenido documental al Actor (`mostrarDocumentacion()`).

---

## Trazabilidad

| Diagrama | Caso de uso | Participantes / responsabilidades |
|---|---|---|
| `CU-01.mdj` / `CU-01` | **CU-01:** Autenticación de usuario | **Actor:** Inicia el flujo e introduce credenciales.<br>**LeoncitosManager:** Orquesta el login, gestiona respuestas y auditoría.<br>**Usuario:** Valida credenciales (`autenticar()`).<br>**LogActividad:** Registra accesos exitosos y fallidos (`registrarAccion()`). |
| `CU-02.mdj` / `CU-02` | **CU-02:** Consulta del historial de facturas | **Actor:** Solicita historial, filtra, descarga PDF y consulta pagos.<br>**LeoncitosManager:** Gestiona consultas, filtros y comunicación externa.<br>**Factura:** Provee su estado actual (`getEstado()`).<br>**Entidad Bancaria:** Sistema externo que informa el estado real de cobro.<br>**NotificacionInterna:** Alerta sobre fallos en la consulta.<br>**LogActividad:** Registra errores y consultas de estado de pago. |
| `CU-03.mdj` / `CU-03` | **CU-03:** Generación de facturas | **Actor:** Configura y emite facturas.<br>**LeoncitosManager:** Controla el flujo de creación, aprobación y emisión.<br>**Usuario:** Verifica permisos de acceso (`isAllowed()`).<br>**Factura:** Gestiona borradores, aprobación y estados.<br>**NotificacionInterna:** Registra solicitudes de aprobación y errores de emisión.<br>**Sistema Notificaciones:** Envía avisos de cobro al cliente.<br>**LogActividad:** Audita emisiones y errores. |
| `CU-04.mdj` / `CU-04` | **CU-04:** Modificación de preferencias de pagos | **Actor:** Introduce las nuevas preferencias de pago.<br>**LeoncitosManager:** Valida autorización y coordina la actualización.<br>**Usuario:** Verifica permisos (`isAllowed()`).<br>**Cliente:** Actualiza sus condiciones comerciales y de pago.<br>**NotificacionInterna:** Gestiona aprobaciones por descuentos/precios pactados.<br>**LogActividad:** Registra la modificación realizada. |
| `CU-05.mdj` / `CU-05` | **CU-05:** Consulta de documentación de uso | **Actor:** Solicita la guía de uso.<br>**LeoncitosManager:** Sirve y muestra la documentación al usuario. |

---

## Consistencia

Los diagramas de secuencia mantienen una alta coherencia estructural y semántica con el **Diagrama de Clases (`LeoncitosProgram`)**, aunque presentan diferencias deliberadas propias del nivel de abstracción entre el diseño estático (dominio) y el diseño dinámico (interacción/casos de uso):

1. **Correspondencia directa con el Diagrama de Clases:**
   - **Controlador central:** Todas las interacciones pasan por `LeoncitosManager` (`«control»`), respetando los métodos definidos en su interfaz de clases: `iniciarSesion()`, `consultarHistorialFactura()`, `generarFacturaPuntual()`, `generarFacturaExtraordinaria()`, `generarFacturaRectificativa()`, `emitirFacturaIndiv()`, `modificarPreferenciasDePago()`, `consultarEstadoPago()` y `enviarNotificacionesCobro()`.
   - **Entidades y operaciones:** Los mensajes enviados a las líneas de vida coinciden con las operaciones del modelo de dominio, tales como `Usuario.autenticar()`, `Usuario.isAllowed()`, `Factura.getEstado()`, `Factura.reqAprobacion()`, `Factura.isAprobada()` y `LogActividad.registrarAccion()`.

2. **Diferencias de nivel de abstracción (Justificación):**
   - **Interfaz de Usuario (UI) implícita en el Controlador:** En el diagrama de clases no existen clases frontera (`«boundary»`), por lo que en los diagramas de secuencia `LeoncitosManager` asume tanto la lógica de control como la respuesta visual hacia el `Actor` (ej. `mostrarCampos()`, `mostrarError()`, `mostrarListadoFacturas()`, `mostrarFormulario()`). Esto simplifica el modelo evitando proliferación de clases de vista sin lógica de negocio.
   - **Sistemas Externos como Participantes:** Se incluyen líneas de vida como **Entidad Bancaria** (en `CU-02`) y **Sistema Notificaciones** (en `CU-03`). Aunque no son entidades persistentes en el diagrama de clases (donde el banco se abstrae en el atributo `pasarelaOrigen: String` de `Pago`), en los diagramas de secuencia es indispensable representarlos como actores/sistemas externos para modelar la comunicación asíncrona o de red.
   - **Métodos auxiliares de flujo y persistencia:** Operaciones como `guardarBorrador()`, `actualizarPreferencias()`, `accederDocumentacion()` o las llamadas de instanciación `crear(...)` sobre `NotificacionInterna` representan responsabilidades de persistencia o navegación de interfaz que en el diagrama de clases quedan encapsuladas dentro de los métodos principales de `LeoncitosManager` o manejadas por los constructores/setters implícitos del lenguaje de implementación.