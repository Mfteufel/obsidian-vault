---
tags: [mt-panel, uso]
fuente: [src/components/nav.tsx, "src/app/(panel)/layout.tsx", "src/app/(panel)/page.tsx", "src/app/(panel)/clientes/page.tsx", "src/app/(panel)/tickets/page.tsx", "src/app/(panel)/emergencias/page.tsx", "src/app/(panel)/guardias/page.tsx", "src/app/(panel)/planes/page.tsx", "src/app/not-found.tsx", "src/app/error.tsx"]
---

# Recorrido por las pantallas

El menú lateral tiene seis entradas. Abajo están el selector de tema y el usuario, con el cierre de sesión.

| Pantalla | Para qué se entra |
|---|---|
| **Inicio** | Ver lo urgente de hoy. Ver [[Tablero de Inicio]]. |
| **Clientes** | Cargar empresas nuevas y mover cada una por el embudo. Ver [[Clientes y embudo de venta]]. |
| **Tickets** | Cargar y seguir pedidos de soporte. Ver [[Tickets y plazos de respuesta]]. |
| **Emergencias** | Registrar una emergencia y cerrarla. Ver [[Emergencias y cobros]]. |
| **Guardias** | Cargar al equipo y armar la rotación. Ver [[Guardias y rotación]]. |
| **Planes y precios** | Definir planes, precios y el extra por mal uso. Ver [[Planes y precios]]. |

Dentro de un cliente, la **ficha** reúne su estado, plan, tickets y emergencias. Se llega desde la lista de Clientes o con el link de cualquier aviso.

Hay dos pantallas especiales:

- **"No encontramos lo que buscabas":** aparece si el cliente no existe. Tiene un botón para volver a Clientes.
- **"Algo salió mal":** aparece ante un error inesperado. El botón **Reintentar** vuelve a cargar la pantalla.

En el celular, el menú pasa a una franja horizontal que se desliza.

Para el acceso y el tema, ver [[Acceso, sesiones y seguridad]] y [[Tema claro, oscuro y sistema]].

## Relacionado

- [[Panel MT]]
- [[Propósito y usuarios del panel]]
- [[Glosario del panel]]
- [[Tablero de Inicio]]
- [[Clientes y embudo de venta]]
- [[Tickets y plazos de respuesta]]
- [[Emergencias y cobros]]
- [[Guardias y rotación]]
- [[Planes y precios]]
- [[Acceso, sesiones y seguridad]]
- [[Tema claro, oscuro y sistema]]
