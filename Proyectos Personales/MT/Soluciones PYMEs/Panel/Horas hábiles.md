---
tags: [mt-panel, reglas]
fuente: [src/lib/time.ts, src/lib/time.test.ts, src/lib/shifts.ts, src/lib/hours.ts, src/lib/queries.ts]
---

# Horas hábiles

Los plazos de respuesta no se cuentan en horas corridas, sino en **horas hábiles**: de **lunes a viernes, de 9 a 18 h**, en hora de Buenos Aires. Así un ticket que llega el viernes a la tarde no queda "vencido" el sábado.

## Cómo se cuenta

- El reloj arranca al crear el ticket. Si ese momento cae fuera de horario, arranca en la próxima apertura.
- Lo que no entra en un día pasa al siguiente hábil.
- Buenos Aires no tiene horario de verano, así que siempre es UTC-3, aunque el servidor esté en otro huso.

Ejemplos, según las pruebas del panel:

- **Viernes 16:00 + 4 h:** 2 h el viernes y 2 h el lunes, así que vence el lunes a las 11:00.
- **Sábado 10:00 + 1 h:** el reloj arranca el lunes a las 9:00, así que vence a las 10:00.
- **Lunes 17:00 + 1 h:** vence a las 18:00 del mismo día. Es el comportamiento actual, pero el dueño todavía no lo confirmó: si el plazo cae justo al cierre, hoy no pasa al día siguiente (ver [[Decisiones abiertas]]).

## Dónde se usa

- **Plazos de tickets:** ver [[Tickets y plazos de respuesta]].
- **Semana de guardia:** las guardias van de lunes a domingo, también en hora de Buenos Aires. Ver [[Guardias y rotación]].
- **Mes del plan Alto:** el consumo de horas se reinicia el día 1 a las 00:00 de Buenos Aires. Ver [[Horas de funcionalidades del plan Alto]].
- **Montos:** se muestran en pesos argentinos, sin centavos. Si un precio no está definido, dice "Sin definir" en lugar de un número.

## Relacionado

- [[Panel MT]]
- [[Tickets y plazos de respuesta]]
- [[Guardias y rotación]]
- [[Horas de funcionalidades del plan Alto]]
- [[Decisiones abiertas]]
