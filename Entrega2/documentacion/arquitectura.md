# Arquitectura y componentes

## Vista representada

Vista de despliegue: unidades de software ejecutables, los nodos donde se ejecutan y los protocolos entre ellos. La organización interna de cada componente está en el diagrama de clases (apartado 4).

---

## Responsabilidades

| Componente | Responsabilidad | Relaciones principales |
|---|---|---|
| Facturas_Cliente_Bundle *(navegador del usuario interno)* | Interfaz web: formularios, listados, filtros y dashboard. No contiene reglas de negocio ni accede a la base de datos | Frontend Build_Estatico, API Backend |
| Frontend Build_Estatico *(contenedor Docker)* | Sirve el bundle y el resto de recursos estáticos. Sin estado | Facturas_Cliente_Bundle |
| API Backend *(contenedor Docker)* | Reglas de negocio y casos de uso. Es el despliegue de `LeoncitosManager`: autenticación y permisos por rol (CU-01), gestión de usuarios y clientes (CU-07, CU-08), facturación (CU-03), dashboard (CU-06), configuración global (CU-10) y los procesos automáticos de cobro y reintentos (FR-96). Envía las notificaciones, tanto la bandeja interna (FR-61, FR-63) como los avisos al cliente, y escribe la traza en `LogActividad` (FR-95) | Facturas_Cliente_Bundle, Validador_Pago, SQL Base de Datos |
| SQL Base de Datos *(host propio)* | Persistencia del modelo del apartado 3. Aplica las claves, las restricciones `CHECK` y los disparadores descritos allí | API Backend |
| Validador_Pago *(pasarela externa simulada)* | Simulación de Stripe/Revolut (FR-47). Responde cuando la API consulta el estado de un cobro y notifica los pagos recibidos | API Backend |

---

## Justificación y alternativas

### Decisiones

- La interfaz se despliega como bundle estático y habla con la API por JSON sobre HTTPS. Se puede rehacer sin tocar la lógica de negocio, que es el objetivo de modularidad del apartado 1.
- Los permisos por rol se resuelven en el servidor (`isAllowed()`, `getRole()` en los diagramas de secuencia). El navegador no decide qué ve cada usuario (NFR-10, NFR-13).
- Frontend y API van en contenedores Docker: mismo entorno en desarrollo y producción, y escalable.
- La base de datos está en un host propio, fuera del ciclo de vida del contenedor, separada de la de TurbineH, como pide el alcance.
- La persistencia es relacional. El modelo del apartado 3 se apoya en integridad referencial, claves compuestas y restricciones `CHECK` que la propia base de datos comprueba.
- La pasarela es un componente externo. Crear una propia queda fuera del alcance, y tratarla como servicio ajeno acota a un solo punto la sustitución de la simulación por Stripe o Revolut reales.
- La API verifica cada pago antes de marcar la factura como pagada: comprueba sus datos (FR-48) y contrasta el importe (FR-51). Si no coincide, la factura no cambia de estado y se crea una `NotificacionInterna` (FR-61).
- Las notificaciones las envía la propia API. Las dispara el mismo proceso que emite facturas y concilia pagos, que es quien conoce el hecho que las origina.

### Restricciones y dependencias externas

- Disponibilidad de la pasarela y formato de sus notificaciones. Mientras esté simulada, ese contrato lo define el equipo.
- TLS 1.2 o superior hacia la pasarela.
- Puerto 5432/TCP entre el host del servidor y el de la base de datos.
- La pasarela debe poder alcanzar la API para confirmar los pagos.
- Correo saliente para los avisos al cliente. El servicio concreto se da por supuesto y no aparece como nodo.
- Red interna de TurbineH: la plataforma es de uso interno y los clientes no deben poder alcanzarla.

### Alternativas consideradas

- Servir la interfaz desde la propia API. Menos piezas que desplegar, pero acopla los despliegues de interfaz y lógica.
- Renderizado en servidor. Mejor primera carga, sin ventaja clara en una herramienta interna tras autenticación.
- Base de datos en el mismo host o contenedor que la API. Más simple y barato pero sin aislamiento de fallos y con riesgo de perder datos al recrear el contenedor.
- Almacén documental en lugar de relacional. Habría que sostener desde la aplicación todas las reglas que ahora comprueba la base de datos.
- Sondear la pasarela periódicamente en vez de recibir su confirmación. Más sencillo, pero retrasa la detección de cobros.
- Notificaciones como componente independiente. Aislaría los reintentos de FR-96, a cambio de una pieza más que desplegar y vigilar.

---

## Trazabilidad

| CU / Requisitos | Componentes que cooperan | Observación |
|---|---|---|
| CU-01, NFR-10, NFR-11 | Facturas_Cliente_Bundle → API Backend → SQL Base de Datos | Credenciales validadas en el servidor. El acceso, logrado o fallido, queda en `LogActividad`. |
| CU-02, FR-48, FR-51 | Facturas_Cliente_Bundle → API Backend → SQL Base de Datos; API Backend → Validador_Pago | La consulta del estado de pago contrasta el dato interno con el de la pasarela. |
| CU-03, FR-20, FR-37, FR-38 | Facturas_Cliente_Bundle → API Backend → SQL Base de Datos | Emisión, numeración y flujo de aprobación. El aviso de cobro sale de la API. |
| CU-04, FR-45 | Facturas_Cliente_Bundle → API Backend → SQL Base de Datos | |
| CU-05 | Facturas_Cliente_Bundle → Frontend Build_Estatico | La guía de uso es contenido estático y no altera el estado del sistema. |
| CU-06, FR-86, FR-87, FR-89 | Facturas_Cliente_Bundle → API Backend → SQL Base de Datos | El informe se calcula con consultas sobre Factura y Pago. |
| CU-07, CU-08 | Facturas_Cliente_Bundle → API Backend → SQL Base de Datos | La importación CSV la procesa la API. |
| CU-09, FR-95, NFR-20 | Facturas_Cliente_Bundle → API Backend → SQL Base de Datos | Los registros del log solo se leen. |
| CU-10 | Facturas_Cliente_Bundle → API Backend → SQL Base de Datos | |
| FR-47, FR-51, FR-61 | Validador_Pago → API Backend → SQL Base de Datos | Proceso automático de cobro, sin intervención del navegador. |
| FR-61, FR-63 | API Backend → SQL Base de Datos → Facturas_Cliente_Bundle | La bandeja se persiste como `NotificacionInterna` y el usuario la consulta desde la interfaz. |
| FR-96 | API Backend → SQL Base de Datos | Agotados los reintentos, la API crea la notificación interna y lo anota en el log. |

---

## Consistencia con otros modelos

- La API Backend es el despliegue de la clase de control `LeoncitosManager`.
- La SQL Base de Datos contiene las tablas del apartado 3. Las clases de entidad no se despliegan por separado: viven en el proceso de la API y se persisten aquí.
- El Validador_Pago es la `Entidad Bancaria` de CU-02 y el origen de los pagos de `procesarPagosSimulados`.
- El `Sistema Notificaciones` de CU-03 no tiene nodo propio. Es la responsabilidad de notificación de la API, que el diagrama de secuencia separa para que el flujo se lea mejor.
- Los tipos primitivos y las enumeraciones no se despliegan: son estructuras internas de la API y en la base de datos pasan a columnas y restricciones `CHECK`.
