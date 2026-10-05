---
tags: [clase, react, react-native, expo, javascript, desarrollo-aplicaciones-1]
---
![[ReactNative_Intro.pdf]]
# Clase 07 — React Native: Conceptos básicos y setup con Expo

**Materia:** Desarrollo de Aplicaciones 1 — 2C 2026

> *React · React Native · Expo*

---

## Bloque 1 · [[React|Fundamentos de React]]

| Concepto | Detalle |
|---|---|
| **Librería de UI** | Creada por Meta. Se ocupa solo de la capa de vista — no es un framework completo |
| **Componentes** | La UI se construye como un árbol de piezas reutilizables e independientes |
| **Declarativo** | Describís cómo debe verse el estado, React se encarga de actualizar el DOM |
| **Virtual DOM** | Compara cambios en memoria antes de tocar el DOM real → renders eficientes |

---

## [[React#Componentes y JSX|Componentes & JSX]]

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
| **`className`, no `class`** | Los atributos van en camelCase. `class` → `className`, `for` → `htmlFor` |

**Llaves `{ }` — ventana a JavaScript:**

```jsx
const hora = "18:00";
<h2>Clase a las {hora}</h2>
```

---

## [[React#Props, condicional y listas|Props · Condicional · Listas]]

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
// Con && (solo si es true):
{cupos > 0 && <Boton>Reservar</Boton>}

// Con ternario:
{cargando ? <Spinner /> : <Lista />}
```

### Renderizado de listas

Usá `.map()` para transformar un array en JSX. Cada elemento necesita una prop `key` única para que React rastree los cambios.

```jsx
const clases = [
    { id: 1, nombre: "Yoga" },
    { id: 2, nombre: "Funcional" },
];

// ✅ key obligatoria
{clases.map(c => (
    <ClaseCard key={c.id} titulo={c.nombre} />
))}

// ❌ Sin key → warning + bugs
```

---

## [[React#Pureza y árbol de UI|Pureza & Árbol de UI]]

### Componentes puros

Un componente es puro si: **no modifica variables fuera de sí mismo** y **misma entrada → misma salida siempre**.

```jsx
// ❌ Impuro: modifica variable externa
let contador = 0;
function Cup() {
    contador++;
    return <p>Taza #{contador}</p>;
}

// ✅ Puro: recibe lo que necesita
function Cup({ numero }) {
    return <p>Taza #{numero}</p>;
}
```

### La UI como árbol

React modela la UI como un árbol de componentes. El estado fluye hacia **abajo** (props), los eventos hacia **arriba** (callbacks).

```
        App
       /   \
   Header  Screen
     |       |
   Card    Card

   props ↓  |  eventos ↑
```

---

## [[React Hooks|¿Qué es un Hook?]]

Un Hook es una función especial de React que empieza con `use` y permite "engancharte" a las funcionalidades internas de React desde un componente.

### Reglas de los Hooks

| # | Regla | Detalle |
|---|---|---|
| 1 | Solo en componentes o en otros hooks | No los llamés dentro de funciones normales, loops o condicionales |
| 2 | Siempre al nivel superior | Tienen que estar al principio del componente, no dentro de un `if` o un `for` |
| 3 | Siempre empiezan con `use` | Es la convención que le indica a React (y al linter) que es un hook |

### Hooks que se usan en la materia

| Hook | Para qué |
|---|---|
| `useState` | Estado local |
| `useEffect` | Efectos / ciclo |
| `useRef` | Referencias |
| `useContext` | Estado global |
| `useMemo` | Optimización |

> Desarrollo completo de cada uno con código: [[React Hooks]]

---

## [[React Hooks#useState — estado y re-render|Estado y re-render (useState)]]

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
| **Inmutabilidad** | Nunca mutés el estado directamente. Siempre usá el setter para que React detecte el cambio |
| **Re-render en cascada** | Cuando el estado de un padre cambia, todos sus hijos se re-renderizan (salvo optimizaciones) |
| **Estado local vs global** | `useState` es local al componente. Para compartir estado entre componentes se usa Context o librerías |

---

## [[React Hooks#useEffect|useEffect]]

Sincroniza un componente con algo externo (API, timer, evento del navegador...).

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

| Array de dependencias | Comportamiento |
|---|---|
| `[]` — array vacío | Se ejecuta **una sola vez**, al montar. Ideal para cargar datos iniciales de una API |
| `[variable]` — con dependencia | Se ejecuta **cuando cambia** esa variable |
| `return () => { }` — cleanup | Se limpia al desmontar. Evita memory leaks |

> ⚠️ No uses `useEffect` para cosas que no son sincronización con algo externo — por ejemplo, para calcular datos que ya tenés disponibles al renderizar.

---

## [[React Hooks#Otros hooks y patrones|Otros hooks y patrones clave]]

| Hook | Para qué | Ejemplo |
|---|---|---|
| `useRef` | Referencia a un elemento sin disparar re-render | `const inputRef = useRef(null); inputRef.current.focus();` |
| `useMemo` | Memoriza el resultado de un cálculo costoso | `const total = useMemo(() => calcularTotal(items), [items]);` |
| `useCallback` | Memoriza una función para que no se recree en cada render | `const handlePress = useCallback(() => reservar(id), [id]);` |

### Render condicional + listas (repaso)

```jsx
{cargando && <Spinner />}
{items.map(i => (
    <Item key={i.id} data={i} />
))}
```

---

## [[React Native|¿Qué es React Native?]]

**"Learn once, write anywhere"** — creá apps nativas para Android, iOS y más usando React.

| Idea | Detalle |
|---|---|
| **Escrito en JS, renderizado con código nativo** | Los primitivos de React se mapean directamente a los componentes nativos de cada plataforma |
| **No es una WebView** | `<View>`, `<Text>`, `<Image>` se mapean a `android.view/UIView`, `TextView/UILabel`, `ImageView/UIImageView` |
| **Empezá con un framework: Expo** | RN no prescribe routing ni acceso a APIs de plataforma. La doc oficial recomienda [[Expo]] para apps nuevas |

```jsx
function Pantalla() {
    return (
        <View>
            <Text>Hola mundo 👋</Text>
        </View>
    );
}
```

### El problema que resuelve

| | Android nativo | Web híbrida (Cordova, Ionic) | React Native |
|---|---|---|---|
| Tipo | Nativo | Híbrido | Cross |
| Ventajas | Máximo rendimiento, acceso total a APIs | Un solo código, HTML+CSS+JS | Un código, nativo real, JS + React (conocido) |
| Desventajas | Solo Android, Java/Kotlin | WebView = no nativo, rendimiento limitado | Algo más complejo que web, algunas APIs aún nativas |

### Casos de uso reales

Facebook · Instagram · Shopify · Discord · Microsoft (Outlook, Teams) · Wix

> **¿Cuándo NO conviene?** → Apps con gráficos 3D intensivos, acceso muy bajo nivel al hardware, o rendimiento crítico extremo (videojuegos).

---

## [[Expo|¿Qué es Expo?]]

Framework **full-stack** para React Native con servicios en la nube para moverse más rápido en cada etapa del ciclo de vida de la app.

| Pieza | Qué ofrece |
|---|---|
| **Expo SDK** | +100 librerías listas para producción: cámara, GPS, notificaciones push, biometría, deep links |
| **Developer Experience** | Expo Go para probar al instante, Expo Orbit para lanzar emuladores sin Android Studio, hot reload |
| **EAS (Expo App Services)** | Build, Submit, Update — compila en la nube y manda a los stores |
| **Recomendado por Meta** | El único framework nativo oficial recomendado por Meta para React Native |

### Expo Go vs Development Build

| | Expo Go | Development Build |
|---|---|---|
| Qué es | Playground, sin compilar | Tu propio "Expo Go" personalizado |
| Instalar Android Studio/Xcode | No | Sí (una sola vez, para compilar) |
| Librerías nativas custom | Solo las incluidas en Expo Go | Cualquiera |
| Cambiar ícono/splash/nombre | No | Sí |
| Hot reload | Sí | Sí |

> Desarrollo completo del flujo de Expo Go y primer proyecto: [[Expo]]

### Primer proyecto

```bash
npx create-expo-app Example
```

| Comando | Qué hace |
|---|---|
| `npx expo start` | Levanta Metro y muestra el QR para Expo Go |
| `npx expo start --android` | Abre directamente en emulador Android |
| `npx expo install <pkg>` | Instala librerías compatibles con tu versión del SDK |
| `npx expo run:android` | Compila y corre como Development Build en Android |

---

## Relacionado

- [[React]]
- [[React Hooks]]
- [[React Native]]
- [[Expo]]
- [[Aplicaciones Nativas]]
- [[Aplicaciones Hibridas]]
