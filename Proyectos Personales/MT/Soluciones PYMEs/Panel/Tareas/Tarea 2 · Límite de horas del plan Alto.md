---
tags: [mt-panel, historia, tarea-2]
fuente: [plan-tarea2.md, progreso-tarea2.md]
---
# Tarea 2 · Límite de horas del plan Alto

**Objetivo.** Registrar cada carga de horas en un ticket y mostrar cuántas horas de funcionalidades consumió cada cliente en el mes, contra el límite de su plan Alto.

**Qué cambió para el usuario.**
- En el detalle de un cliente con plan Alto aparece "Funcionalidades este mes: X de Y horas", o "X horas · sin límite definido" si no hay límite.
- Un aviso ámbar aparece al pasar el 80 % del límite y uno rojo al superarlo. Ambos llevan ícono y texto.
- Cada carga de horas queda con fecha y con el usuario que la cargó.

**Qué cambió en el código.**
- Tabla `ticket_hours`, una fila por carga. Se borra junto con su ticket.
- Migración de una sola vez (`PRAGMA user_version`) que pasa las horas viejas de `tickets.hours_spent` a la nueva tabla. `hours_spent` sigue siendo el total del ticket.
- `updateTicket` guarda la carga y actualiza el total en una misma transacción.
- `src/lib/hours.ts` reúne la lógica pura del límite, sin Next.

**Decisiones y desvíos.**
- Solo cuentan los tickets de tipo funcionalidad (lo confirmó el dueño).
- La consulta quedó en una función aparte, para poder probarla sin Next (desvío autorizado).
- Los totales se redondean a 2 decimales antes de comparar, para que la suma de decimales no deje pasar el umbral del 80 %.
- Con límite 0, 0 horas da "ok" y cualquier hora da "superado".

**Cómo se verificó.** Compilación, lint y build sin errores. Migración probada con copias de la base: las sumas coinciden y reiniciar no duplica filas. Navegador con build de producción: sin límite, límite 7 (aviso), límite 5 (superado), límite 20 (sin aviso) y plan Medio sin contador.

**Pendientes que dejó.** Qué pasa al superar el límite (hoy solo avisa), cómo corregir una carga equivocada, qué límite rige si el cliente cambia de plan a mitad de mes, y si la fecha de creación es el criterio correcto para las horas viejas. Los íconos y colores de esta tarea se rehicieron en la [[Tarea 4 · Rediseño visual]].

## Relacionado
- [[Historial de tareas]]
- [[Horas de funcionalidades del plan Alto]]
- [[Base de datos SQLite]]
- [[Decisiones abiertas]]
- [[Panel MT]]
