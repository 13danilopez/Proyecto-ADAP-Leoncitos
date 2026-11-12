# Modelo físico de datos

## Alcance
Ejemplo: almacenar clientes, pedidos y líneas de pedido para dar soporte al caso de uso CU-01.

## Justificación del modelo
- `CLIENTE (1)` — `PEDIDO (0..N)`: un cliente puede no tener pedidos; cada pedido corresponde a un cliente.
- `PEDIDO (1)` — `LINEA_PEDIDO (1..N)`: todo pedido confirmado tiene al menos una línea (regla de ejemplo).
- `id_cliente` e `id_pedido` actúan como claves foráneas donde corresponda.

## Reglas y restricciones
Ejemplo: `cantidad > 0`; `LINEA_PEDIDO.id_pedido` referencia `PEDIDO.id_pedido`. **La notación gráfica no valida automáticamente las restricciones**: describid las que falten.

## Trazabilidad
| Requisitos/casos | Tablas/columnas | Observación |
|---|---|---|
| RF-01, CU-01 | CLIENTE, PEDIDO, LINEA_PEDIDO | Persistencia del pedido y sus líneas. |

## Decisiones abiertas
Indicar aquí supuestos, alternativas descartadas o preguntas al cliente.
