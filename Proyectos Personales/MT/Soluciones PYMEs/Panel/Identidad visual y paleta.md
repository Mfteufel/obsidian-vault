---
tags: [mt-panel, diseno]
fuente:
  - "CLAUDE.md"
  - "README.md"
  - "src/app/theme.css"
  - "src/app/globals.css"
  - "src/components/ui.tsx"
  - "src/components/logo.tsx"
  - "src/components/icons.tsx"
  - "progreso-tarea4.md"
---

# Identidad visual y paleta

**Para qué:** que el panel se vea como MT: elegante, sobria y confiable. Los colores son carbón, bordó y gris claro.

## Proporción

La regla de diseño es 60 % gris claro, 30 % carbón y 10 % bordó. En la pantalla de Inicio, medida a 1440 px, da unos 82 % gris claro, 17 % carbón y muy poco bordó. La diferencia viene del menú lateral, que mide 240 px. El dueño aceptó ese resultado.

## Dónde vive la paleta

- `src/app/theme.css` es el **único** archivo con colores HEX. Tiene 38 tonos base y 31 roles, como fondo, texto, acento o estado.
- `src/app/globals.css` registra esos roles para Tailwind dentro de `@theme inline`. La paleta por defecto de Tailwind está apagada.
- Las pantallas usan solo roles, por ejemplo `bg-surface`, `text-fg` o `bg-accent`. Una clase vieja como `bg-zinc-100` no genera ningún estilo.

La paleta es una interpretación hecha a partir de los logos de MT. No es la paleta oficial, porque los archivos oficiales no existían cuando se hizo.

## Reglas de la marca

- El bordó se usa solo para acciones y acentos. Nunca para errores: los errores usan un rojo anaranjado distinto.
- Los estados (éxito, advertencia, error e información) llevan siempre ícono y texto. Los íconos están en `icons.tsx`.
- Los links van subrayados y el foco siempre se ve.
- El logo es un cubo plano con la M y la T, dibujado en `logo.tsx`. Toma el color del texto que lo rodea.

## Cuando llegue la paleta oficial

1. Reemplazar solo los HEX de las primitivas en `theme.css`.
2. Si la paleta trae otros tonos, agregarlos y reasignar los roles.
3. Correr `pnpm check:contrast` hasta que termine con código 0.
4. Revisar a ojo el logo y el selector de tema, en claro y en oscuro.
5. Quitar la aclaración "no oficial" del encabezado de `theme.css`.

## Relacionado

- [[Tema claro, oscuro y sistema]]
- [[Accesibilidad y contraste]]
- [[Tarea 4 · Rediseño visual]]
- [[Decisiones abiertas]]
- [[Panel MT]]
