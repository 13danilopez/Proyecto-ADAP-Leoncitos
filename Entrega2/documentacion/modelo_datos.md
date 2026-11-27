# Modelo físico de datos

## Alcance

El modelo guarda los datos que necesita LeoncitosManager para llevar la facturación y el seguimiento de cobros de TurbineH, tal y como quedan los requisitos tras la segunda entrega. En concreto:

- Usuarios internos, con su rol y los clientes que tienen asignados (CU-01, CU-07).
- Clientes con sus contactos, etiquetas, departamento y estado de suscripción, incluidas las pausas y las bajas (CU-08).
- Facturas puntuales, recurrentes, extraordinarias y rectificativas, con idioma, flujo de aprobación y estados como incobrable o condonada (CU-02, CU-03).
- Pagos recibidos desde la pasarela simulada de Stripe/Revolut.
- Log de actividad, notificaciones internas y configuración global (CU-09, CU-10).

Quedan fuera la integración con pasarelas reales, el acceso de los clientes a la plataforma y Verifactu (Q-01). El dashboard (CU-06) no tiene tablas propias: se calcula con consultas sobre Factura y Pago.

## Justificación del modelo

**Usuarios y clientes**

- Usuario (1) — Cliente (0..N), "responsable": todo cliente tiene un responsable (FR-15), pero un usuario puede no serlo de ninguno.
- Usuario (N) — Cliente (M), "asignación": es una relación muchos a muchos, así que se resuelve con la tabla Usuario_Cliente_Asignado (clave compuesta por email y NIF). Con ella se decide qué clientes ve cada lector.
- Cliente (1) — Cliente_Etiqueta (0..N): en el diagrama de clases las etiquetas son una lista, y una lista no cabe en una sola columna, así que va en su propia tabla.

**Contactos**

- Cliente (1) — Contacto (1..N): un cliente tiene al menos un contacto (FR-03).
- Contacto (1) — Contacto_Email (0..N) y Contacto (1) — Contacto_Telefono (0..N): mismo motivo que con las etiquetas, cada contacto puede tener varios emails y teléfonos.

**Facturación**

- Cliente (1) — Factura (0..N): un cliente puede no tener facturas todavía.
- Usuario (1) — Factura (0..N), "creador": cada factura guarda quién la creó.
- Factura (0..1) — Factura (0..N), "original": la rectificativa apunta a su original con `id_factura_original`. Una original puede tener varias rectificativas y nunca se borra ni se sustituye (FR-20).
- Factura (1) — Pago (0..N): una factura puede no tener pagos (pendiente, condonada) o tener varios. Un pago siempre pertenece a una factura.

El número de factura (`numero`) es nulo mientras la factura está en borrador, para que los borradores no gasten numeración. Una factura emitida se identifica por el par serie y número. Condonada e incobrable son estados propios y distintos de Pagada, para no falsear los datos financieros.

**Trazabilidad y avisos**

- Usuario (0..1) — LogActividad (0..N): cada entrada dice quién hizo qué y sobre qué entidad (`tipo_entidad` e `id_entidad`). El autor es nulo cuando lo hace un proceso automático, como la facturación mensual o los reintentos.
- Usuario (1) — NotificacionInterna (0..N): es la bandeja de cada usuario (FR-63). `elemento_relacionado_id` apunta a la factura, cliente o pago correspondiente.

**Configuración**

- ConfiguracionGlobal (1) — Configuracion_DiasAviso (0..N): los días de aviso de impago son una lista, así que van en tabla aparte. ConfiguracionGlobal solo tiene una fila.

Los usuarios no se borran, se desactivan con `activo`, para no perder la referencia en el log y en las facturas que crearon. Los clientes dados de baja se conservan (borrado lógico) y `fecha_baja` marca cuándo empieza a contar el plazo de conservación.

## Reglas y restricciones

La notación gráfica no valida nada por sí sola, así que dejamos por escrito qué se comprueba y dónde.

**En la propia base de datos (PK, FK, NOT NULL, UNIQUE, CHECK)**

- Claves primarias: email del usuario, NIF del cliente, `id_factura`, `id_transaccion` del pago (si la pasarela repite un evento, no se puede insertar dos veces), y las claves compuestas de Usuario_Cliente_Asignado y Cliente_Etiqueta.
- Factura: `UNIQUE (serie, numero)`. `nif_cliente` y `email_creador` son obligatorios.
- Cliente: `email_responsable` obligatorio.
- Valores cerrados con CHECK: rol, estado de cliente, idioma, tipo y estado de factura, estado de aprobación, estado de notificación interna, `tipo_entidad` y `tipo_elemento`.
- Valores por defecto: plazo de pago de 5 días, retención de 5 años y `activo = TRUE`.
- Configuracion_DiasAviso: `UNIQUE (id_configuracion, dia)`.
- Factura en borrador implica `numero` nulo, y al revés, una factura que no es borrador tiene número.
- Factura rectificativa implica `id_factura_original` no nulo.
- Factura extraordinaria implica `requiere_aprobacion = TRUE` y `estado_aprobacion` no nulo. Si `requiere_aprobacion` es falso, `estado_aprobacion` es nulo.
- Cliente en estado Baja implica `fecha_baja` no nula.

**Reglas que van en triggers o en la aplicación**

- Una factura condonada no tiene pagos, y una pagada tiene al menos uno.
- La serie de una rectificativa es distinta de la de su original (FR-20).
- Un cliente en baja no puede tener facturas pendientes ni vencidas (FR-14).
- Los clientes en pausa, con baja solicitada o de baja no generan facturas recurrentes, y los que tienen `factura_recurrente = FALSE` tampoco las generan de forma automática.
- Si el importe de un pago no coincide con el de la factura, la factura no pasa a Pagada: se crea una NotificacionInterna para revisión manual (FR-51, FR-61). Antes de marcar una factura como pagada se verifican sus datos (FR-48).
- Si un proceso automático falla y agota los reintentos (FR-96), se avisa a administradores y superadministradores con una NotificacionInterna y queda anotado en el log.
- Solo los usuarios con rol Lector o Administrador pueden estar en Usuario_Cliente_Asignado.
- Cada cliente debe tener al menos un contacto.
- Una factura emitida no se borra, y pasado el cierre del trimestre tampoco se modifica (RB-03, NFR-15). Los registros del log no se editan ni se eliminan (NFR-20).

## Trazabilidad

| Requisitos / casos | Tablas / columnas | Observación |
|---|---|---|
| CU-01, UR-04, RB-01, RB-02, NFR-10, NFR-11, NFR-12 | Usuario | Inicio de sesión. La contraseña solo se guarda con hash. El dominio del correo se valida en la aplicación. |
| CU-07, UR-03, NFR-10 | Usuario (`rol`, `activo`) | Altas, ediciones y desactivación de usuarios. |
| CU-08, UR-06, FR-01, FR-02, FR-03, FR-06 | Cliente, Contacto, Contacto_Email, Contacto_Telefono | Datos comerciales, fiscales y de contacto. |
| FR-07, FR-08, FR-42, FR-43, FR-45 | Cliente (`rango_facturacion_*`, `tipo_plan`, `factura_recurrente`) | Rango anual del que sale la cuota, y si el cliente se factura de forma recurrente o puntual. |
| FR-09, FR-10, FR-12, FR-13, FR-14, NFR-14 | Cliente (`estado`, `motivo_pausa`, `fecha_reactivacion_prevista`, `fecha_baja`) | Pausa y baja son estados distintos. La baja es un borrado lógico. |
| FR-11 | Cliente_Etiqueta | Etiquetas independientes del estado. |
| FR-17 | Cliente (`departamento`) | División de clientes por departamentos de TurbineH. |
| FR-15, NFR-13, RB-07 | Cliente (`email_responsable`), Usuario_Cliente_Asignado | Responsable de cada cliente y acceso del lector solo a sus clientes. |
| CU-03, UR-01, UR-08 | Factura | Creación, serie, número, estados y borradores. |
| FR-20 | Factura (`id_factura_original`, `serie`, `tipo`) | La rectificativa se relaciona con la original sin sustituirla. |
| FR-30 | Factura (`estado`) | El estado de pago se elige entre opciones fijas. |
| FR-37 | Factura (`idioma`) | Facturas en varios idiomas. |
| FR-38 | Factura (`tipo`, `requiere_aprobacion`, `estado_aprobacion`) | Facturas extraordinarias con revisión manual. |
| FR-52, FR-53 | Factura (`estado`: Incobrable, Condonada) | Estados propios, distintos de Pagada. |
| CU-02, UR-02, UR-07 | Factura, Pago, Cliente | Historial de facturas por cliente. |
| FR-47, FR-48, FR-51 | Pago | Pagos de la pasarela simulada. `id_transaccion` evita duplicados. |
| CU-10, UR-05 | ConfiguracionGlobal, Configuracion_DiasAviso | Plazo de pago, formato de serie, retención, idioma y días de aviso. |
| FR-61, FR-63 | NotificacionInterna | Bandeja de tareas e incidencias del usuario. |
| CU-09, UR-07, FR-95, FR-96, NFR-20 | LogActividad | Historial de modificaciones, notificaciones a clientes y fallos de procesos automáticos. |
| CU-06, UR-09, FR-86, FR-87, FR-89 | Consultas sobre Factura y Pago | El dashboard no tiene tablas propias. |

## Decisiones abiertas

- **Notificaciones a clientes.** No tienen tabla propia. Cada envío se anota en LogActividad (`accion = 'NotificacionCliente'`, `tipo_entidad = 'Factura'`, y el destinatario, canal y estado en `detalle_modificacion`). Una tabla aparte sería más cómoda para consultar canales y reintentos, pero implicaba añadir una clase nueva al diagrama de clases.
- **Cliente recurrente o puntual.** `factura_recurrente` indica cómo se factura al cliente por defecto, pero no impide que un cliente recurrente reciba una factura puntual o extraordinaria. Eso lo decide `Factura.tipo`.
- **Pagos sin factura identificada.** `Pago.id_factura` es obligatorio. Un pago que no se pueda asociar no se inserta: genera una NotificacionInterna y una entrada en el log para revisarlo a mano. La alternativa era dejar `id_factura` como opcional.
- **Cuota anual inmutable (FR-43).** Si un cliente cambia de rango a mitad de año, la cuota ya facturada se mantiene. No guardamos histórico de rangos, queda reflejado en el importe de las facturas ya emitidas.
- **Tabla de rangos de facturación.** El modelo guarda el rango de cada cliente, pero no una tabla con los rangos y sus cuotas. Pendiente de confirmar con el cliente si el superadministrador debe poder configurarla.
- **Descuentos y precios pactados (FR-45).** No tienen entidad propia. Suponemos que se reflejan en el importe de una factura extraordinaria sujeta a aprobación.
- **Quién aprueba una factura extraordinaria.** No se guarda en Factura, se puede saber por el log. Se podría añadir `email_aprobador` si hace falta.
- **Claves naturales.** Usuario usa su email y Cliente su NIF como clave primaria. Si alguno cambia hay que propagarlo con `ON UPDATE CASCADE`. Preferimos esto a claves artificiales para no complicar el modelo.
- **Tipos de datos.** La divisa es un código ISO 4217 (por ejemplo EUR) y los importes son `DECIMAL(10,2)`.
