---
tags: [mt-panel, uso]
fuente: [src/lib/shifts.ts, src/lib/shifts.test.ts, "src/app/(panel)/guardias/page.tsx", src/lib/actions.ts, src/lib/queries.ts]
---

# Guardias y rotación

Cada semana tiene **una persona de guardia**. Es quien atiende las emergencias de esa semana. Las semanas van de lunes a domingo, en hora de Buenos Aires.

## El equipo

En **Guardias**, la columna **Equipo** permite:

- **Sumar persona:** se carga el nombre.
- **Desactivar / Activar:** una persona inactiva no aparece en las listas de guardia y se saltea en la rotación. No se borra, así que se puede reactivar.

Hace falta cargar al menos una persona activa para armar la rotación.

## Armar la rotación

**Completar rotación** llena las semanas vacías de las próximas 8, rotando entre las personas activas. Tiene dos reglas:

- No cambia las semanas que ya tienen persona asignada.
- Si se repite, no cambia nada ni duplica filas.

La rotación continúa desde la última persona asignada, así que el reparto queda parejo.

## Cambios a mano

En cada semana se puede elegir otra persona activa, o **Sin asignar** para dejarla vacía. La semana actual lleva la etiqueta **Esta semana**.

## Relación con emergencias

Cuando se registra una emergencia, queda asociada a la persona de guardia de ese momento. Por eso, si cambia la guardia después, la emergencia ya registrada conserva a quien la atendía. Ver [[Emergencias y cobros]].

## Relacionado

- [[Panel MT]]
- [[Emergencias y cobros]]
- [[Horas hábiles]]
- [[Tablero de Inicio]]
- [[Recorrido por las pantallas]]
