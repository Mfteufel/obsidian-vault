---
tags: [mt-panel, reglas]
fuente: ["src/app/(panel)/tickets/page.tsx", src/components/ticket-row.tsx, src/lib/actions.ts, src/lib/queries.ts]
---

# Tickets y plazos de respuesta

Un ticket es un pedido de soporte de un cliente. Puede ser de tres tipos:

- **Error:** algo que no funciona.
- **Consulta:** una pregunta de uso.
- **Funcionalidad:** un pedido de algo nuevo.

## Cargarlo

Se crea desde **Tickets**, con el cliente elegido, o desde la ficha del cliente. En Tickets solo aparecen clientes activos o en demo; desde la ficha se puede cargar para cualquier cliente. Pide título y tipo, y en la ficha también un detalle.

## Seguirlo

Cada ticket tiene un estado: **abierto**, **en curso** o **resuelto**. Los abiertos aparecen primero. La pantalla Tickets filtra por Todos, Abiertos, En curso o Resueltos.

## El plazo

Cada plan tiene un tiempo de respuesta, medido en **horas hábiles** desde que se creó el ticket (ver [[Horas hábiles]]). Mientras el ticket está abierto, la tarjeta muestra **"Respuesta hasta"** con la fecha límite.

Si pasa esa fecha sin que nadie lo haya tomado, aparece **"Plazo vencido"**. Un ticket **en curso** ya no figura como vencido: alguien lo tomó. Los plazos iniciales son Básico 9 h, Medio 4 h y Alto 1 h, pero son una sugerencia que se edita en [[Planes y precios]].

## Cargar horas

Al guardar un cambio de estado se puede sumar **"+ horas"** dedicadas. Cada carga queda registrada con fecha y persona, y el total del ticket aparece como "X h dedicadas". Esas horas son las que se cuentan para [[Horas de funcionalidades del plan Alto]].

## Funcionalidades y cobro

Un ticket de tipo **funcionalidad** muestra **"Se cobra aparte"** si el plan del cliente no las incluye. Hoy el panel solo marca esto; el cobro se gestiona fuera.

## Relacionado

- [[Panel MT]]
- [[Glosario del panel]]
- [[Horas hábiles]]
- [[Horas de funcionalidades del plan Alto]]
- [[Planes y precios]]
- [[Clientes y embudo de venta]]
- [[Base de datos SQLite]]
