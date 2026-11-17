---
tags: [mt-panel, historia, tarea-4]
fuente: [plan-tarea4.md, progreso-tarea4.md, git log (commits a40eb0d, 6ae388d, dcda26a)]
---
# Tarea 4 · Rediseño visual

**Objetivo.** Aplicar la identidad de MT (carbón, bordó y gris claro) a todo el panel, con modo claro y oscuro y contraste WCAG AA verificado.

**Qué cambió para el usuario.**
- Menú lateral carbón, fondo gris claro y bordó solo en botones, filtros activos y el ítem del menú que está abierto.
- Modo oscuro completo.
- Los estados (éxito, advertencia, error e info) muestran ícono y texto, nunca solo color.
- Links subrayados y foco visible en todo lo que se puede tocar.

**Qué cambió en el código.**
- `src/app/theme.css` es el único archivo con colores en HEX: 38 primitivas y 31 roles, en claro y oscuro.
- `globals.css` registra los roles, y la paleta de Tailwind por defecto quedó apagada. Las pantallas usan solo roles como `bg-surface` o `text-fg`.
- `icons.tsx` agrega los íconos de estado. `ui.tsx` y todas las pantallas se migraron a roles.
- `scripts/check-contrast.ts` (`pnpm check:contrast`) revisa 80 pares de color.

**Decisiones y desvíos.**
- El dueño reformuló la regla del ámbar: lo prohibido es texto blanco sobre fondo ámbar. El bordó oscuro sobre ámbar sí está permitido.
- La proporción de colores no llegó al rango del plan: el carbón ronda el 16,5 % y no el 20 al 35 %. El dueño aceptó esto (opción A), porque el menú de 240 px ocupa ese espacio.

**Cómo se verificó.** Compilación, lint, 199 pruebas, `check:contrast` y build sin errores. En el navegador se revisaron 9 pantallas en claro y oscuro: se pasó con Tab por 322 elementos sin fallas y el contraste mínimo medido fue 5,8. Al final, los estilos calculados de 1558 elementos coincidían antes y después de la limpieza.

**Pendientes que dejó.**
- Cargar la paleta oficial cuando exista (pasos en el README).
- Las opciones B (menú más ancho) y C (más bordó en Inicio) quedaron sin decidir.
- El favicon no se tocó.
- El error #419 apareció durante la revisión y se corrigió en la [[Tarea 5 · Tema, logo y error 419]].

## Relacionado
- [[Historial de tareas]]
- [[Identidad visual y paleta]]
- [[Accesibilidad y contraste]]
- [[Decisiones abiertas]]
- [[Panel MT]]
