---
tags: [mt-panel, introduccion]
fuente: [src/lib/db.ts, src/components/ui.tsx, src/lib/queries.ts, src/lib/time.ts, src/lib/emergencies.ts, src/lib/shifts.ts]
---

# Glosario del panel

Definiciones de una línea. Cada término tiene su nota con el detalle.

## Clientes

- **Relevamiento:** primera etapa, se conoce a la empresa y su necesidad. Ver [[Clientes y embudo de venta]].
- **Demo:** prueba del sistema. Se cuenta desde que el cliente entra en demo y tiene un límite de 7 días.
- **Activo:** cliente con plan contratado. Solo los activos pueden tener emergencias.
- **Perdido:** cliente que no se concretó.

## Planes

- **Básico, Medio, Alto:** los tres planes de seguimiento. Ver [[Planes y precios]].
- **Se cobra aparte:** marca de un ticket de funcionalidad nueva que no está incluida en el plan del cliente.
- **Funcionalidades incluidas:** pedidos de funcionalidad que el plan absorbe, con un límite mensual de horas opcional. Ver [[Horas de funcionalidades del plan Alto]].

## Tickets

- **Ticket:** pedido de soporte del cliente. Tipos: **error**, **consulta** y **funcionalidad**.
- **Estados:** **abierto** (nadie lo tomó), **en curso** y **resuelto**. Ver [[Tickets y plazos de respuesta]].
- **Plazo de respuesta:** tiempo del plan para responder, contado en horas hábiles. Ver [[Horas hábiles]].
- **Plazo vencido:** ticket abierto que pasó su plazo sin que nadie lo tomara.

## Emergencias

- **Emergencia:** el sistema del cliente se cayó. Ver [[Emergencias y cobros]].
- **Mal uso:** emergencia que resultó no serlo; suma el extra por mal uso.
- **Modos de emergencia** (en Planes): **Sin botón de emergencia** (`none`), **Botón con pago por uso** (`pay_per_use`) y **Botón incluido 24/7** (`included`).

## Equipo

- **Guardia:** la persona que atiende las emergencias durante una semana. Ver [[Guardias y rotación]].
- **Rotación:** reparto automático de guardias entre las personas activas del equipo.

## Relacionado

- [[Panel MT]]
- [[Propósito y usuarios del panel]]
- [[Recorrido por las pantallas]]
- [[Clientes y embudo de venta]]
- [[Tickets y plazos de respuesta]]
- [[Horas hábiles]]
- [[Emergencias y cobros]]
- [[Guardias y rotación]]
- [[Planes y precios]]
