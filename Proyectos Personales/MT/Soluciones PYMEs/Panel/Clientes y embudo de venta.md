---
tags: [mt-panel, uso]
fuente: ["src/app/(panel)/clientes/page.tsx", "src/app/(panel)/clientes/[id]/page.tsx", src/lib/actions.ts, src/lib/queries.ts]
---

# Clientes y embudo de venta

Cada empresa que MT conoce es un cliente. Va avanzando por tres etapas: **relevamiento**, **demo** y **activo**. Si no se concreta, queda **perdido**.

## Alta

En **Clientes** se despliega el formulario **+ Nuevo cliente**. Pide:

- **Empresa** (obligatorio).
- **Contacto**, **Qué necesita** y **Notas** (opcionales).

Al guardar se abre la ficha de la empresa nueva.

## La lista

Muestra empresa, qué necesita, estado y plan. Los clientes en demo tienen una etiqueta **"día X de 7"**, que pasa a ámbar al llegar a 7.

## Avanzar en el embudo

En la ficha, **Estado y plan** permite cambiar la etapa y elegir el plan de seguimiento. Reglas:

- **Demo:** la cuenta de los 7 días arranca al pasar a demo. Si se guarda otra vez como demo, no se reinicia.
- **Activo:** exige tener un plan elegido. Si no, el panel avisa "Para activar a un cliente hay que elegir un plan."
- Los precios de los planes aparecen como "Sin definir" hasta que se carguen en [[Planes y precios]].

## Lo que muestra la ficha

- **Plan:** seguimiento, plazo de respuesta y si incluye funcionalidades. Para el plan Alto, también el contador de horas del mes. Ver [[Horas de funcionalidades del plan Alto]].
- **Botón de emergencia:** solo aparece con cliente activo y un plan que lo tenga. Ver [[Emergencias y cobros]].
- **Tickets** y **emergencias** del cliente. Ver [[Tickets y plazos de respuesta]].

Si el link apunta a un cliente que no existe, se ve la pantalla "No encontramos lo que buscabas" (ver [[Recorrido por las pantallas]]).

## Relacionado

- [[Panel MT]]
- [[Glosario del panel]]
- [[Planes y precios]]
- [[Tickets y plazos de respuesta]]
- [[Emergencias y cobros]]
- [[Horas de funcionalidades del plan Alto]]
- [[Tablero de Inicio]]
