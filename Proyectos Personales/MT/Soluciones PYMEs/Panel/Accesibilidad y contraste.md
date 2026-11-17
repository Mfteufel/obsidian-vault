---
tags: [mt-panel, accesibilidad]
fuente:
  - "CLAUDE.md"
  - "scripts/check-contrast.ts"
  - "src/components/icons.tsx"
  - "src/components/ui.tsx"
  - "src/components/nav.tsx"
  - "src/app/globals.css"
  - "progreso-tarea4.md"
---

# Accesibilidad y contraste

**Para qué:** que el panel se pueda leer y usar también con poca visión, con daltonismo o solo con el teclado.

## Reglas que cumple el panel

- **Estados con ícono y texto.** Nunca solo color. Los componentes `Badge` y `StatusText` agregan el ícono a los estados de éxito, advertencia, error e información. El ícono va oculto para lectores de pantalla, porque el texto ya dice lo mismo.
- **Links subrayados** en todo el panel.
- **Foco visible** en todo lo que se puede tocar: un contorno de 2 px. Dentro del menú carbón el foco es bordó claro, porque el bordó oscuro sobre carbón no tiene contraste suficiente.
- **`aria-current`** marca la sección activa en el menú y el filtro activo en Tickets.
- **Contraste WCAG AA** en claro y en oscuro: texto de al menos 4,5 a 1, y elementos de interfaz de al menos 3 a 1.

## Combinaciones prohibidas

- Gris-500 sobre gris-50.
- Gris-400 sobre blanco. La primitiva gris-400 no existe hoy, pero la regla queda escrita.
- Bordó 700 u 800 sobre carbón.
- Texto blanco sobre el ámbar de advertencia. Es una decisión del dueño.

## Cómo se verifica

`pnpm check:contrast` revisa 80 pares de colores (40 en claro y 40 en oscuro), los 31 roles de `globals.css` y las combinaciones prohibidas. Termina con código 1 si algo falla. Hoy el par más justo da 3,66 a 1, en oscuro, entre el borde de los campos y la superficie. Como es un elemento de interfaz, el mínimo es 3.

Corrélo siempre que cambie `theme.css`, y también cuando se cargue la paleta oficial.

## Relacionado

- [[Identidad visual y paleta]]
- [[Tema claro, oscuro y sistema]]
- [[Cómo correr el panel]]
- [[Panel MT]]
