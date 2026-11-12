# Análisis y Diseño de Aplicaciones · Grupo A · 2026–27

## Paquete de trabajo para la Entrega 2: Diseño del sistema

**Fecha prevista según guía operativa v1.0: 27/11/2026.**

Este repositorio contiene **plantillas y ejemplos ficticios mínimos**, no una solución al caso Turbine. Reemplazad los ejemplos por vuestro trabajo. Los códigos `RF-01` y `CU-01` son ilustrativos: emplead los identificadores reales de vuestra Entrega 1.

## ¿Qué va en cada carpeta?

| Ruta | Contenido |
|---|---|
| `entrega1-actualizada/fuentes/` | Fuentes corregidas y actualizadas de la primera entrega: requisitos, casos de uso, diagrama y Scrum de esa etapa. |
| `entrega1-actualizada/informe/` | PDF consolidado de la primera entrega actualizada, elaborado manualmente por el equipo. |
| `entrega2/diagramas/` | **Fuentes PlantUML** del modelo de datos, clases, secuencias y componentes. |
| `entrega2/documentacion/` | Explicaciones de cada modelo: decisiones, supuestos, alternativas y trazabilidad. |
| `entrega2/informe_scrum.md` | Informe Scrum de **esta** entrega (reuniones, evidencias del tablero, riesgos, retrospectiva). |
| `entrega2/cambios_entrega1.md` | Cambios desde la primera entrega y consecuencias de diseño. |
| `entrega2/informe.md` | Portada, introducción y orden de capítulos del informe consolidado. |
| `informe-final/` | PDF consolidado de la **segunda** entrega. |

## Procedimiento recomendado

1. **Actualizad Entrega 1**: corregid requisitos y casos de uso, manteniendo identificadores estables; documentad los cambios.
2. **Editad las fuentes `.puml`**: cada diagrama debe representar vuestro sistema, no los ejemplos ficticios. Añadid más secuencias cuando sea necesario.
3. **Explicad cada diagrama en `.md`**: alcance, relación con RF/CU, decisiones y supuestos. PlantUML permite `note`, pero la explicación extensa debe ir en Markdown.
4. **Completad `entrega2/informe_scrum.md`**: sustituid las referencias a capturas por imágenes reales y verificad que sean legibles.
5. **Preparad manualmente los informes PDF**: renderizad o exportad cada diagrama en PlantUML, insertadlo junto a sus explicaciones en el documento que utilicéis (por ejemplo, un procesador de texto) y exportad el resultado a PDF. Revisad la legibilidad y el contenido final. El informe de Entrega 1 actualizada y el de Entrega 2 son PDFs distintos.
6. **Subid fuentes, PDFs y evidencias al repositorio Git**. No subáis únicamente PDFs.
7. **Realizad la evaluación interna** con el procedimiento oficial del profesorado (método de las bolas); este paquete no sustituye sus instrucciones.

## Preparación manual de los informes PDF

**Este paquete no incluye scripts ni mecanismos de compilación automática.** Cada equipo es responsable de preparar y revisar manualmente los dos documentos PDF.

1. Abrir cada archivo `.puml` con PlantUML (aplicación, complemento del editor o editor en línea), comprobar que el diagrama se visualiza correctamente y exportarlo como imagen (preferentemente SVG o PNG de buena resolución).
2. Completar los textos `.md` con explicaciones, decisiones de diseño y referencias a requisitos/casos de uso. Pueden copiarse o incorporarse manualmente al documento final usando el editor que elija el equipo.
3. Elaborar un documento de **Entrega 1 actualizada** a partir de `entrega1-actualizada/fuentes/`, incorporando sus diagramas revisados. Exportarlo manualmente a `entrega1-actualizada/informe/entrega1_actualizada.pdf`.
4. Elaborar el documento de **Entrega 2** siguiendo el índice de `entrega2/informe.md`, incorporando los diagramas exportados, su prosa explicativa, el registro de cambios y el informe Scrum con capturas legibles. Exportarlo manualmente a `informe-final/entrega2.pdf`.
5. Abrir ambos PDFs y comprobar visualmente que no faltan apartados, páginas, imágenes, leyendas ni referencias. Los archivos fuente deben quedar en el repositorio.

No se exige una herramienta concreta de edición de documentos. **No basta con entregar capturas de diagramas sin explicación escrita.** La evaluación individual por el método de las bolas sigue el procedimiento indicado por el profesorado.

### Comprobaciones habituales

- Si un diagrama no se ve correctamente, revisad el archivo `.puml` antes de exportarlo.
- Si una figura aparece demasiado pequeña en el PDF, mejorad su resolución, divididla por subsistemas o utilizad orientación apaisada cuando proceda.
- Si el PDF queda desactualizado tras modificar las fuentes, **revisadlo y volved a exportarlo manualmente**.
- Comprobad la correspondencia entre requisitos, casos de uso, clases, tablas, secuencias y componentes.

## Reglas de calidad y alcance

- Usad nombres significativos y relaciones con cardinalidades/multiplicidades cuando proceda.
- Asegurad consistencia entre modelo de datos, diseño de clases, secuencias y arquitectura.
- El diagrama físico ER requiere tablas, atributos, PK, FK, relaciones y cardinalidades pertinentes.
- En el diagrama de secuencia se deben representar escenarios relevantes de casos de uso; uno solo **no implica** que uno siempre baste.
- No es necesario incorporar todas las relaciones UML posibles: representad las que realmente existan.
- Los ejemplos son mínimos, **no** un criterio completo de calificación ni un sustituto de la guía operativa v1.0.

## Lista de comprobación antes de entregar

- [ ] PDF de Entrega 1 actualizada, con fuentes correspondientes.
- [ ] Informe Scrum (fuente y PDF dentro del informe consolidado; capturas reales del tablero).
- [ ] ER físico: tablas, PK, FK, tipos de datos y cardinalidades coherentes.
- [ ] Clases: atributos, operaciones, relaciones y multiplicidades pertinentes.
- [ ] Secuencias: escenarios relevantes y mensajes consistentes con clases y casos de uso.
- [ ] Arquitectura/componentes: responsabilidades y dependencias claras.
- [ ] Justificación escrita de cada diagrama y trazabilidad a requisitos y CU.
- [ ] PDF consolidado revisado y fuentes versionadas en Git.
- [ ] Evaluación interna de compañeros por el procedimiento oficial.
