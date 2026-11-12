# Diagrama UML de clases

## Alcance
Ejemplo: estructura estática mínima para la gestión de pedidos.

## Decisiones de diseño
- `Pedido` agrupa `LineaPedido` mediante **composición**, porque una línea pertenece al ciclo de vida de un pedido (hipótesis del ejemplo).
- `Cliente` mantiene una asociación con múltiples pedidos.
- Las operaciones ilustran responsabilidades, no son firmas finales de implementación.

## Trazabilidad
| Requisito / CU | Clases y operaciones | Motivo |
|---|---|---|
| RF-01, CU-01 | `Pedido.agregarLinea`, `Pedido.confirmar`, `LineaPedido` | Gestión y validación del pedido. |

## Consistencia con otros modelos
Explicar correspondencias de conceptos con tablas del ER; no asumir que toda clase implica una tabla.
