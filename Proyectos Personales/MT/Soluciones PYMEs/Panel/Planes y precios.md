---
tags: [mt-panel, uso]
fuente: ["src/app/(panel)/planes/page.tsx", src/lib/plans.ts, src/lib/db.ts, src/lib/form.ts, src/components/ui.tsx]
---

# Planes y precios

Hay tres planes de seguimiento. Cada cliente activo tiene uno, y de ese plan salen el plazo de respuesta, el botón de emergencia y las funcionalidades incluidas.

## Los tres planes

Estos son los valores iniciales que trae el panel. Los de respuesta son una sugerencia, y los precios no están definidos.

| Plan | Respuesta (h hábiles) | Seguimiento | Botón de emergencia | Funcionalidades |
|---|---|---|---|---|
| **Básico** | 9 | 1 contacto por semana | Sin botón | Se cobran aparte |
| **Medio** | 4 | 2 contactos por semana | Pago por uso | Se cobran aparte |
| **Alto** | 1 | Seguimiento diario | Incluido 24/7 | Incluidas, con límite opcional de horas |

## Qué se configura en cada plan

- **Nombre** y **seguimiento** (texto libre).
- **Precio mensual (ARS):** vacío significa "Sin definir".
- **Respuesta (horas hábiles):** número entero de 1 o más. Ver [[Horas hábiles]].
- **Botón de emergencia:** sin botón, pago por uso o incluido.
- **Precio por emergencia:** solo aplica con pago por uso. Ver [[Emergencias y cobros]].
- **Incluye funcionalidades nuevas:** sí o no.
- **Límite de horas por mes:** opcional. Ver [[Horas de funcionalidades del plan Alto]].

## Extra por mal uso

Es una tarjeta aparte, que vale para **todos** los planes. Se suma cuando una emergencia se cierra como "no era una emergencia". También puede quedar "Sin definir".

## Cómo se escriben los números

- Se acepta coma o punto decimal: `2,5` y `2.5` son lo mismo.
- **No uses punto de miles.** Escribí `1500`, no `1.500`: el panel lo toma como 1,5.
- No se aceptan montos negativos.

## Precios

Los precios de los tres planes, de la emergencia y del mal uso **no están definidos** y el panel no inventa ninguno. Cuando el dueño los defina, se cargan en esta pantalla y se reflejan en toda la aplicación.

## Relacionado

- [[Panel MT]]
- [[Emergencias y cobros]]
- [[Horas de funcionalidades del plan Alto]]
- [[Tickets y plazos de respuesta]]
- [[Horas hábiles]]
- [[Decisiones abiertas]]
