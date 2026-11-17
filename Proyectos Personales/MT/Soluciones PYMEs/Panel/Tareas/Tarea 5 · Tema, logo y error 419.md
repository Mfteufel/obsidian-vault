---
tags: [mt-panel, historia, tarea-5]
fuente: [plan-tarea5.md, progreso-tarea5.md, git show dcda26a]
---
# Tarea 5 · Tema, logo y error 419

**Estado.** Terminada. El archivo del plan dice "PENDIENTE DE APROBACIÓN", pero el dueño lo aprobó por chat y se ejecutó completo.

**Objetivo.** Dar un selector de tema, poner el logo del cubo, documentar cómo cargar la paleta oficial y corregir el error React #419 al salir de un cliente inexistente.

**Qué cambió para el usuario.**
- Selector Claro / Oscuro / Sistema en el menú y en `/login`. La elección se recuerda un año.
- Logo del cubo MT en el menú y en el login.
- Al salir de `/clientes/<id inexistente>` ya no aparece el error en consola.

**Qué cambió en el código.**
- `layout.tsx` lee la cookie `theme` en un script dentro del `<head>`, antes de pintar, para evitar el destello.
- `theme-toggle.tsx` es el selector. `logo.tsx` es el SVG del cubo, con `currentColor`.
- Se quitó el `Suspense` interno de `clientes/[id]/page.tsx`, que causaba el #419.
- El README documenta la paleta oficial, y `CLAUDE.md` apunta a `src/app/theme.css`.

**Decisiones y desvíos.**
- El logo se mostró a 40 px (no 32) y se engrosaron las letras, porque a 32 px se leían justo.
- El criterio para el cliente inexistente quedó en HTTP 200, no 404: lo decidió el dueño.
- Cambiar `CLAUDE.md` estaba fuera del plan, pero el dueño lo autorizó.

**Cómo se verificó.** Compilación, lint, 199 pruebas, `check:contrast` y build sin errores. En Chrome con build de producción, a 375 y 1440 px, con tema claro, oscuro y sistema, se recorrieron 9 pantallas sin errores de consola y sin desborde horizontal.

**Pendientes que dejó.**
- Para `/xyz` (ruta fuera del panel), el navegador muestra su propio error de recurso 404. No es un error de la app.
- El #419 no se reprodujo con `next dev`, así que se verificó solo en producción.
- El favicon no se tocó.
- La paleta oficial sigue pendiente, porque los archivos de colores todavía no existen.

## Relacionado
- [[Historial de tareas]]
- [[Tema claro, oscuro y sistema]]
- [[Identidad visual y paleta]]
- [[Problemas conocidos y notas técnicas]]
- [[Panel MT]]
