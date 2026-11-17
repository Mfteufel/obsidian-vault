---
tags: [react, react-native, hooks, usecontext, estado-global, desarrollo-aplicaciones-1]
aliases: [Context API, useContext, AuthContext, Prop Drilling, useAuth]
---

# CC_Context API (useContext)

> Solución **nativa** de React para compartir estado entre componentes sin pasarlo manualmente nivel por nivel. Es el hook elegido en la materia para manejar la sesión del usuario en React Native.

## El problema: Prop Drilling

Sin Context, para que un componente profundo acceda a un dato (ej: el usuario logueado), **cada nivel intermedio** tiene que recibirlo y volver a pasarlo, aunque no lo use.

```
App (tiene el user)
 ↓ pasa user como prop
 Navigator
 ↓ pasa user como prop
 HomeScreen
 ↓ pasa user como prop
 Header  ← lo necesita acá
```

> Cada nivel intermedio recibe y pasa props que no usa → código frágil, difícil de mantener. Si mañana `Header` se mueve a otro lugar del árbol, hay que reescribir toda la cadena.

## La solución: Context API

```
AuthContext.Provider
 ├─ Navigator
 │   ├─ HomeScreen
 │   │   └─ Header          ← accede directo
 │   └─ ProfileScreen       ← accede directo
 └─ SettingsScreen          ← accede directo
```

Cualquier componente **consume el contexto directamente**, sin pasar props intermedias — no importa qué tan profundo esté en el árbol.

---

## useContext y las alternativas

React Context API no es la única forma de manejar estado global. Según la complejidad de la app, hay otras opciones:

| Herramienta | Curva de aprendizaje | Boilerplate | Ideal para |
|---|---|---|---|
| **useContext** | Baja | Mínimo | Auth, tema, idioma — estados simples |
| **Zustand** | Baja-Media | Bajo | Apps medianas, muy popular en RN |
| **Redux Toolkit** | Media-Alta | Moderado | Apps grandes, estado muy complejo |
| **Jotai / Recoil** | Media | Bajo | Estado atómico y granular |

> 💡 Para esta materia se usa `useContext` — es nativo, sin dependencias, y suficiente para manejar la sesión.

---

## Creando el AuthContext

```javascript
// context/AuthContext.js
import { createContext, useState, useContext } from 'react';

export const AuthContext = createContext(null);

export function AuthProvider({ children }) {
    const [user, setUser] = useState(null);

    const login = (userData) => setUser(userData);
    const logout = () => setUser(null);

    return (
        <AuthContext.Provider value={{ user, login, logout }}>
            {children}
        </AuthContext.Provider>
    );
}
```

```jsx
// App.js — envolver la app
import { AuthProvider } from './context/AuthContext';
import { NavigationContainer } from '@react-navigation/native';

export default function App() {
    return (
        <AuthProvider>
            <NavigationContainer>
                {/* ... stacks / tabs ... */}
            </NavigationContainer>
        </AuthProvider>
    );
}
```

> El `AuthProvider` debe envolver todo lo que necesite acceder a la sesión — en la práctica, casi siempre es el componente raíz, igual que [[React Navigation#Los 3 componentes obligatorios|NavigationContainer]].

---

## Consumiendo el contexto

```jsx
// screens/HomeScreen.js
import { useContext } from 'react';
import { AuthContext } from '../context/AuthContext';

export function HomeScreen() {
    const { user, logout } = useContext(AuthContext);

    return (
        <View>
            <Text>Bienvenido, {user?.nombre}</Text>
            <Button title="Cerrar sesión" onPress={logout} />
        </View>
    );
}
```

| Propiedad | Detalle |
|---|---|
| **Acceso directo** | No importa qué tan profundo esté el componente en el árbol |
| **Reactivo** | Cuando cambia el contexto, **todos los consumidores se re-renderizan automáticamente** |
| **Control de rutas** | Si `user === null` → redirigir al Login; si `user` existe → mostrar Home |

---

## El problema: el contexto vive solo en memoria RAM

Al cerrar o matar la app, **todo el estado se pierde** — el contexto no persiste por sí solo.

| | Sin persistencia | Con persistencia (Storage) |
|---|---|---|
| 1 | Usuario hace login | Login → token guardado en disco |
| 2 | Cierra la app | Cierra la app |
| 3 | Reabre → debe loguearse de nuevo | Reabre → token leído → auto-login |
| 4 | ❌ Mala experiencia de usuario | ✅ Experiencia fluida |

> La solución es combinar `useContext` con almacenamiento persistente. Desarrollo completo: [[Storage en React Native]].

### Integrando Storage + Context

```javascript
// context/AuthContext.js — con persistencia
import { createContext, useState, useEffect } from 'react';
import * as SecureStore from 'expo-secure-store';

export const AuthContext = createContext(null);

export function AuthProvider({ children }) {
    const [user, setUser] = useState(null);
    const [loading, setLoading] = useState(true);

    // Al montar: leer token guardado
    useEffect(() => {
        const restore = async () => {
            const token = await SecureStore.getItemAsync('token');
            if (token) setUser({ token }); // o decodificá el JWT
            setLoading(false);
        };
        restore();
    }, []);

    const login = async (token, userData) => {
        await SecureStore.setItemAsync('token', token);
        setUser(userData);
    };

    const logout = async () => {
        await SecureStore.deleteItemAsync('token');
        setUser(null);
    };

    return (
        <AuthContext.Provider value={{ user, loading, login, logout }}>
            {children}
        </AuthContext.Provider>
    );
}
```

> `loading` evita el "flash" de pantalla de login mientras se lee el token guardado al abrir la app.

---

## El interceptor vive fuera del árbol de React

Un problema real: el [[Axios y fetch API#Interceptors — la pieza clave para JWT|interceptor de Axios]] necesita poder hacer `logout()` cuando recibe un 401 — pero `services/api.js` **no es un componente React**, así que no puede usar `useContext(AuthContext)` ahí directamente.

```
❌ No podés usar useContext(AuthContext) acá —
   los hooks solo funcionan dentro de componentes React o custom hooks.
```

### Solución — inyectar logout como parámetro

```javascript
// services/api.js — recibe logoutFn como parámetro
export function setupInterceptors(logoutFn) {
    api.interceptors.response.use(
        (response) => response,
        async (error) => {
            if (error.response?.status === 401) {
                await SecureStore.deleteItemAsync('token');
                logoutFn(); // <-- acá usamos el parámetro
            }
            return Promise.reject(error);
        }
    );
}
```

```javascript
// context/AuthContext.js — registrar al montar el Provider
useEffect(() => {
    setupInterceptors(logout);
}, []);
```

> El patrón es: el `Provider` (que sí tiene acceso al contexto) le **pasa** la función al mundo externo, en vez de que el mundo externo intente leer el contexto.

---

## Best practice — separación de contextos

### ❌ Un solo contexto para todo

```javascript
const AppContext = createContext();
// user + theme + cart + config + ...
// Cualquier cambio en theme
// re-renderiza TODA la app,
// incluso pantallas que solo
// necesitan el user
```

Un cambio en el carrito recarga las pantallas de perfil y ajustes. **Rendimiento degradado** a medida que crece la app.

### ✅ Contextos separados

```jsx
<AuthProvider>       {/* user, login, logout */}
    <ThemeProvider>   {/* darkMode, colors */}
        <CartProvider> {/* items, addItem... */}
            <App />
        </CartProvider>
    </ThemeProvider>
</AuthProvider>

// Cada componente suscribe solo
// al contexto que necesita
```

**Re-render quirúrgico:** solo los componentes suscritos al contexto que cambió se actualizan.

---

## Custom Hook — useAuth()

Encapsular el `useContext(AuthContext)` en un hook propio evita repetir el import en cada pantalla y permite validar que se use dentro del `Provider`.

```javascript
// hooks/useAuth.js
import { useContext } from 'react';
import { AuthContext } from '../context/AuthContext';

export function useAuth() {
    const context = useContext(AuthContext);

    if (!context) {
        throw new Error('useAuth debe usarse dentro de <AuthProvider>');
    }

    return context;
}
```

```jsx
// ❌ Sin custom hook (verboso)
import { useContext } from 'react';
import { AuthContext } from '../context/AuthContext';
const { user, logout } = useContext(AuthContext);

// ✅ Con custom hook (limpio)
import { useAuth } from '../hooks/useAuth';
const { user, logout } = useAuth();

// También se puede derivar lógica:
// const { isAuthenticated } = useAuth();
// Donde isAuthenticated = !!user
```

| Ventaja del custom hook | Detalle |
|---|---|
| Encapsula el acceso al contexto | Un solo punto de entrada |
| Valida uso dentro del Provider | Lanza error claro si se usa mal, en vez de un `undefined` silencioso |
| Permite derivar lógica | `isAuthenticated`, `isAdmin`, etc. sin tocar cada componente |
| Un solo import por componente | En vez de `useContext` + `AuthContext` por separado |

---

## Errores comunes

| ❌ Qué NO hacer | Por qué | ✅ Qué hacer |
|---|---|---|
| Un solo contexto global para todo | Cada cambio re-renderiza TODA la app | Separar en contextos específicos: `AuthContext`, `ThemeContext`, etc. |
| Llamar `useContext` fuera de un componente React | Los hooks solo funcionan dentro de componentes o custom hooks | Inyectar la función necesaria como parámetro (ver interceptor arriba) |
| Olvidar envolver la app con el `Provider` | `useContext` devuelve el valor por defecto (a veces `null`) silenciosamente | Verificar con el patrón de `useAuth()` que lanza error si falta el Provider |

---

## Relacionado

- [[Clase 09 - Gestión de Sesión en React Native]]
- [[React Hooks]]
- [[Storage en React Native]]
- [[Biometría en React Native]]
- [[Axios y fetch API]]
- [[React Navigation]]
- [[JWT]]
