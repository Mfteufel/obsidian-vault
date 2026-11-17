---
tags: [mt-panel, diseno]
fuente:
  - "README.md"
  - "src/app/layout.tsx"
  - "src/components/theme-toggle.tsx"
  - "src/app/theme.css"
  - "progreso-tarea5.md"
---

# Tema claro, oscuro y sistema

**Para qué:** cada persona elige si quiere el panel claro, oscuro o siguiendo la configuración de su computadora. La elección queda guardada en ese navegador.

## Dónde se elige

El selector tiene tres botones: **Claro**, **Oscuro** y **Sistema**. Está en el menú lateral, debajo de la lista de secciones, y también en la pantalla de ingreso. El botón elegido queda marcado.

## Cómo se guarda

- La elección va en la cookie `theme`, por un año.
- Si la cookie tiene `light` o `dark`, el panel fuerza ese tema.
- Si no hay cookie, o tiene otro valor, se usa **Sistema**.

## Cómo se aplica

El tema se marca con el atributo `data-theme` en la etiqueta `<html>`:

- En **Sistema** no hay atributo, y la paleta sigue la preferencia del sistema operativo.
- En **Claro** u **Oscuro**, el atributo fuerza ese tema, aunque el sistema esté en el otro.

## Sin destello al cargar

Un script pequeño en el `<head>` lee la cookie antes de que se pinte la página. Así la primera imagen ya tiene el tema correcto. No se usa la función `cookies()` en el layout raíz, porque eso impide generar la página de antemano y rompe la configuración de Cache Components.

## Detalles para quien lo mantenga

- En desarrollo, el modo estricto de React borra el atributo de `<html>` al arrancar. El selector lo vuelve a aplicar desde la cookie.
- Antes de que termine de cargar la app, el botón marcado puede decir "Sistema" por un instante. Solo cambia el botón; la página no parpadea.

## Verificación

En la Tarea 5 se probó con el build de producción en 375 y 1440 px, en las nueve pantallas y en los tres modos, sin errores en la consola del navegador.

## Relacionado

- [[Identidad visual y paleta]]
- [[Accesibilidad y contraste]]
- [[Tarea 5 · Tema, logo y error 419]]
- [[Recorrido por las pantallas]]
- [[Panel MT]]
