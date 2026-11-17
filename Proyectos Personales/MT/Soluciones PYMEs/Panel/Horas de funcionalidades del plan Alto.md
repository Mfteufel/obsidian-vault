---
tags: [mt-panel, reglas]
fuente: [src/lib/hours.ts, src/lib/hours.test.ts, src/lib/queries.ts, "src/app/(panel)/clientes/[id]/page.tsx"]
---

# Horas de funcionalidades del plan Alto

El plan **Alto** incluye las funcionalidades nuevas. Para no quedarse corto, el panel lleva la cuenta de las horas que se dedican a ellas en el mes y las compara con un límite.

## Qué cuenta

- Solo los tickets de tipo **funcionalidad**. Errores y consultas no suman.
- Solo las horas **cargadas** en el mes calendario en curso (hora de Buenos Aires). Importa la fecha de la carga, no la de creación del ticket.
- Las horas viejas se incorporaron con la fecha de creación de su ticket, porque es el único dato que había.

## Qué se ve

En la ficha del cliente, dentro de **Estado y plan**, aparece una de estas líneas:

- "Funcionalidades este mes: **X de Y horas**" si hay límite.
- "Funcionalidades este mes: **X horas · sin límite definido**" si no lo hay.

## Estados

- **OK:** por debajo del 80 % del límite.
- **Cerca:** a partir del 80 %. Aparece la marca "Pasó el 80% del límite". Usar exactamente el límite también es "cerca", no superado.
- **Superado:** más que el límite. Aparece "Límite superado".

Con límite 0, cualquier hora ya supera. Los decimales se redondean a dos cifras, para que sumas como 0,1 + 0,1… no den falsas alarmas.

## Qué no hace (todavía)

- **No cobra.** Hoy solo avisa. Qué pasa al superar el límite es una decisión abierta (ver [[Decisiones abiertas]]).
- **El límite se carga en [[Planes y precios]]**, en el campo "Límite de horas por mes (opcional)". Si está vacío, no hay límite.

Las horas se registran al cargar un cambio en un ticket, desde [[Tickets y plazos de respuesta]]. Para el criterio de fechas, ver [[Horas hábiles]].

## Relacionado

- [[Panel MT]]
- [[Planes y precios]]
- [[Tickets y plazos de respuesta]]
- [[Horas hábiles]]
- [[Base de datos SQLite]]
- [[Decisiones abiertas]]
