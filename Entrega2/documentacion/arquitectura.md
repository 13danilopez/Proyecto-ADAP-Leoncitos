# Arquitectura y componentes

## Vista representada
Ejemplo: arquitectura lógica en capas, **no** topología de despliegue.

## Responsabilidades
| Componente | Responsabilidad | Relaciones principales |
|---|---|---|
| Interfaz web | Entrada y presentación de datos | Servicio de pedidos |
| Servicio de pedidos | Casos de uso y reglas de negocio | Repositorio |
| Repositorio de pedidos | Acceso a persistencia | Base de datos |

## Justificación y alternativas
Ejemplo: separar interfaz y lógica de negocio facilita cambios en la interfaz. Describir otras decisiones pertinentes, restricciones, dependencias externas y alternativas.

## Trazabilidad
`CU-01` se realiza mediante la cooperación de Interfaz web, Servicio de pedidos, Repositorio de pedidos y Base de datos.
