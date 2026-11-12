# Diagramas UML de secuencia

## Selección de escenarios
Ejemplo mínimo: `CU-01`, flujo principal y excepción de falta de disponibilidad. El equipo debe elegir **los escenarios relevantes** de sus casos de uso; pueden ser necesarios varios diagramas.

## Descripción de CU-01
1. El cliente confirma los productos seleccionados en la interfaz.
2. El servicio valida las líneas.
3. Si son válidas, se guarda el pedido; de lo contrario, se comunica el error.

## Trazabilidad
| Diagrama | Caso de uso | Participantes / responsabilidades |
|---|---|---|
| `CU-01.puml` | CU-01 | Interfaz, ServicioPedidos, RepositorioPedidos. |

## Consistencia
Los mensajes y participantes deben ser compatibles con los diagramas de clases y arquitectura; justificar las correspondencias o diferencias de nivel de abstracción.
