---
tags: [mt-panel, uso]
fuente: ["src/app/(panel)/page.tsx", src/lib/queries.ts, src/lib/demo.ts, src/lib/actions.ts]
---

# Tablero de Inicio

Inicio es lo primero que se ve al entrar. Dice "Lo importante de hoy: demos, tickets y emergencias", y se lee de arriba abajo.

## Indicadores

- **Clientes activos:** cuántas empresas tienen plan contratado.
- **Tickets sin resolver:** los que no están resueltos. Si alguno tiene el plazo vencido, la tarjeta se marca en rojo y lo dice.
- **Emergencias del mes:** cuántas hubo desde el 1 del mes. Debajo figura **"Cobrado"**, que en realidad es la suma de lo que se registra a cobrar, incluido el extra por mal uso.
- **De guardia esta semana:** quién atiende. Si nadie está asignado, dice "Sin asignar" y ofrece armar la rotación en [[Guardias y rotación]].

## Para atender

Es la lista de lo que pide acción. Cada aviso tiene ícono y texto, y un link **Ver**:

- **Emergencia abierta** (rojo): todavía no se marcó como resuelta. Ver [[Emergencias y cobros]].
- **Plazo vencido** (rojo): un ticket abierto que pasó su plazo. Ver [[Tickets y plazos de respuesta]].
- **Demo pasada de tiempo** (ámbar): una demo que lleva 7 días o más. Ver [[Clientes y embudo de venta]].

Si no hay nada de eso, dice "Todo en orden. No hay nada urgente."

## Primera vez

Si todavía no hay clientes, Inicio muestra la tarjeta **Empecemos**, con dos opciones: cargar el primer cliente o cargar datos de ejemplo. Los ejemplos son cinco empresas inventadas, con tickets y una emergencia cerrada. El botón solo funciona con la base vacía, así que no duplica nada. Para levantar el panel por primera vez, ver [[Cómo correr el panel]].

## Relacionado

- [[Panel MT]]
- [[Recorrido por las pantallas]]
- [[Clientes y embudo de venta]]
- [[Tickets y plazos de respuesta]]
- [[Emergencias y cobros]]
- [[Cómo correr el panel]]
