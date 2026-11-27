# Diagrama UML de clases

## Alcance

El diagrama recoge la estructura estática del dominio de LeoncitosManager tal y como quedan los requisitos tras la segunda entrega. Incluye:

- La clase de control `LeoncitosManager`, que coordina las operaciones de los casos de uso.
- Usuarios internos con su rol, y su relación con los clientes como responsables o como asignados (CU-01, CU-07).
- Clientes con contactos, etiquetas, departamento y estado de suscripción, incluidas pausas y bajas (CU-08).
- Facturas puntuales, recurrentes, extraordinarias y rectificativas, con idioma, flujo de aprobación y estados como incobrable o condonada (CU-02, CU-03).
- Pagos recibidos desde la pasarela simulada de Stripe/Revolut.
- Configuración global, log de actividad y notificaciones internas (CU-09, CU-10).
- Los tipos de datos (`Nif`, `Email`, `Movil`, `Direccion`, `Dinero`) y las enumeraciones de estados, tipos, idiomas y roles.

Quedan fuera la integración con pasarelas reales, el acceso de los clientes a la plataforma y Verifactu (Q-01). El dashboard (CU-06) y la documentación de uso (CU-05) no tienen clases propias: el dashboard se resuelve con la operación `generarReporteDashboard` sobre Factura y Pago, y la documentación no forma parte del modelo de dominio.

## Decisiones de diseño

**Clase de control**

- `LeoncitosManager` (estereotipo `<<control>>`) es el punto de entrada del sistema. Cada operación corresponde a un caso de uso o a un proceso automático (`generarFacturas`, `procesarPagosSimulados`, `enviarNotificacionesCobro`).
- Compone `Cliente`, `Usuario` y una única `ConfiguracionGlobal`, porque su ciclo de vida depende del sistema. Las facturas no se componen aquí: se alcanzan a través de su cliente.
- Las operaciones ilustran responsabilidades, no son firmas finales de implementación.

**Tipos de datos y enumeraciones**

- `Nif`, `Email`, `Movil` y `Dinero` son `<<primitiveType>>`: encapsulan un valor con su formato y validación (por ejemplo, el dominio corporativo del email, FR-57), en lugar de usar `String` sueltos.
- `Direccion` es `<<dataType>>`: agrupa calle, número, piso, ciudad y país, y no tiene identidad propia.
- `Dinero` une cantidad y divisa para que ningún importe quede sin moneda. `Moneda` representa un código (por ejemplo EUR).
- Los valores cerrados (estados, tipos, idiomas, roles) son enumeraciones. Así el estado de una factura se elige entre opciones fijas (FR-30) y no se escribe a mano.
- `EstadoFactura` separa `Condonada` e `Incobrable` de `Pagada`, para no falsear los datos financieros (FR-52, FR-53).

**Usuarios**

- `Usuario` guarda solo el hash de la contraseña (NFR-11) y se desactiva con `activo` en vez de borrarse, para no perder la referencia en facturas y log.
- Hay dos asociaciones distintas con `Cliente`. `responsable` (1 — 0..*) indica el usuario a cargo de cada cliente (FR-15). `clientesAsignados` (* — *) indica qué clientes puede ver y editar cada usuario (RB-07).
- Una restricción sobre `clientesAsignados` limita las asignaciones a los roles Lector y Administrador; el superadministrador ya tiene acceso total.

**Clientes y contactos**

- `Cliente` se identifica por su `nif`. `etiquetas` es multivaluado (FR-11) y `departamento` es opcional (FR-17).
- Pausa y baja son estados distintos de `EstadoCliente`. Sus datos asociados (`motivoPausa`, `fechaReactivacionPrevista`, `fechaBaja`) son opcionales `[0..1]` y solo se rellenan en ese estado (FR-09, FR-10).
- `rangoFacturacionAnual` es de tipo `Dinero` y determina la cuota (FR-07). `facturaRecurrente` indica la modalidad por defecto del cliente (FR-08).
- `Cliente` compone `Contacto` con multiplicidad 1..*: un contacto no existe sin su cliente y todo cliente tiene al menos uno (FR-03). Cada contacto admite varios emails y teléfonos, con operaciones para añadirlos y quitarlos.

**Facturas y pagos**

- `Cliente` agrega `Factura` (agregación, no composición). Una factura emitida tiene valor legal propio y se conserva aunque el cliente se dé de baja (FR-12, RB-03).
- `Factura` compone `Pago`: un pago solo existe ligado a una factura. Uno sin factura identificada no se registra y pasa a revisión manual.
- `Factura` conoce a su `creador` (`Usuario`) mediante una asociación navegable en un solo sentido.
- La asociación reflexiva `original` relaciona una rectificativa con la factura que corrige, sin sustituirla (FR-20). Una original puede tener varias rectificativas.
- `numero` es `[0..1]`: un borrador no consume numeración (FR-33) y `emitir()` se la asigna.
- `idioma` cubre las facturas multi-idioma (FR-37). `requiereAprobacion` y `estadoAprobacion` cubren las extraordinarias con revisión manual (FR-38, FR-45).
- `registrarPago`, `marcarComoVencida` y `condonar` concentran en `Factura` los cambios de estado de su ciclo de vida.

**Configuración, trazabilidad y notificaciones**

- `ConfiguracionGlobal` es única. Lleva valores por defecto (plazo de 5 días, retención de 5 años) que `restaurarValoresPredeterminados` recupera. `diasAvisoImpago` es una lista configurable.
- `LogActividad` apunta a su `autorCambio` con multiplicidad 0..1: es nulo cuando actúa un proceso automático. No tiene operaciones porque sus registros son inalterables (NFR-20).
- `tipoEntidad` e `idEntidad` en el log, y `elementoRelacionadoID` en las notificaciones, son referencias genéricas. Evitan una asociación por cada clase registrada.
- `NotificacionInterna` forma la bandeja de cada usuario (FR-63). Su `destinatario` es obligatorio y `actualizarEstado` la mueve entre Pendiente, EnRevision y Resuelta.
- Log y notificaciones navegan hacia `Usuario`, pero no al revés: el usuario no necesita conocerlos para operar.

**Restricciones OCL**

| Restricción | Significado |
| --- | --- |
| `estado = Borrador implies numero = null` | Un borrador no consume numeración. |
| `tipo = Rectificativa implies original != null` | Toda rectificativa referencia a su original. |
| `tipo = Extraordinaria implies requiereAprobacion = true and estadoAprobacion != null` | Las extraordinarias siempre pasan por aprobación. |
| `estado = Condonada implies Pago->isEmpty()` | Una deuda condonada no tiene pagos. |
| `estado = Pagada implies Pago->notEmpty()` | Una factura pagada tiene al menos un pago. |
| `clientesAsignados->notEmpty() implies rol = Lector or rol = Administrador` | Solo lectores y administradores reciben clientes asignados. |

## Trazabilidad

| Requisito / CU | Clases y operaciones | Motivo |
| --- | --- | --- |
| CU-01, UR-04, RB-01, NFR-11 | `LeoncitosManager.iniciarSesion`, `Usuario.verificarContrasena`, `Usuario.cambiarContrasena`, `Email` | Inicio de sesión con correo corporativo y contraseña guardada como hash. |
| CU-07, UR-03, NFR-10 | `LeoncitosManager.darAltaUsuario`, `modificarUsuario`, `darBajaUsuario`, `Usuario` (`rol`, `activo`), `RolUsuario` | Alta, edición y desactivación de usuarios con su rol. |
| CU-08, UR-06, FR-01, FR-02, FR-03 | `LeoncitosManager.darAltaCliente`, `importarClientesCSV`, `modificarCliente`, `Cliente`, `Contacto` (`agregarEmail`, `agregarTelefono`…), `Nif`, `Direccion` | Datos comerciales, fiscales y de contacto, alta individual o masiva por CSV. |
| FR-09, FR-10, FR-12, FR-13, FR-14 | `LeoncitosManager.darBajaCliente`, `Cliente.solicitarBaja`, `Cliente.pausarSuscripcion`, `EstadoCliente` | Pausa y baja como estados distintos; la baja es un borrado lógico. |
| FR-11, FR-17 | `Cliente.etiquetas`, `Cliente.departamento` | Etiquetado flexible y división por departamentos. |
| FR-15, RB-07, NFR-13 | Asociaciones `responsable` y `clientesAsignados` entre `Usuario` y `Cliente` | Responsable de cada cliente y acceso del lector solo a sus clientes. |
| FR-07, FR-08, FR-42, FR-43 | `Cliente.rangoFacturacionAnual`, `Cliente.tipoPlan`, `Cliente.facturaRecurrente`, `Dinero` | Rango anual del que sale la cuota y modalidad de facturación. |
| CU-04 | `LeoncitosManager.modificarPreferenciasDePago`, `Cliente` | Cambio de las condiciones de pago de un cliente. |
| CU-03, UR-01, UR-08 | `LeoncitosManager.generarFacturas`, `Factura.emitir`, `Factura` → `creador`, `TipoFactura` | Creación de facturas puntuales y recurrentes, y emisión con numeración. |
| FR-20 | Asociación `original` de `Factura`, `Factura.serie` | La rectificativa se relaciona con la original sin sustituirla. |
| FR-30 | `Factura.estado`, `EstadoFactura` | El estado se elige entre opciones fijas. |
| FR-37 | `Factura.idioma`, `Idioma` | Facturas en varios idiomas. |
| FR-38, FR-45 | `Factura.requiereAprobacion`, `Factura.estadoAprobacion`, `EstadoAprobacion` | Facturas extraordinarias y descuentos con revisión manual. |
| FR-52, FR-53 | `Factura.condonar`, `EstadoFactura` (`Incobrable`, `Condonada`) | Estados propios, distintos de Pagada. |
| CU-02, UR-02, UR-07 | `LeoncitosManager.consultarFacturas`, `Cliente`, `Factura`, `Pago` | Historial de facturas por cliente y su estado de cobro. |
| FR-47, FR-48, FR-51 | `LeoncitosManager.procesarPagosSimulados`, `Factura.registrarPago`, `Factura.marcarComoVencida`, `Pago` | Pagos de la pasarela simulada; `idTransaccion` evita duplicados. |
| UR-01 | `LeoncitosManager.enviarNotificacionesCobro`, `ConfiguracionGlobal.diasAvisoImpago`, `LogActividad` | Recordatorios y avisos de impago al cliente, anotados en el log. |
| FR-61, FR-63 | `NotificacionInterna` (`actualizarEstado`), asociación `destinatario`, `EstadoNotificacion` | Bandeja de tareas e incidencias de cada usuario. |
| CU-06, UR-09, FR-86, FR-87, FR-89 | `LeoncitosManager.generarReporteDashboard` sobre `Factura` y `Pago` | El dashboard se calcula, no tiene clase propia. |
| CU-09, UR-07, FR-95, NFR-20 | `LeoncitosManager.consultarHistorialModificaciones`, `LogActividad`, asociación `autorCambio` | Historial de quién cambió qué y cuándo. |
| FR-96 | `LogActividad` (autor nulo), `NotificacionInterna` | Fallos de procesos automáticos tras agotar los reintentos. |
| CU-10, UR-05 | `LeoncitosManager.actualizarConfiguracionGlobal`, `ConfiguracionGlobal` (`cambiarIdioma`, `aplicarPlazosNuevos`, `restaurarValoresPredeterminados`) | Plazo de pago, serie, retención, idioma y días de aviso. |

## Consistencia con otros modelos

No toda clase implica una tabla, ni toda tabla procede de una clase. La correspondencia con el modelo físico de datos es la siguiente:

| Elemento del diagrama de clases | Elemento del E-R | Observación |
| --- | --- | --- |
| `Usuario`, `Cliente`, `Contacto`, `Factura`, `Pago`, `ConfiguracionGlobal`, `LogActividad`, `NotificacionInterna` | Tabla homónima | Clases de entidad con tabla propia. |
| `LeoncitosManager` | Sin tabla | Clase de control. Sus composiciones no se traducen a claves foráneas. |
| `Cliente.etiquetas` | `Cliente_Etiqueta` | Atributo multivaluado llevado a tabla aparte. |
| `Contacto.email`, `Contacto.telefono` | `Contacto_Email`, `Contacto_Telefono` | Atributos multivaluados llevados a tabla aparte. |
| `ConfiguracionGlobal.diasAvisoImpago` | `Configuracion_DiasAviso` | Atributo multivaluado; `ConfiguracionGlobal` tiene una sola fila. |
| Asociación `clientesAsignados` (* — *) | `Usuario_Cliente_Asignado` | Tabla intermedia con clave compuesta por email y NIF. |
| Asociación `responsable` | `Cliente.email_responsable` | Clave foránea obligatoria. |
| Agregación `Cliente` — `Factura` | `Factura.nif_cliente` | Clave foránea obligatoria. |
| Composición `Factura` — `Pago` | `Pago.id_factura` | Clave foránea obligatoria; un pago sin factura no se inserta. |
| Asociación `creador` | `Factura.email_creador` | Clave foránea obligatoria. |
| Asociación reflexiva `original` | `Factura.id_factura_original` | Clave foránea opcional hacia la propia tabla. |
| Asociación `autorCambio` | Clave foránea opcional en `LogActividad` | Nula cuando actúa un proceso automático. |
| Asociación `destinatario` | Clave foránea obligatoria en `NotificacionInterna` | Bandeja de cada usuario. |
| `Nif`, `Email`, `Movil` | Columnas simples | Los tipos primitivos no generan tabla. |
| `Direccion` | Columnas de `Cliente` | El tipo de dato se aplana en sus campos. |
| `Dinero` | Dos columnas: importe `DECIMAL(10,2)` y divisa ISO 4217 | Por ejemplo `rango_facturacion_*` en `Cliente`. |
| Enumeraciones | Restricciones `CHECK` | Rol, estados, tipo de factura e idioma. |

Otras correspondencias:

- **Identificadores.** `Usuario` y `Cliente` usan su email y su NIF como clave, igual que en el E-R. `Factura` lleva en el E-R una clave artificial `id_factura`, porque `numero` es nulo en los borradores; en el diagrama la identidad la aporta el propio objeto.
- **Restricciones OCL.** Las de borrador sin número, rectificativa con original y extraordinaria con aprobación se implementan como `CHECK`. Las de pagos en facturas condonadas o pagadas y la de roles en `clientesAsignados` van en triggers o en la aplicación.
- **Dashboard.** No tiene clase ni tabla; `generarReporteDashboard` se apoya en consultas sobre `Factura` y `Pago`.
- **Decisiones compartidas.** Las notificaciones a clientes no tienen clase ni tabla y se anotan en `LogActividad`. Los descuentos y precios pactados se reflejan en el importe de una factura extraordinaria. La tabla de rangos de facturación sigue pendiente de confirmar en ambos modelos.
- **Casos de uso.** Cada operación pública de `LeoncitosManager` corresponde a un caso de uso (CU-01 a CU-10, salvo CU-05) o a un proceso automático, lo que sirve de enlace con los diagramas de secuencia.
