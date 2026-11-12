# Especificación de casos de uso

> **Instrucciones de uso**
>
> Para cada caso de uso representado en el diagrama de casos de uso deberá incluirse una especificación textual siguiendo esta plantilla.
>
> La descripción debe ser coherente con el diagrama y permitir comprender qué persigue el caso de uso, quién participa en él, en qué condiciones puede ejecutarse y cuál es el comportamiento esperado del sistema.
>
> Los comentarios incluidos entre `<!-- ... -->` sirven únicamente como ayuda y pueden eliminarse en la versión final de la entrega.

---

## CU-XX — Nombre del caso de uso

<!--
Ejemplo:
CU-01 — Realizar pedido
-->

### 1. Identificación

| Campo | Descripción |
|---|---|
| **Identificador** | CU-XX |
| **Nombre** | Nombre del caso de uso |
| **Objetivo** | Describir brevemente qué pretende conseguir el actor mediante este caso de uso. |
| **Actores participantes** | Actor principal y, si corresponde, otros actores que intervienen. |

<!--
Ejemplo:

| **Identificador** | CU-01 |
| **Nombre** | Realizar pedido |
| **Objetivo** | Permitir que un cliente seleccione productos y confirme su compra. |
| **Actores participantes** | Cliente (principal), Pasarela de pago |
-->

---

### 2. Precondiciones

<!--
Indicar las condiciones que deben cumplirse antes de que pueda comenzar el caso de uso.

Las precondiciones describen el estado previo del sistema o del entorno.
No deben confundirse con los pasos del flujo principal.

Ejemplos:
- El cliente está registrado en el sistema.
- El cliente ha iniciado sesión.
- El carrito contiene al menos un producto.
-->

- PRE-1. ...
- PRE-2. ...

---

### 3. Flujo principal

<!--
Describir la secuencia normal de interacción entre los actores y el sistema cuando todo ocurre según lo esperado.

Se recomienda:
- Numerar los pasos.
- Indicar claramente quién realiza cada acción.
- Mantener un nivel de abstracción adecuado: describir QUÉ ocurre, no detalles de interfaz o implementación salvo que sean relevantes para el requisito.

Ejemplo:
1. El cliente solicita iniciar el proceso de compra.
2. El sistema muestra un resumen del pedido.
3. El cliente confirma los datos de entrega.
4. El sistema solicita los datos necesarios para realizar el pago.
5. El cliente confirma el pago.
6. El sistema registra el pedido.
7. El sistema confirma al cliente que el pedido ha sido realizado.
-->

1. **Actor:** ...
2. **Sistema:** ...
3. **Actor:** ...
4. **Sistema:** ...
5. ...

---

### 4. Flujos alternativos

<!--
Los flujos alternativos describen variantes válidas del comportamiento normal.

Cada flujo alternativo debe indicar:
- En qué punto del flujo principal puede producirse.
- Qué condición lo provoca.
- Qué pasos se realizan.
- Si el flujo regresa al flujo principal y, en ese caso, a qué paso.

No son errores: representan formas alternativas válidas de completar o continuar el caso de uso.

Ejemplo:

#### FA-1 — Utilizar una dirección de entrega diferente

**Se produce en:** paso 3 del flujo principal.

1. El cliente indica que desea utilizar otra dirección.
2. El sistema solicita los datos de la nueva dirección.
3. El cliente introduce la nueva dirección.
4. El sistema valida los datos.
5. El caso de uso continúa en el paso 4 del flujo principal.
-->

#### FA-1 — Nombre del flujo alternativo

**Se produce en:** paso X del flujo principal.  
**Condición:** ...

1. ...
2. ...
3. ...

**Continuación:** el caso de uso continúa en el paso X del flujo principal.

<!-- Añadir tantos flujos alternativos como sean necesarios. -->

---

### 5. Flujos de excepción

<!--
Incluir esta sección cuando existan situaciones anómalas o errores relevantes que impidan continuar normalmente el caso de uso.

Cada excepción debería indicar:
- En qué punto puede producirse.
- Qué provoca la excepción.
- Cómo responde el sistema.
- Cómo termina o continúa el caso de uso.

Ejemplo:

#### FE-1 — Pago rechazado

**Se produce en:** paso 5 del flujo principal.

1. La pasarela de pago informa de que el pago ha sido rechazado.
2. El sistema informa al cliente de que no ha sido posible completar el pago.
3. El sistema no registra el pedido.
4. El cliente puede intentar realizar nuevamente el pago.

El caso de uso regresa al paso 4 del flujo principal.
-->

#### FE-1 — Nombre de la excepción

**Se produce en:** paso X del flujo principal.  
**Condición:** ...

1. ...
2. ...
3. ...

**Resultado:** ...

<!-- Añadir tantos flujos de excepción como sean necesarios. Si no existen, puede indicarse "No se identifican flujos de excepción relevantes". -->

---

### 6. Postcondiciones

<!--
Describir las condiciones que deben cumplirse después de la finalización del caso de uso.

Las postcondiciones deben expresar el estado observable del sistema tras completar el caso de uso, no las acciones realizadas para llegar hasta él.

Ejemplos:
- El pedido queda registrado en el sistema.
- El pedido queda asociado al cliente.
- El stock de los productos queda actualizado.
-->

- POST-1. ...
- POST-2. ...

---

## Resumen de la estructura

Cada caso de uso debe contener, como mínimo:

- **Nombre del caso de uso**
- **Identificador**
- **Objetivo**
- **Actor o actores participantes**
- **Precondiciones**
- **Flujo principal**
- **Flujos alternativos**
- **Flujos de excepción**, cuando corresponda
- **Postcondiciones**