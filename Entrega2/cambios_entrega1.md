# Cambios respecto a la Entrega 1

| Elemento | Cambio | Motivo | Impacto en Entrega 2 | Validación / pendiente |
|---|---|---|---|---|
| RF-01 | Aclarar que el pedido tiene al menos una línea (ejemplo). | El modelo necesitaba una cardinalidad definida. | ER, clases y CU-01. | Confirmar con el cliente si procede. |

**Instrucción:** distinguir cambios ya acordados de hipótesis pendientes. Actualizar las fuentes de `entrega1-actualizada/` en consecuencia.

### Especificación de Requisitos

Requisitos a añadir/cambiar:

- Añadir **facturas multi-idioma**
- Modificar **FR-40**: factura constante todo el año (no hay cambio de rango entre año)

### Casos de Uso y UML

- Hacer los **casos de uso** (se nos olvidó) siguiendo la plantilla "template_casos_uso" que se proporciona en la tarea de la Entrega 1 en el campus.

- **Más diagramas UML** -> diagrama general (el que ya tenemos) + otros especificados por "*módulos*" (similar al de los Perritos), que podríamos basar en las categorías de requisitos que tenemos para relacionarlos fácilmente.

### Informe Scrum

Incluir en el informe Scrum de esta entrega:

- Quién hizo qué tareas.
- Justificación de prioridad de tareas.
- Conexión con artefactos (que el proceso explique cómo se llegó al resultado final).

---

### Otras anotaciones

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

- Campo adicional de factura para indicar si está rectificando a una (relacionarla claramente) ó incluir en el propio formato del identificador (e.g. 178239203-1, donde el "-1" indica que es una primera rectificación sobre la original, 178239203).

- **Factura incobrable**/irresoluble, en casos especiales en los que por motivos legales (e.g. quiebra del cliente) una factura no vaya a poder ser pagada -> Turbine debe encargarse personalmente de contactar con la empresa.

- **Comprobación de pagos** con la pasarela simulada Stripe. ¿Qué hacer cuando haya pagos no identificados, incoherencia de datos o eventos repetidos? -> respuesta automática (e.g. descartar eventos repetidos) / mandar a revisión manual de admin/superadmin (pago no identificado/datos incoherentes en factura).

- **Notificación a usuarios del sistema** por impagos, incidencias, aprobaciones, revisiones... ¿Tener una **bandeja de notificaciones** para que vea sus tareas pendientes?

- **Fallos en procesos automatizados** -> probar un número determinado de reintentos y si sigue sin haber éxito, notificar a admins/superadmin.

