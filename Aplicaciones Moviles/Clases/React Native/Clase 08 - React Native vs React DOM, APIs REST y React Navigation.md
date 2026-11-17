---
tags: [clase, react-native, react-dom, axios, react-navigation, desarrollo-aplicaciones-1]
---
![[ReactNative - Axios y Navigation.pdf]]
# Clase 08 — React Native vs React DOM, APIs REST y React Navigation

**Materia:** Desarrollo de Aplicaciones 1 — 2C 2026

> *React DOM · Axios · React Navigation*

---

## Agenda

| #   | Bloque                                                                      | Contenido                                                         |
| --- | --------------------------------------------------------------------------- | ----------------------------------------------------------------- |
| 01  | [[React Native#Diferencias con React DOM (Web)\|React Native vs React DOM]] | Scaffold · Similitudes · Diferencias en UI, eventos, estilos y SO |
| 02  | [[Axios y fetch API\|Comunicación con servicios REST]]                      | fetch API · Axios · Interceptors                                  |
| 03  | [[React Navigation]]                                                        | Tipos de navegación · Props & Hooks · Paso de parámetros          |
|     |                                                                             |                                                                   |

---

## Bloque 01 · React Native & React DOM

### [[React Native#Scaffold — estructura de carpetas recomendada|Scaffold — estructura de carpetas recomendada]]

| Carpeta | Contiene |
|---|---|
| `components/` | UI pura, sin lógica (botones, inputs, tarjetas) |
| `screens/` | Cada pantalla de navegación (Home, Login...) |
| `navigation/` | Stacks, tabs y routers — ver [[React Navigation]] |
| `services/` | Comunicación con APIs REST/GraphQL — ver [[Axios y fetch API]] |
| `hooks/` | Hooks reutilizables (`useAuth`, `useForm`...) — ver [[React Hooks]] |
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

> Desarrollo completo con ejemplos de código: [[React Native]]

---

### [[React Native#Similitudes con React Web — base compartida|Similitudes — base compartida]]

> Lo que se aprendió en React Web funciona **igual** en React Native. La lógica no cambia; cambia el entorno de ejecución.

| Concepto | Detalle |
|---|---|
| JSX | Sintaxis de componentes — ver [[React#Componentes y JSX]] |
| Componentes funcionales | `function App() {}` |
| Props & Estado | Flujo de datos unidireccional — ver [[React#Props, condicional y listas]] |
| Hooks | `useState` · `useEffect` · `useContext` — desarrollo completo en [[React Hooks]] |
| Ciclo de vida | mount · update · unmount |

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

Validaciones, formularios, contextos y hooks personalizados se escriben **exactamente igual**. El código de negocio es 100% portable:

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
// En React Web 🖥 — mismo hook
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

> Este mismo patrón (`useFetch` + fetch/Axios) se desarrolla a fondo en [[Axios y fetch API#Uso típico dentro de un hook]].

---

### [[React Native#Renderizado de la UI|Diferencias — Renderizado de la UI]]

React Native **no corre en un navegador**. Corre directamente sobre el SO. Por eso no existe HTML ni DOM: los componentes JSX se traducen a **vistas nativas reales**.

| React DOM (Web) | React Native |
|---|---|
| Se monta sobre el DOM del navegador | — |
| Punto de entrada: `<div id="root">` | — |
| `ReactDOM.createRoot(...).render()` | Motor: JSI + Fabric Renderer + Hermes |
| JSX → elementos HTML reales | JSX → vistas nativas reales |
| Motor: Blink/WebKit | Motor: Fabric + Yoga |

| Web | React Native |
|---|---|
| `<div>` | `<View>` |
| `<p>` | `<Text>` |
| `<img>` | `<Image>` |
| `<input>` | `<TextInput>` |
| `<button>` | `<TouchableOpacity>` |

#### [[React Native#Del JSX a la pantalla — el recorrido completo|Del JSX a la pantalla — el recorrido completo]]

| # | Etapa | Qué sucede | Quién participa |
|---|---|---|---|
| 1 | Build | Babel traduce el JSX, Metro empaqueta el bundle (Hermes lo precompila en release) | Babel · Metro · Hermes |
| 2 | Ejecución JS | Hermes ejecuta el bundle. React arma el árbol de elementos | Hermes · React (hilo JS) |
| 3 | Shadow Tree | React le pide a Fabric un nodo por elemento, vía JSI | React → JSI → Fabric |
| 4 | Layout | Yoga calcula posición y tamaño con reglas de Flexbox | Fabric · Yoga (hilo de fondo) |
| 5 | Diff | Fabric compara el árbol nuevo con el anterior | Fabric (hilo de fondo) |
| 6 | Mount | Se instancian las vistas nativas reales | Fabric, capa Android (hilo UI) |
| 7 | Dibujo | `measure → layout → draw` | Sistema Android (hilo UI) |

> Con un `setState` se repiten los pasos 2 a 6 — el diff produce un único cambio (ej: actualizar un texto).
>
> **¿Qué hay en el APK del development build?** El motor nativo (Hermes, Fabric, Yoga) + el JS propio. La UI no se traduce a nativo en build time — se construye en **runtime**.
>
> Tabla completa y fuente oficial: [[React Native#Del JSX a la pantalla — el recorrido completo]]

---

### [[React Native#Manejo de eventos|Diferencias — Manejo de eventos]]

En la web hay mouse, teclado y scroll de navegador. En móvil hay **dedo**.

| React DOM (Web) | React Native |
|---|---|
| `onClick` | `onPress` (≈ click — cualquier tap) |
| `onChange` | `onChangeText` (devuelve string directo) |
| `onSubmit` | Se maneja en el botón con `onPress` |
| `onMouseOver` / `onMouseEnter` | ❌ No existe — no hay mouse |
| `onFocus` / `onKeyDown` | `onFocus` / `onBlur` en `TextInput` |

---

### [[React Native#Estilos|Diferencias — Estilos]]

En la web Flexbox es una opción. En React Native es el **único sistema de layout**. Las propiedades CSS se convierten a camelCase.

| React DOM (Web) | React Native |
|---|---|
| CSS externo (`.css`, `className`) | Sin CSS externo, sin `className` |
| Bootstrap / Tailwind / SCSS | Objetos JS: `style={{ ... }}` |
| Selectores: `.clase`, `#id`, `:hover` | `StyleSheet.create({ ... })` |
| Media queries nativas | Solo Flexbox (`direction: column`) |
| Float, Grid, Flexbox, position | `Dimensions` / libs para responsive |

**Conversión CSS → JS:**

| CSS | React Native |
|---|---|
| `background-color` | `backgroundColor` |
| `font-size` | `fontSize` |
| `border-radius` | `borderRadius` |
| `padding-top` | `paddingTop` |
| `z-index` | `zIndex` |

---

### [[React Native#Acceso al sistema operativo|Diferencias — Acceso al Sistema Operativo]]

| React DOM (Web) | React Native |
|---|---|
| `localStorage` / `sessionStorage` | `AsyncStorage` / `SecureStore` |
| `window.innerHeight` / `innerWidth` | `Dimensions.get('window').width` |
| `navigator.geolocation.getCurrentPosition()` | `expo-location` / `@react-native-community/geolocation` |
| `addEventListener('scroll')` | — |
| `document.querySelector` | `expo-camera` / `react-native-camera` |
| — | `expo-notifications` / FCM / OneSignal |

> Estos módulos nativos vienen del [[Expo#Expo SDK|Expo SDK]]. Desarrollo completo de todo este bloque, con el pipeline de render detallado: [[React Native]]

---

## Bloque 02 · Comunicación con APIs REST

### [[Axios y fetch API#fetch API — peticiones HTTP nativas|fetch API — nativa de JavaScript]]

```javascript
// GET
fetch('https://api.example.com/data')
    .then(response => {
        if (!response.ok) throw new Error('Error en la petición');
        return response.json();
    })
    .then(data => console.log('Datos:', data))
    .catch(error => console.error('Error:', error));

// POST
fetch('https://api.example.com/submit', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ name: 'John', age: 30 })
})
    .then(res => res.json())
    .then(data => console.log(data))
    .catch(err => console.error(err));
```

> ⚠️ `fetch` no rechaza la promesa en errores HTTP (404, 500) — hay que chequear `response.ok` a mano. Comparación completa con Axios: [[Axios y fetch API#Comparación directa fetch vs Axios]]

### [[Axios y fetch API#Axios — librería HTTP con superpoderes|Axios — librería HTTP con superpoderes]]

```bash
npm install axios
```

| ✓ Ventaja | Detalle |
|---|---|
| Async/Await nativo | — |
| Manejo automático JSON | `response.data` ya es JSON |
| Interceptors de request/response | — |
| Interfaz más simple que fetch | — |

```javascript
axios.get('https://api.example.com/data')
    .then(response => {
        console.log(response.data); // ya es JSON
    })
    .catch(error => console.error('Error:', error));
```

### [[Axios y fetch API#Interceptors — la pieza clave para JWT|Interceptors — agregar el token automáticamente]]

```javascript
axios.interceptors.request.use(
    config => {
        config.headers.Authorization = 'Bearer ' + token;
        return config;
    },
    error => Promise.reject(error)
);
```

> Mismo problema y misma solución conceptual que el [[Hilt#Interceptor de OkHttp — agregar el token automáticamente|interceptor de OkHttp]] con [[Hilt]] en Android nativo — ambos agregan el header `Authorization` sin repetir código en cada llamada. Repasar el flujo del token: [[JWT]].
>
> Desarrollo completo con interceptor de response y manejo de 401: [[Axios y fetch API#Interceptor de response — manejar 401 globalmente]]

---

## Bloque 03 · React Navigation

### [[React Navigation#Los 3 tipos de navegador|¿Qué es?]]

Librería **oficial y recomendada** para navegar entre pantallas. React Native **no incluye navegación por defecto** — a diferencia de la web, donde existen rutas como `/home`, `/about`.

| Navegador | Uso típico |
|---|---|
| **Stack Navigator** | Pilas de pantallas, navegar adelante/atrás |
| **Tab Navigator** | Navegación con pestañas, como Instagram |
| **Drawer Navigator** | Menú deslizable, como Gmail |

### [[React Navigation#Los 3 componentes obligatorios|Los 3 componentes obligatorios]]

| Componente | Dónde va | Qué hace |
|---|---|---|
| `NavigationContainer` | `App.js` (raíz) | Contexto global que gestiona el estado de navegación |
| `<X>.Navigator` | `navigation/AppNavigator.jsx` | Define el tipo de navegación (Stack, Tab, Drawer) |
| `<X>.Screen` | Dentro del Navigator | Registra cada pantalla con su nombre y componente |

> ⚠️ **Sin `NavigationContainer`**: cualquier intento de navegar lanza `Couldn't find a navigation object`. Siempre debe ser el componente raíz.

```jsx
// App.js
import { NavigationContainer } from '@react-navigation/native';
import AppNavigator from './navigation/AppNavigator';

export default function App() {
    return (
        <NavigationContainer>
            <AppNavigator />
        </NavigationContainer>
    );
}
```

```jsx
// navigation/AppNavigator.jsx
import { createNativeStackNavigator } from '@react-navigation/native-stack';
const Stack = createNativeStackNavigator();

export default function AppNavigator() {
    return (
        <Stack.Navigator initialRouteName="Home">
            <Stack.Screen name="Home" component={HomeScreen} options={{ title: 'PokeExplorer' }} />
            <Stack.Screen name="PokemonList" component={PokemonListScreen} options={{ title: 'Pokedex' }} />
            <Stack.Screen
                name="PokemonDetail"
                component={PokemonDetailScreen}
                options={({ route }) => ({ title: route.params.name })}
            />
        </Stack.Navigator>
    );
}
```

> Código completo con `screenOptions` y la tabla de propiedades: [[React Navigation#NavigationContainer y AppNavigator — código completo]]

### [[React Navigation#Paso de parámetros entre pantallas|Paso de parámetros entre pantallas]]

```jsx
// Quien envía
navigation.navigate('PokemonDetail', { name: item.name });

// Quien recibe
const { name } = route.params;
```

> Código completo de ambas pantallas (quien envía y quien recibe): [[React Navigation#Paso de parámetros entre pantallas]]

### [[React Navigation#El prop navigation|El prop navigation]]

| Método | Qué hace |
|---|---|
| `navigation.navigate('Pantalla')` | Ir a una pantalla |
| `navigation.goBack()` | Volver atrás |
| `navigation.replace('Otra')` | Reemplazar la pantalla actual |
| `navigation.navigate('P', { id: 5 })` | Navegar pasando parámetros |

### [[React Navigation#Hooks de navegación — useNavigation y useRoute|Hooks de navegación]]

| Hook | Para qué |
|---|---|
| `useNavigation()` | Navegar desde componentes que no son pantalla |
| `useRoute()` | Leer parámetros desde cualquier componente |
| `useFocusEffect()` | Ejecutar un efecto al enfocar la pantalla |
| `useIsFocused()` | Saber si la pantalla está visible |

> Tabla resumen completa de hooks y props, con ejemplos de código: [[React Navigation#Resumen — Hooks & Props de React Navigation]]
>
> Desarrollo completo y comparación con [[Navigation Component]] de Android: [[React Navigation]]

---

## Relacionado

- [[Clase 07 - React Native y Expo]]
- [[React Native]]
- [[Axios y fetch API]]
- [[React Navigation]]
- [[React Hooks]]
- [[React]]
- [[Expo]]
- [[JWT]]
- [[Hilt]]
- [[Navigation Component]]
