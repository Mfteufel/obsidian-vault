---
tags: [mt-panel, reglas]
fuente: [src/lib/emergencies.ts, src/lib/emergencies.test.ts, "src/app/(panel)/emergencias/page.tsx", src/lib/actions.ts, src/lib/plans.ts]
---

# Emergencias y cobros

Una **emergencia** es cuando el sistema del cliente se cayó. No es cualquier pedido: para eso está el ticket (ver [[Tickets y plazos de respuesta]]).

## Qué pasa según el plan

- **Básico:** sin botón de emergencia (se puede cambiar en [[Planes y precios]]). Si se intenta registrar, el panel avisa que el plan no la incluye.
- **Medio:** botón con **pago por uso**. Cada emergencia suma el precio por emergencia que figure en el plan.
- **Alto:** botón **incluido 24/7**, sin costo por uso.

Solo se puede registrar para clientes **activos** con un plan que tenga botón. Desde la ficha del cliente o desde la pantalla Emergencias, que muestra los clientes elegibles.

## Quién la atiende

La emergencia queda a cargo de la **guardia de esa semana** (ver [[Guardias y rotación]]). Si nadie está asignado, la pantalla lo señala con "nadie asignado" y deja el link para armar la rotación.

## Cerrarla

Cada emergencia abierta tiene **Marcar resuelta**. Si no fue una emergencia de verdad, se tilda **"No era una emergencia"** antes de cerrar: se suma el **extra por mal uso**, que aplica a cualquier plan y se carga en Planes. Una emergencia ya cerrada no vuelve a cobrar.

## Lo que hay que saber

- **Precio sin definir:** si el precio por emergencia del plan Medio está vacío, la emergencia se registra con cobro 0. Es un caso pendiente que el panel todavía no resuelve.
- **Doble alta:** cargar dos veces la misma emergencia crea dos registros. También está pendiente.
- Los montos que aparecen son lo que el panel **registra a cobrar**. El cobro efectivo ocurre fuera del panel.

Los casos pendientes están en [[Decisiones abiertas]].

## Relacionado

- [[Panel MT]]
- [[Planes y precios]]
- [[Guardias y rotación]]
- [[Clientes y embudo de venta]]
- [[Tablero de Inicio]]
- [[Decisiones abiertas]]
