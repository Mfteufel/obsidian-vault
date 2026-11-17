---
tags: [react-native, javascript, mobile, cross-platform, desarrollo-aplicaciones-1]
aliases: [React Native, RN, Learn once write anywhere, React DOM, Scaffold React Native]
---

# CC_React Native

> **"Learn once, write anywhere"** — framework de Meta para crear apps **nativas** (no híbridas) para Android, iOS y más, usando [[React]].

## La idea central

React Native está escrito en JavaScript, pero se **renderiza con código nativo real**. Los primitivos de React se mapean directamente a los componentes nativos de cada plataforma — **no es una WebView**.

| Componente React Native | Se mapea a (Android) | Se mapea a (iOS) |
|---|---|---|
| `<View>` | `android.view` | `UIView` |
| `<Text>` | `TextView` | `UILabel` |
| `<Image>` | `ImageView` | `UIImageView` |

```jsx
function Pantalla() {
    return (
        <View>
            <Text>Hola mundo 👋</Text>
        </View>
    );
}
```

> La app usa las **mismas APIs nativas** que cualquier otra app de la plataforma — el rendimiento y el look & feel son nativos, no una capa web disfrazada.

---

## El problema que resuelve

| | **Android nativo** | **Web híbrida** (Cordova, Ionic) | **React Native** |
|---|---|---|---|
| Categoría | Nativo | Híbrido | Cross-platform |
| ✓ Ventajas | Máximo rendimiento, acceso total a APIs | Un solo código, HTML + CSS + JS | Un código, nativo real, JS + React (conocido) |
| ✗ Desventajas | Solo Android, hay que saber Java/Kotlin | WebView = no nativo, rendimiento limitado | Algo más complejo que web, algunas APIs aún requieren código nativo |

> React Native ocupa el punto intermedio: **un código compartido, pero renderizado nativamente** — a diferencia de una WebView que solo simula ser nativa.

---

## RN no prescribe todo — por eso existe Expo

React Native por sí solo **no trae**:
- Sistema de routing/navegación
- Acceso simplificado a APIs de plataforma (cámara, GPS, notificaciones)
- Herramientas de build para los stores

La propia documentación oficial de React Native **recomienda [[Expo]]** para empezar cualquier app nueva — Expo agrega todo eso encima de RN.

```bash
# Recomendado por reactnative.dev:
npx create-expo-app MiApp
npx expo start
```

---

## Scaffold — estructura de carpetas recomendada

| Carpeta | Contiene |
|---|---|
| `components/` | UI pura, sin lógica (botones, inputs, tarjetas) |
| `screens/` | Cada pantalla de navegación (Home, Login...) |
| `navigation/` | Stacks, tabs y routers — ver [[React Navigation]] |
| `services/` | Comunicación con APIs REST/GraphQL — ver [[Axios y fetch API]] |
| `hooks/` | Hooks reutilizables (`useAuth`, `useForm`...) |
| `context/` | React Contexts (login, dark mode...) |
| `store/` | Redux / Zustand — store global |
| `utils/` | Helpers: validaciones, formateo de fechas |
| `config/` | Constantes, temas, colores globales |

```
my-app/
├── components/
│   └── Button.jsx
├── screens/
│   ├── Home.jsx
│   └── Login.jsx
├── navigation/
├── services/
├── hooks/
├── context/
├── utils/
└── config/
```

---

## Similitudes con React Web — base compartida

> Lo que se aprende en React Web funciona **igual** en React Native. La lógica no cambia; cambia el **entorno de ejecución**.

| Concepto | Detalle |
|---|---|
| **JSX** | Misma sintaxis de componentes |
| **Componentes funcionales** | `function App() {}` |
| **Props & Estado** | Flujo de datos unidireccional |
| **Hooks** | `useState` · `useEffect` · `useContext` — ver [[React Hooks]] |
| **Ciclo de vida** | mount · update · unmount |

```jsx
// Funciona IGUAL en Web y en React Native
import { useState, useEffect } from 'react';

export default function Ejemplo() {
    const [datos, setDatos] = useState([]);
    const [cargando, setCargando] = useState(true);

    useEffect(() => {
        fetch('https://api.ejemplo.com/items')
            .then(r => r.json())
            .then(data => {
                setDatos(data);
                setCargando(false);
            });
    }, []); // [] = solo al montar

    if (cargando) return <Loading />;
    return <Lista items={datos} />;
}
```

### Patrones 100% portables

Validaciones, manejo de formularios, contextos y **hooks personalizados** se escriben exactamente igual. Solo cambian los componentes de UI que devuelven:

```jsx
// hooks/useFetch.js — reutilizable Web / RN
export function useFetch(url) {
    const [data, setData] = useState(null);
    const [error, setError] = useState(null);
    const [loading, setLoading] = useState(true);

    useEffect(() => {
        fetch(url)
            .then(r => r.json())
            .then(setData)
            .catch(setError)
            .finally(() => setLoading(false));
    }, [url]);

    return { data, error, loading };
}
```

```jsx
// En React Web 🖥
const { data } = useFetch('/api/items');
return <ul>{data?.map(i => <li key={i.id}>{i.name}</li>)}</ul>;

// En React Native 📱 — mismo hook, distintos componentes
const { data } = useFetch('/api/items');
return (
    <FlatList
        data={data}
        keyExtractor={i => i.id}
        renderItem={({ item }) => <Text>{item.name}</Text>}
    />
);
```

---

## Diferencias con React DOM (Web)

### Renderizado de la UI

React Native **no corre en un navegador** — corre directamente sobre el SO. Por eso no existe HTML ni DOM: los componentes JSX se traducen a **vistas nativas reales**.

| React DOM (Web) | React Native |
|---|---|
| Se monta sobre el DOM del navegador | Se monta sobre el SO, vía Fabric |
| Punto de entrada: `<div id="root">` | Punto de entrada: `AppRegistry.registerComponent` |
| `ReactDOM.createRoot(...).render()` | Motor: JSI + Fabric Renderer + Hermes |
| JSX → elementos HTML reales | JSX → vistas nativas reales |
| Motor de renderizado: Blink/WebKit | Motor: Fabric (C++) + Yoga (layout) |

| Elemento Web | Equivalente React Native |
|---|---|
| `<div>` | `<View>` |
| `<p>` | `<Text>` |
| `<img>` | `<Image>` |
| `<input>` | `<TextInput>` |
| `<button>` | `<TouchableOpacity>` |

#### Del JSX a la pantalla — el recorrido completo

| # | Etapa | Qué sucede | Quién participa |
|---|---|---|---|
| 1 | **Build** | Babel traduce el JSX a llamadas de función. Metro empaqueta todo en un bundle (en release, Hermes lo precompila a bytecode) | Babel · Metro · Hermes |
| 2 | **Ejecución JS** | Hermes ejecuta el bundle. React llama al componente y obtiene un árbol de elementos JS que describe la interfaz | Hermes · React (hilo JS) |
| 3 | **Shadow Tree** | React le pide a Fabric un nodo por cada elemento, vía JSI (sin puente serializado) | React → JSI → Fabric |
| 4 | **Layout** | Yoga recorre el Shadow Tree y aplica las reglas de Flexbox para calcular posición y tamaño | Fabric · Yoga (hilo de fondo) |
| 5 | **Diff** | Fabric compara el árbol nuevo con el anterior y arma la lista de cambios a aplicar | Fabric (hilo de fondo) |
| 6 | **Mount** | Las instrucciones se ejecutan en el hilo UI de Android: se instancian las vistas nativas reales | Fabric, capa Android (hilo UI) |
| 7 | **Dibujo** | Android hace su ciclo habitual `measure → layout → draw` | Sistema Android (hilo UI) |

> Con un `setState`, se repiten los pasos 2 a 6 y el diff produce solo el cambio necesario (ej: actualizar un texto) — no se reconstruye toda la pantalla.
>
> **¿Qué hay en el APK del development build?** El motor nativo (Hermes, Fabric, Yoga) + el JS propio. La UI **no se traduce a código nativo en build time** — se construye en runtime.
>
> Fuente: reactnative.dev/architecture/render-pipeline

### Manejo de eventos

En la web hay mouse, teclado y scroll de navegador. En móvil hay **dedo**. El sistema de eventos de React Native refleja eso.

| React DOM (Web) | React Native |
|---|---|
| `onClick` | `onPress` (≈ click — cualquier tap) |
| `onChange` | `onChangeText` (devuelve el string directo, no un evento) |
| `onSubmit` | Se maneja en el botón con `onPress` |
| `onMouseOver` / `onMouseEnter` | ❌ No existe — no hay mouse |
| `onFocus` / `onKeyDown` | `onFocus` / `onBlur` en `TextInput` |

### Estilos

En la web, Flexbox es una opción entre varias. En React Native es el **único sistema de layout**. Y la propiedad CSS se convierte a **camelCase**.

| React DOM (Web) | React Native |
|---|---|
| CSS externo (`.css`, `className`) | Sin CSS externo, sin `className` |
| Bootstrap / Tailwind / SCSS | Objetos JS: `style={{ ... }}` |
| Selectores: `.clase`, `#id`, `:hover` | `StyleSheet.create({ ... })` |
| Media queries nativas | Solo Flexbox (`direction: column` por defecto) |
| Float, Grid, Flexbox, position | `Dimensions` / libs para responsive |

**Conversión de propiedades CSS → JavaScript (camelCase):**

| CSS | React Native |
|---|---|
| `background-color` | `backgroundColor` |
| `font-size` | `fontSize` |
| `border-radius` | `borderRadius` |
| `padding-top` | `paddingTop` |
| `z-index` | `zIndex` |

### Acceso al sistema operativo

En la web el entorno es el navegador. En React Native el entorno es el **sistema operativo real**: cámara, GPS, notificaciones, almacenamiento nativo.

| React DOM (Web) | React Native |
|---|---|
| `localStorage` / `sessionStorage` | `AsyncStorage` / `SecureStore` |
| `window.innerHeight` / `innerWidth` | `Dimensions.get('window').width` |
| `navigator.geolocation.getCurrentPosition()` | `expo-location` / `@react-native-community/geolocation` |
| `addEventListener('scroll')` | — (no aplica igual, cada componente scrolleable maneja su propio evento) |
| `document.querySelector` | `expo-camera` / `react-native-camera` para cámara |
| — | `expo-notifications` / FCM / OneSignal para push |

---

## Casos de uso reales

| Empresa | Uso de React Native |
|---|---|
| **Facebook** | App original de Meta — uno de los primeros casos de RN en producción masiva |
| **Instagram** | Pantallas de notificaciones y vistas secundarias migradas a RN |
| **Shopify** | App de merchants reescrita en RN — redujo drásticamente el tiempo de desarrollo |
| **Discord** | Usado en partes de la app móvil para compartir código entre plataformas |
| **Microsoft** | Outlook Mobile y partes de Teams usan React Native |
| **Wix** | Apostó fuerte a RN y contribuye activamente al ecosistema open source |

### ¿Cuándo NO conviene React Native?

- Apps con **gráficos 3D intensivos**
- Acceso muy **bajo nivel al hardware**
- **Rendimiento crítico extremo** (videojuegos)

En esos casos, nativo puro (Android/Kotlin o iOS/Swift) sigue siendo la mejor opción.

---

## Relación con lo visto en Android nativo

| Concepto en Android nativo (Java) | Equivalente conceptual en React Native |
|---|---|
| [[Actividad|Activity]] / [[Fragment]] | Componente funcional de React |
| [[Intents]] / [[Navigation Component]] | [[React Navigation]] / Expo Router |
| [[Retrofit - Consumo de API REST]] | `fetch()` / [[Axios y fetch API|Axios]] dentro de `useEffect` |
| [[Room]] | AsyncStorage / SQLite vía librerías de RN |
| [[Hilt]] (DI) | Context API / librerías como `react-native-di` (menos común en RN) |

> El TPO tiene su segunda entrega justamente en **React Native con Expo** — este mapeo ayuda a trasladar lo aprendido en Android nativo.

---

## Relacionado

- [[Clase 07 - React Native y Expo]]
- [[Clase 08 - React Native vs React DOM, APIs REST y React Navigation]]
- [[React]]
- [[React Hooks]]
- [[Expo]]
- [[Axios y fetch API]]
- [[React Navigation]]
- [[Aplicaciones Nativas]]
- [[Aplicaciones Hibridas]]
