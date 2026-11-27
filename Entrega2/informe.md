# Entrega 2 · Diseño del sistema

**Asignatura:** Análisis y Diseño de Aplicaciones · Grupo A · 2026–27  
**Equipo:** Leoncitos
**Integrantes:** Daniel Delgado, Wail Amchi El Haskouri, José Javier Jimenez, Daniel López, Jesús García, Fernando Montero
**Fecha:** 27/11/2026
**Repositorio:** https://github.com/13danilopez/Proyecto-ADAP-Leoncitos

> **Plantilla de índice para componer el PDF manualmente.** Sustituid el texto de ejemplo por vuestro trabajo e incorporad manualmente los diagramas renderizados y el contenido de los archivos de documentación. El archivo Markdown no se transforma automáticamente en PDF.

---

## 1. Introducción y alcance

> Nuestro objetivo general planteado para este proyecto es el siguiente:

- **Objetivo**: Generar una plataforma simple, ágil e intuitiva que automatice la gestión de pagos y cobros de la empresa. El enfoque de la plataforma debe ser de uso exclusivo interno e independiente de BrainOS, y los clientes no deben tener acceso a ella. Se hace especial énfasis en garantizar la fiabilidad de los procesos automáticos (facturación, seguimiento de cobros), la trazabilidad de las modificaciones realizadas ("historial"), y la modularidad de la implementación software para facilitar futuras ampliaciones.

> Declaramos los siguientes aspectos como incluidos/excluidos del alcance de este proyecto:

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

---

## 2. Actualizaciones de la Entrega 1

> Los principales cambios del diseño de nuestro sistema se ha visto en la actualización y ampliación de los **requisitos funcionales** y **no funcionales**.

> Columna **N/M** indica si el requisito es "***Nuevo***" o "***Modificado***".

### Actualización de requisitos

#### a) Requisitos funcionales

| ID    | N/M | Cambio                                                                                                        | Motivo                                                                                                | Impacto en Entrega 2 | Validación / pendiente               |
| ----- | --- | ------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------- | -------------------- | ------------------------------------ |
| FR-13 | M   | Contemplar además de las bajas de clientes, las pausas de las suscripciones de su cuenta                      | Funcionalidad que se pedía en la transcripción                                                        | ER y clases          | Confirmar con el cliente si procede. |
| FR-14 | M   | Definición de más concreta de cómo actúa el sistema ante bajas teniendo aún impagos                           | Evitar ambigüedad, definición vaga                                                                    | ER, clases y CU-08   | Confirmado.                          |
| FR-17 | N   | Contemplar la división y asignación de clientes por departamentos de la empresa TurbineH                      | Funcionalidad que se pedía en la transcripción                                                        | Clases y CU-08       | Confirmado.                          |
| FR-20 | M   | Indicar que una factura rectificativa no elimina ni sustituye la original. Se usan números de serie distintos | Evitar ambigüedad, definición vaga                                                                    | CU-03                | Confirmado.                          |
| FR-30 | M   | Especificar que la asignación de estado de pago de una factura se hace mediante un menú con opciones          | Desacoplar error humano para que no se tenga que indicar a mano el estado                             | CU-03                | Confirmado.                          |
| FR-37 | N   | Contemplar facturas multi-idioma                                                                              | Funcionalidad que se pedía en la transcripción                                                        | Clases y CU-03       | Confirmado.                          |
| FR-38 | N   | Contemplar facturas extraordinarias                                                                           | Funcionalidad que se pedía en la transcripción                                                        | Clases y CU-03       | Confirmado.                          |
| FR-42 | M   | Tener en cuenta impacto de descuentos extraordinarios en cuotas de facturación de clientes                    | Mantener coherencia con otros requisitos                                                              | -                    | Confirmar con el cliente si procede. |
| FR-43 | M   | Inmutabilidad de cuotas anuales de clientes incluso si cambian de rango entre medias                          | Funcionalidad que se pedía en la transcripción                                                        | -                    | Confirmado.                          |
| FR-45 | N   | Contemplar descuentos extraordinarios y precios pactados por la empresa                                       | Funcionalidad que se pedía en la transcripción                                                        | -                    | Confirmado.                          |
| FR-47 | M   | Aclaración de implementación de pasarela Stripe/Revolut como simulada                                         | Evitar ambigüedad, definición vaga                                                                    | Clases y CU-02       | Confirmado.                          |
| FR-48 | M   | Comprobaciones de verificación de facturas antes de marcar como pagada                                        | Añadir verificación de datos de facturas para garantizar su actualización automática correcta         | CU-02                | Confirmar con el cliente si procede. |
| FR-51 | N   | Acción ante incoherencia de importe de pago de facturas                                                       | Especificar cómo actúa el sistema si se registra un pago con importe distinto al que lista su factura | Arquitectura         | Confirmar con el cliente si procede. |
| FR-52 | N   | Contemplar deudas incobrables                                                                                 | Funcionalidad que se pedía en la transcripción                                                        | Clases y CU-06       | Confirmado.                          |
| FR-53 | N   | Contemplar condonaciones de deudas                                                                            | Funcionalidad que se pedía en la transcripción                                                        | Clases y CU-06       | Confirmado.                          |
| FR-61 | M   | Tener en cuenta notificaciones a usuarios del sistema en caso de incoherencia de pagos de facturas            | Mantener coherencia con otros requisitos                                                              | Arquitectura y CU-02 | Confirmar con el cliente si procede. |
| FR-63 | N   | Bandeja de notificaciones de usuarios                                                                         | Proporcionar forma de consulta de solicitudes e incidencias de sus clientes asignados                 | Arquitectura         | Confirmar con el cliente si procede. |
| FR-86 | M   | Especificar las funcionalidades del dashboard financiero                                                      | Evitar ambigüedad, definición vaga                                                                    | Clases y CU-06       | Confirmar con el cliente si procede. |
| FR-87 | M   | Especificar reporte de pagos condonados e incobrables en el dashboard                                         | Mantener coherencia con otros requisitos                                                              | Clases y CU-06       | Confirmar con el cliente si procede. |
| FR-89 | N   | Funciones de búsqueda filtrada del dashboard                                                                  | Proporcionar forma de consulta eficaz                                                                 | Clases y CU-06       | Confirmar con el cliente si procede. |
| FR-95 | N   | Sistema de logs                                                                                               | Trazabilidad de acciones de usuarios dentro del sistema                                               | Arquitectura         | Confirmar con el cliente si procede. |
| FR-96 | N   | Flujo de reintentos automáticos del sistema ante fallos recuperables (hasta un máximo establecido)            | Dotar al sistema de una cierta capacidad de resistencia ante errores                                  | Clases               | Confirmar con el cliente si procede. |

#### b) Requisitos no funcionales

| ID     | N/M | Cambio                                                                                                              | Motivo                                                                      | Impacto en Entrega 2 | Validación / pendiente               |
| ------ | --- | ------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------- | -------------------- | ------------------------------------ |
| NFR-04 | M   | Especificar características y capacidades de la interfaz web                                                        | Evitar ambigüedad, definición vaga                                          | -                    | Confirmado.                          |
| NFR-08 | M   | Especificación de mecanismos automáticos fiables y  errores detectables                                             | Evitar ambigüedad, definición vaga                                          | -                    | Confirmar con el cliente si procede. |
| NFR-09 | M   | Especificación de sistema de notificaciones fiable y errores detectables                                            | Evitar ambigüedad, definición vaga                                          | -                    | Confirmar con el cliente si procede. |
| NFR-10 | M   | Especificación de niveles de permiso y flujo de acceso controlado del sistema                                       | Evitar ambigüedad, definición vaga                                          | -                    | Confirmado.                          |
| NFR-13 | M   | Limitación de acceso a datos personales de clientes a los usuarios adecuados                                        | Aclarar restricción de acceso a datos de clientes                           | -                    | Confirmado.                          |
| NFR-14 | M   | Conservación temporal de objetos del sistema (clientes, usuarios y facturas) acordes al marco legal correspondiente | Aclarar la conservación de información del sistema según la normativa legal | -                    | Confirmado.                          |

#### c) Demás requisitos

> El resto de apartados de los requisitos (ej: 6. Suposiciones y 8. Trazabilidad) han sido modificados únicamente para que los nuevos IDs de los requisitos funcionales/no-funcionales sean coherentes con la actualización.

### Especificaciones de casos de uso

> Ausentes en la primera entrega debido a un despiste del equipo. Añadidas a la actualización de la primera entrega debidamente, acordes con el diagrama de casos de uso general que sí se presentó.

| ID    | N/M | Cambio                                           | Motivo               | Impacto en Entrega 2                  | Validación / pendiente               |
| ----- | --- | ------------------------------------------------ | -------------------- | ------------------------------------- | ------------------------------------ |
| CU-01 | N   | Autenticación de usuario                         | Ausente en entrega 1 | ER, clases, secuencias y arquitectura | Confirmar con el cliente si procede. |
| CU-02 | N   | Consulta del historial de facturas               | Ausente en entrega 1 | ER, clases, secuencias y arquitectura | Confirmar con el cliente si procede. |
| CU-03 | N   | Generación de facturas                           | Ausente en entrega 1 | ER, clases, secuencias y arquitectura | Confirmar con el cliente si procede. |
| CU-04 | N   | Modificación de preferencias de pagos            | Ausente en entrega 1 | ER, clases, secuencias y arquitectura | Confirmar con el cliente si procede. |
| CU-05 | N   | Consulta de documentación de uso del sistema     | Ausente en entrega 1 | ER, clases, secuencias y arquitectura | Confirmar con el cliente si procede. |
| CU-06 | N   | Acceso al dashboard de facturación               | Ausente en entrega 1 | ER, clases, secuencias y arquitectura | Confirmar con el cliente si procede. |
| CU-07 | N   | Gestión de usuarios del sistema                  | Ausente en entrega 1 | ER, clases, secuencias y arquitectura | Confirmar con el cliente si procede. |
| CU-08 | N   | Gestión de clientes                              | Ausente en entrega 1 | ER, clases, secuencias y arquitectura | Confirmar con el cliente si procede. |
| CU-09 | N   | Consulta de historial de modificaciones          | Ausente en entrega 1 | ER, clases, secuencias y arquitectura | Confirmar con el cliente si procede. |
| CU-10 | N   | Modificación de configuración global del sistema | Ausente en entrega 1 | ER, clases, secuencias y arquitectura | Confirmar con el cliente si procede. |

### Anotaciones originales

> Anotaciones que tomó el equipo tras la primera entrega, en la que basamos la mayoría de cambios listados arriba.

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

- Facturas multi-idioma.

- Campo adicional de factura para indicar si está rectificando a una (relacionarla claramente) ó incluir en el propio formato del identificador (e.g. 178239203-1, donde el "-1" indica que es una primera rectificación sobre la original, 178239203).

- **Factura incobrable**/irresoluble, en casos especiales en los que por motivos legales (e.g. quiebra del cliente) una factura no vaya a poder ser pagada -> Turbine debe encargarse personalmente de contactar con la empresa.

- **Comprobación de pagos** con la pasarela simulada Stripe. ¿Qué hacer cuando haya pagos no identificados, incoherencia de datos o eventos repetidos? -> respuesta automática (e.g. descartar eventos repetidos) / mandar a revisión manual de admin/superadmin (pago no identificado/datos incoherentes en factura).

- **Notificación a usuarios del sistema** por impagos, incidencias, aprobaciones, revisiones... ¿Tener una **bandeja de notificaciones** para que vea sus tareas pendientes?

- **Fallos en procesos automatizados** -> probar un número determinado de reintentos y si sigue sin haber éxito, notificar a admins/superadmin.


---

## 3. Modelo físico de datos (Entidad–Relación)

Insertad aquí el diagrama exportado de `diagramas/modelo_datos.puml` y la explicación de `documentacion/modelo_datos.md`. Incluid relaciones, PK/FK y decisiones relevantes.



---

## 4. Diagrama UML de clases

Insertad el diagrama exportado de `diagramas/diagrama_clases.puml` y la explicación de `documentacion/diagrama_clases.md`.



---

## 5. Diagramas UML de secuencia

Insertad los diagramas exportados de `diagramas/secuencias/` y la explicación de `documentacion/diagramas_secuencia.md`. Identificad el caso de uso y el escenario que cubre cada uno.



---

## 6. Arquitectura y componentes

Insertad el diagrama exportado de `diagramas/arquitectura.puml` y la explicación de `documentacion/arquitectura.md`.




---

## 7. Informe Scrum

## Identificación del equipo

| Luis San Juan          | Miembro del equipo |
| ---------------------- | ------------------ |
| Daniel Delgado         | Scrum Máster       |
| Wail Amchi El Haskouri | Product Owner      |
| José Javier Jimenez    | Miembro del equipo |
| Daniel López           | Miembro del equipo |
| Jesús García           | Miembro del equipo |
| Fernando Montero       | Miembro del equipo |

## Reuniones del periodo

| Fecha      | Inicio | Duración            | Participantes                                                        | Temas y acuerdos                                                                                                                                                                                                                         |
| ---------- | ------ | ------------------- | -------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 05/11/2026 | 8:45   | 1 hora y 45 minutos | - Luis<br>- Daniel L. <br>- Daniel D.<br>- José<br>- Wail<br>- Jesús | Organización de los puntos del trabajo a realizar, inicio de los diagramas de secuencia pero por consejo del profesorado lo dejaremos para más adelante.                                                                                 |
| 10/11/2026 | 12:30  | 30 min              | - Luis<br>- Daniel D.<br>- Jesús<br>- Wail                           | Reunión para la revisión del trabajo con Jose Luis Subirats.                                                                                                                                                                             |
| 12/11/2026 | 8:45   | 1 hora y 45 minutos | - Luis<br>- Daniel L.<br>- Daniel D.<br>- José<br>- Wail<br>- Jesús  | Realizacion de cada parte por los miembros del grupo correspondientes (Corrección de la entrega 1 e inicio de los diagramas de clase) y planteamiento de una futura reunión para aclarar dudas y afianzar más la gestión de esta entrega |
| 19/11/2026 | 8:45   | 1 hora y 45 minutos | - Luis<br>- Daniel L.<br>- José<br>- Wail                            | Continuación con tareas repartidas sobre elaboración de diagramas.                                                                                                                                                                       |
| 24/11/2026 | 20:00  | 1 hora              | - Luis<br>- Daniel L.<br>- Daniel D.<br>- José<br>- Wail<br>- Jesús  | Avances finales sobre diagramas de la Entrega 2: arquitectura, secuencias, clases, y entidad-relación.                                                                                                                                   |
| 26/11/2026 | 8:45   | 1 hora y 45 minutos | - Luis<br>- Daniel L.<br>- Daniel D.<br>- José<br>- Wail<br>- Jesús  | Retoques finales de coherencia y elaboración de informes y documentación de diagramas.                                                                                                                                                   |
 
## Estado del product backlog y Sprint backlog

**Tablero:** [sustituir por URL real](https://example.org/REEMPLAZAR).

Añadir **capturas reales legibles** del estado del tablero en Sprint Planning y Sprint Retrospective. Copiar las imágenes a `entrega2/evidencias/` y enlazarlas así (descomentar con archivos existentes):

#### Estado del tablero en el Sprint Planning

| Estado                               | Tareas                                                                                                                                                                                 |
| ------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Tareas pendientes de la Entrega      | - Actualizar infrome Scrum de la Entrega 2.<br>- Corrección de la Entrega 1.<br>- Realización de los diagramas de clase.<br>- Diagrama de Arquitectura.                                |
| Tareas omitidas del Sprint actual    | - Diagrama de secuencia.<br>- Sintetización de conclusiones y referencias.                                                                                                             |
| En progreso                          | - Informe Scrum.<br>- Identificación y documentación de requisitos.                                                                                                                    |
| En revisión                          | ...                                                                                                                                                                                    |
| Completado                           | ...                                                                                                                                                                                    |

#### Estado del tablero en la Sprint Retrospective

| Qué hacer  | Qué hemos hecho                                                                                                                                                                                                                                                                    |
| ---------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Mantener   | - La creación y uso del repositorio de GitHub para facilitar la contribución del equipo en cualquier momento.<br>- La alta tasa de tareas completadas durante las sesiones.<br>- El trabajo individual y su posterior puesta en común para dar con nuevas ideas y puntos de vista. |
| Mejorar    | - La definición y estructuración de los requisitos (tanto funcionales como no funcionales).<br>- La resolución de dudas para definir el alcance exacto del sistema de facturación.                                                                                                 |
| Cambiar    | - Completar la asignación de roles al inicio, ya que fue la única tarea planificada que no se finalizó.<br>- Asegurar la disponibilidad de transcripciones completas de las charlas antes de documentar, para evitar que falten especificaciones.                                  |

## Información de tareas

| Tarea                                                  | Responsable       | Milestone             | Prioridad | Etiqueta     | Descripción / estado                                                            |
| ------------------------------------------------------ | ----------------- | --------------------- | --------- | ------------ | ------------------------------------------------------------------------------- |
| Especificación de casos de uso                         | Daniel L. y Wail  | Entrega 1 actualizada | Alta      | casos de uso | Añadir casos de uso que faltaron en la 1a entrega                               |
| Actualización de requisitos                            | Jesús y Daniel L. | Entrega 1 actualizada | Alta      | requisitos   | Actualizar requisitos en función de los informes de feedback tras la 1a entrega |
| Informe Scrum                                          | Luis              | Entrega 2             | Media     | scrum        | Mantener actualizado el informe Scrum de esta entrega                           |
| Diagrama de arquitectura                               | Daniel D.         | Entrega 2             | Media     | diagramas    | Elaborar diagrama de arquitectura y documentarlo                                |
| Diagramas de clase                                     | Wail y Jesús      | Entrega 2             | Media     | diagramas    | Elaborar diagrama de clases y documentarlo                                      |
| Diagramas de secuencia (1-5)                           | Luis              | Entrega 2             | Media     | diagramas    | Elaborar diagramas de secuencias 1 a 5 y documentarlos                          |
| Diagramas de secuencia (6-10)                          | José              | Entrega 2             | Media     | diagramas    | Elaborar diagramas de secuencias 6 a 10 y documentarlos                         |
| Diagrama E/R                                           | Jesús             | Entrega 2             | Media     | diagramas    | Elaborar diagramas entidad-relación y documentarlo                              |
| Restructuración y mantenimiento del repositorio GitHub | Daniel L.         | Entrega 2             | Media     | repositorio  | Restructurar el repo según la plantilla proporcionada para la 2da entrega       |

## Problemas y riesgos

| Sprint   | Problema/riesgo                                                               | Impacto                               | Medida aplicada                                                 | Estado                              |
| -------- | ----------------------------------------------------------------------------- | ------------------------------------- | --------------------------------------------------------------- | ----------------------------------- |
| Sprint 1 | Actualización de requisitos                                                   | Retraso en tareas de Entrega 2        | Repaso del feedback proporcionado                               | Terminado                           |
| Sprint 1 | Especificaciones de casos de uso                                              | Retraso en tareas de Entrega 2        | Realización de las especificaciones                             | Terminado                           |
| Sprint 2 | Diagramas de secuencia en formato `.mdj` (`.puml` requerido)                  | Requisito necesario para la Entrega 2 | Pudimos pasarlos a PlantUML empleando extensiones y ayuda de IA | Terminado                           |
| Sprint 2 | Confusión sobre qué evidencias presentar exactamente en `Entrega2/evidencias` | Duda no resuelta para la Entrega 2    | Presentar capturas de commits y branches de GitKraken y GitHub  | Confirmación pendiente del profesor |

## Revisión y retrospectiva

- **Logros:**
	- Primera definición de funciones y métodos del sistema para el diagrama de clases.
	- Reuniones fuera de horario académico.
	- Actualización mediante revisión exhaustiva de documentos de la Entrega 1.

- **Aspectos positivos:**
	- Contraste de decisiones del equipo con feedback de profesores y del cliente.
	- Revisión de especificaciones de casos de uso para asegurar coherencia con requisitos y demás características del sistema ya definidas.
	- Mantenimiento de la plantilla de entrega proporcionada en el repositorio.
	- Reparto/división eficiente de tareas.

- **Mejoras:**
	- Requisitos más coherentes y específicos.
	- Especificaciones de casos de uso incluidas (faltaban en la Entrega 1).
	- Trazabilidad de errores del sistema.

- **Acciones siguientes:**
	- Repaso de diagramas de Entrega2 con el feedback proporcionado.
	- Tomar nota de las validaciones/objeciones del cliente (Alejandro) con respecto a nuestro trabajo realizado.


---

## 8. Conclusiones y referencias (si proceden)

Sintetizad las decisiones más importantes y las cuestiones pendientes. Revisad que los nombres e identificadores sean consistentes en todo el documento.

