---
tags: [react, javascript, jsx, virtual-dom, frontend, desarrollo-aplicaciones-1]
aliases: [React, JSX, Virtual DOM, Componentes React]
---

# CC_React

> Librería de UI creada por **Meta**. Se ocupa solo de la **capa de vista** — no es un framework completo (no trae routing, ni fetch, ni estado global por defecto).

## Los 4 pilares

| Concepto | Detalle |
|---|---|
| **Librería de UI** | Se ocupa solo de la vista, no es un framework completo |
| **Componentes** | La UI se construye como un árbol de piezas reutilizables e independientes |
| **Declarativo** | Describís **cómo debe verse** el estado; React se encarga de actualizar el DOM |
| **Virtual DOM** | Compara cambios en memoria antes de tocar el DOM real → renders eficientes |

> Este es el mismo modelo mental que usa [[React Native]] para renderizar componentes nativos en vez de HTML.

---

## Componentes y JSX

**Componente = función JS que retorna JSX.** Una app React es un árbol de componentes reutilizables. Cada uno es independiente y puede importarse/exportarse como cualquier módulo JS.

```jsx
// Definición
function ClaseCard({ titulo }) {
    return <h2>{titulo}</h2>;
}

// Uso (en otro componente)
import ClaseCard from './ClaseCard';
<ClaseCard titulo="Funcional 18:00" />
```

### JSX — reglas clave

| Regla | Detalle |
|---|---|
| **Un solo elemento raíz** | Siempre wrapeá en un `<div>` o en un fragmento `<>...</>` |
| **Tags siempre cerrados** | `<img />` en lugar de `<img>`. JSX es más estricto que HTML |
| **`className`, no `class`** | Los atributos van en camelCase: `class` → `className`, `for` → `htmlFor` |

**Llaves `{ }` — ventana a JavaScript.** Cualquier expresión de JS dentro de `{ }` se evalúa e inserta en el JSX:

```jsx
const hora = "18:00";
<h2>Clase a las {hora}</h2>
```

---

## Props, condicional y listas

### Props

El padre le pasa datos al hijo. Son de **solo lectura** — el hijo nunca los modifica.

```jsx
function Clase({ titulo, cupos }) {
    return <p>{titulo} — {cupos} cupos</p>;
}
<Clase titulo="Yoga" cupos={10} />
```

### Renderizado condicional

```jsx
// Con && (solo se muestra si es true)
{cupos > 0 && <Boton>Reservar</Boton>}

// Con ternario (elige entre dos opciones)
{cargando ? <Spinner /> : <Lista />}
```

### Renderizado de listas

Se usa `.map()` para transformar un array en JSX. **Cada elemento necesita una prop `key` única** para que React rastree los cambios entre renders.

```jsx
const clases = [
    { id: 1, nombre: "Yoga" },
    { id: 2, nombre: "Funcional" },
];

// ✅ key obligatoria
{clases.map(c => (
    <ClaseCard key={c.id} titulo={c.nombre} />
))}

// ❌ Sin key → warning + bugs (React no puede rastrear qué cambió)
```

> ⚠️ La `key` no reemplaza props como `id` dentro del componente — sirve **solo** para que React optimice el diffing del Virtual DOM.

---

## Pureza y árbol de UI

### Componentes puros

Un componente es **puro** si:
- **No modifica variables fuera de sí mismo**
- **Misma entrada → misma salida siempre**

```jsx
// ❌ Impuro: modifica variable externa (side effect)
let contador = 0;
function Cup() {
    contador++;  // efecto lateral fuera del componente
    return <p>Taza #{contador}</p>;
}

// ✅ Puro: recibe lo que necesita, no toca nada externo
function Cup({ numero }) {
    return <p>Taza #{numero}</p>;
}
```

> La pureza es lo que le permite a React optimizar renders y ejecutar componentes más de una vez sin romper nada (ej: en Strict Mode).

### La UI como árbol

React modela la UI como un **árbol de componentes**. El estado fluye hacia **abajo** (props), los eventos hacia **arriba** (callbacks).

```
              App
             /    \
        Header    Screen
          |          |
        Card       Card

        props ↓  |  eventos ↑
```

Este patrón (estado abajo, eventos arriba mediante callbacks) es la base de cómo se comunican componentes padre-hijo en toda la materia.

---

## Virtual DOM — cómo funciona el render eficiente

```
Estado cambia → React construye un Virtual DOM nuevo
              → lo compara con el anterior (diffing)
              → actualiza solo lo que cambió en el DOM real
```

Esto evita recrear toda la pantalla en cada cambio — solo se tocan los nodos que realmente cambiaron.

---

## Relacionado

- [[Clase 07 - React Native y Expo]]
- [[React Hooks]]
- [[React Native]]
- [[Expo]]
