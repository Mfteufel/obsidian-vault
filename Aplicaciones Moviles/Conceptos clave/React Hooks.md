---
tags: [react, hooks, usestate, useeffect, useref, usememo, usecallback, desarrollo-aplicaciones-1]
aliases: [Hooks, useState, useEffect, useRef, useMemo, useCallback, useContext]
---

# CC_React Hooks

> Un Hook es una función especial de React que empieza con `use` y permite "engancharte" a las funcionalidades internas de React (estado, ciclo de vida, referencias) desde un **componente funcional**.

## Las 3 reglas de los Hooks

| # | Regla | Por qué |
|---|---|---|
| 1 | **Solo en componentes o en otros hooks** | No se pueden llamar dentro de funciones normales, loops o condicionales |
| 2 | **Siempre al nivel superior** | Tienen que estar al principio del componente, nunca dentro de un `if` o un `for` |
| 3 | **Siempre empiezan con `use`** | Convención que le indica a React (y al linter) que es un hook — permite validar las reglas 1 y 2 automáticamente |

> Romper la regla 2 (por ejemplo, un `useState` dentro de un `if`) hace que React pierda la cuenta de en qué orden se llaman los hooks entre renders, y todo el estado se desincroniza.

## Hooks que se usan en la materia

| Hook | Para qué |
|---|---|
| `useState` | Estado local |
| `useEffect` | Efectos / ciclo de vida |
| `useRef` | Referencias sin re-render |
| `useContext` | Estado global |
| `useMemo` | Optimización de cálculos |
| `useCallback` | Optimización de funciones |

---

## useState — estado y re-render

```jsx
const [cupos, setCupos] = useState(20);

// Al reservar:
setCupos(cupos - 1);
// → React detecta el cambio y vuelve a renderizar
```

```
Estado cambia → React compara → Re-render eficiente
```

| Idea clave | Detalle |
|---|---|
| **Inmutabilidad** | Nunca mutés el estado directamente (`cupos--` no funciona). Siempre usá el setter (`setCupos(...)`) para que React detecte el cambio |
| **Re-render en cascada** | Cuando el estado de un padre cambia, **todos sus hijos se re-renderizan** (salvo optimizaciones como `useMemo`/`useCallback`) |
| **Estado local vs global** | `useState` es local al componente. Para compartir estado entre componentes se usa `useContext` o librerías externas |

---

## useEffect

Sirve para **sincronizar un componente con algo externo**: una API, un timer, un evento del navegador.

```jsx
// 1. Sin dependencias → corre al montar
useEffect(() => {
    fetchClases();
}, []);

// 2. Con dependencia → corre cuando cambia 'sede'
useEffect(() => {
    filtrarPorSede(sede);
}, [sede]);

// 3. Con cleanup → se llama al desmontar
useEffect(() => {
    const intervalo = setInterval(tick, 1000);
    return () => clearInterval(intervalo);
}, []);
```

### El array de dependencias — las 3 variantes

| Forma | Comportamiento |
|---|---|
| `[]` — array vacío | Se ejecuta **una sola vez**, al montar el componente. Ideal para cargar datos iniciales de una API |
| `[variable]` — con dependencia | Se ejecuta **cada vez que esa variable cambia** |
| `return () => { }` — cleanup | La función que se retorna se llama **al desmontar**. Evita memory leaks (timers colgados, listeners sin remover) |

> ⚠️ **No uses `useEffect`** para cosas que no son sincronización con algo externo — por ejemplo, para calcular datos que ya tenés disponibles al renderizar. Eso se calcula directo en el cuerpo del componente, o con `useMemo` si es costoso.

---

## Otros hooks y patrones

### useRef

Referencia a un elemento **sin disparar re-render**. Útil para inputs, timers y valores que necesitás persistir entre renders sin que eso dispare un nuevo render.

```jsx
const inputRef = useRef(null);
inputRef.current.focus();
```

> A diferencia de `useState`, cambiar `.current` **no** vuelve a renderizar el componente.

### useMemo

Memoriza el **resultado** de un cálculo costoso. Solo se recalcula si cambia alguna dependencia.

```jsx
const total = useMemo(
    () => calcularTotal(items),
    [items]
);
```

### useCallback

Memoriza una **función** para que no se recree en cada render. Evita re-renders innecesarios en componentes hijos que reciben esa función como prop.

```jsx
const handlePress = useCallback(
    () => reservar(id),
    [id]
);
```

> `useMemo` memoriza un **valor**, `useCallback` memoriza una **función**. Ambos evitan trabajo repetido entre renders.

### Render condicional + listas (repaso, se usa constantemente junto a los hooks)

```jsx
{cargando && <Spinner />}
{items.map(i => (
    <Item key={i.id} data={i} />
))}
```

---

## Cuándo usar cada uno — resumen

| Necesito... | Hook |
|---|---|
| Guardar un valor que cambia y re-renderizar cuando cambia | `useState` |
| Ejecutar código cuando el componente aparece, cambia algo, o desaparece | `useEffect` |
| Guardar una referencia (input, timer) sin re-renderizar | `useRef` |
| Compartir estado entre componentes lejanos en el árbol | `useContext` |
| Evitar recalcular algo costoso en cada render | `useMemo` |
| Evitar recrear una función en cada render | `useCallback` |

---

## Relacionado

- [[Clase 07 - React Native y Expo]]
- [[React]]
- [[React Native]]
