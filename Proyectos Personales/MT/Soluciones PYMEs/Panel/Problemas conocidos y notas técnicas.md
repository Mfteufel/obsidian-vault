---
tags: [mt-panel, problemas]
fuente:
  - "README.md"
  - "progreso-tarea5.md"
  - "progreso-tarea3.md"
  - "src/app/(panel)/clientes/[id]/page.tsx"
  - "src/lib/emergencies.ts"
  - "src/lib/time.test.ts"
  - "src/lib/plans.test.ts"
  - "src/lib/shifts.test.ts"
  - "src/lib/emergencies.test.ts"
---

# Problemas conocidos y notas técnicas

**Para qué:** dejar registradas las rarezas del panel, su causa y lo que se decidió. Así nadie las vuelve a investigar desde cero.

## Error de React #419 (resuelto)

Solo aparecía en producción. Al salir de la ficha de un cliente que no existe (`/clientes/<id>`), con un link o con el menú, la consola mostraba el error #419.

- **Causa:** la ficha tenía su propio `Suspense`, anidado dentro del de `AuthGate`. El servidor no llegaba a cerrar ese límite interno y la página pasaba a renderizarse en el navegador.
- **Corrección:** se quitó el `Suspense` interno. El del `AuthGate` ya cubre los parámetros de la ruta.
- **Verificación:** en producción, sin errores de consola. En `next dev` no se reproducía.

## Página de "no encontrado" con estado 200

La página de no encontrado responde HTTP 200, porque llega en streaming. Se aceptó así. Si más adelante se exige un 404 real, es otro cambio.

## Comportamientos dudosos que se conservaron

- **Doble cierre de emergencias.** Si la emergencia ya está cerrada, no se cobra de nuevo. Pero si se cerró sin marcar mal uso y después se vuelve a cerrar marcándolo, el extra no se suma. Está pendiente como caso a decidir.
- **Horas hábiles en el borde.** Un pedido de lunes 17:00 con 1 hora de plazo vence a las 18:00, justo al cierre. Es el comportamiento actual y está pendiente de decisión. Ver [[Horas hábiles]].

## Casos pendientes en las pruebas (6 `it.todo`)

- Precio vacío en el plan Medio al registrar una emergencia.
- Volver a cerrar una emergencia marcándola como mal uso.
- Doble alta del mismo cliente por doble clic.
- Precios con punto de miles.
- Guardias: una semana de alguien que ya está inactivo, y una guardia manual en el futuro.

## Notas técnicas

- **`connection()` en cada lectura.** SQLite es síncrono. Sin `connection()`, Next guardaría los datos al compilar. El costo es que nada queda en caché.
- **Acciones con `{ error }`.** Porque en producción Next oculta el mensaje de los errores lanzados. Ver [[Lecturas y acciones del servidor]].
- **Aviso de instalación.** Al correr las pruebas, `vitest` pide `@types/node` 22 o superior (o 24), y el proyecto tiene la 20. Es un aviso de dependencia que no impide correrlas.

## Relacionado

- [[Lecturas y acciones del servidor]]
- [[Tarea 5 · Tema, logo y error 419]]
- [[Decisiones abiertas]]
- [[Panel MT]]
