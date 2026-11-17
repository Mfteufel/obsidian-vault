---
tags: [clase, react-native, usecontext, storage, biometria, desarrollo-aplicaciones-1]
---
![[UseContext_Storage_Security.pdf]]
# Clase 09 — Gestión de Sesión en React Native

**Materia:** Desarrollo de Aplicaciones 1 — 2C 2026

> *Persistencia · Seguridad · Experiencia de Usuario*

---

## Agenda

| # | Bloque | Contenido |
|---|---|---|
| 01 | [[Context API (useContext)]] | Estado global · AuthContext · Consumo en componentes |
| 02 | [[Storage en React Native]] | AsyncStorage · SecureStore · Integración con Context |
| 03 | [[Biometría en React Native]] | expo-local-authentication · Disponibilidad · Prompt |

---

## Bloque 01 · useContext

### [[Context API (useContext)#El problema: Prop Drilling|¿Por qué necesitamos estado global?]]

Sin Context, cada nivel intermedio del árbol tiene que recibir y volver a pasar un dato que no usa:

```
App (tiene el user)
 ↓ pasa user como prop
 Navigator
 ↓ pasa user como prop
 HomeScreen
 ↓ pasa user como prop
 Header  ← lo necesita acá
```

> Código frágil, difícil de mantener.

### La solución: Context API

```
AuthContext.Provider
 ├─ Navigator
 │   ├─ HomeScreen
 │   │   └─ Header          ← accede directo
 │   └─ ProfileScreen       ← accede directo
 └─ SettingsScreen          ← accede directo
```

### [[Context API (useContext)#useContext y las alternativas|¿Qué es useContext? — y las alternativas]]

| Herramienta | Curva de aprendizaje | Boilerplate | Ideal para |
|---|---|---|---|
| **useContext** | Baja | Mínimo | Auth, tema, idioma — estados simples |
| Zustand | Baja-Media | Bajo | Apps medianas, muy popular en RN |
| Redux Toolkit | Media-Alta | Moderado | Apps grandes, estado muy complejo |
| Jotai / Recoil | Media | Bajo | Estado atómico y granular |

> 💡 Para este curso se usa useContext — es nativo, sin dependencias y suficiente para manejar la sesión.

### Creando el AuthContext

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

### Consumiendo el contexto

```javascript
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

| Propiedad            | Detalle                                                                            |
| -------------------- | ---------------------------------------------------------------------------------- |
| **Acceso directo**   | No importa qué tan profundo esté el componente en el árbol                         |
| **Reactivo**         | Cuando cambia el contexto, todos los consumidores se re-renderizan automáticamente |
| **Control de rutas** | Si `user === null` → redirigir al Login; si `user` existe → mostrar Home           |

> Desarrollo completo con custom hook `useAuth()`, separación de contextos y el problema del interceptor fuera del árbol de React: [[Context API (useContext)]]

---

## Bloque 02 · Storage

### [[Storage en React Native#¿Cómo elegir la herramienta correcta?|¿Qué pasa si se cierra la app?]]

El contexto vive **solo en memoria RAM**. Al cerrar o matar la app, todo el estado se pierde.

| | Sin persistencia | Con persistencia (Storage) |
|---|---|---|
| 1 | Usuario hace login | Login → token guardado en disco |
| 2 | Cierra la app | Cierra la app |
| 3 | Reabre → debe loguearse de nuevo | Reabre → token leído → auto-login |
| 4 | ❌ Mala experiencia de usuario | ✅ Experiencia fluida |

### Storage en React Native — AsyncStorage vs SecureStore

| | AsyncStorage | SecureStore |
|---|---|---|
| Encriptado | ❌ No | ✅ Sí (Keychain / Keystore) |
| Tamaño máximo | Sin límite definido | 2 KB por valor |
| Ideal para | Preferencias, caché | Tokens JWT, credenciales |
| Instalación | `@react-native-async-storage/async-storage` | `expo-secure-store` |
| Síncrono | ❌ Async | ❌ Async |

> 🔐 Regla de oro: si es sensible (tokens, contraseñas, datos de sesión) → siempre SecureStore.

```javascript
// AsyncStorage
import AsyncStorage from '@react-native-async-storage/async-storage';
await AsyncStorage.setItem('theme', 'dark');
const theme = await AsyncStorage.getItem('theme');

// SecureStore — encriptado en Keychain / Keystore
import * as SecureStore from 'expo-secure-store';
await SecureStore.setItemAsync('token', 'eyJhbGciOiJIUzI1...');
const token = await SecureStore.getItemAsync('token');
```

### Integrando Storage + Context

```javascript
// context/AuthContext.js — con persistencia
useEffect(() => {
    const restore = async () => {
        const token = await SecureStore.getItemAsync('token');
        if (token) setUser({ token });
        setLoading(false);
    };
    restore();
}, []);

const login = async (token, userData) => {
    await SecureStore.setItemAsync('token', token);
    setUser(userData);
};
```

### ¿Y si necesito más? — WatermelonDB

`AsyncStorage` y `SecureStore` son clave-valor. Para datos **relacionales, con queries y reactividad**, se usa WatermelonDB.

| Concepto | Room (Android Nativo) | WatermelonDB (React Native) |
|---|---|---|
| Definir estructura | `@Entity` | `tableSchema()` |
| Modelo de datos | `@Dao` + clase Java/Kotlin | `class extends Model` |
| Reactividad | `LiveData` / `Flow` | `observe()` |
| Lenguaje | Java / Kotlin | JavaScript puro ✅ |
| Migración de schema | Migration classes | `migrations` array |

> 💡 WatermelonDB es lazy — solo carga en memoria los registros que necesitás.

### Manejo de token expirado — Interceptor Axios

```
api.get('/perfil') → Adjunta Bearer token → Servidor responde 401
      → Interceptor lo captura → deleteItemAsync('token') → logout() → Login
```

> El interceptor vive **fuera del árbol de React** — no puede usar `useContext` directo, hay que inyectar `logout` como parámetro. Desarrollo completo: [[Context API (useContext)#El interceptor vive fuera del árbol de React]]

### Errores comunes

| ❌ No hacer | ✅ Hacer |
|---|---|
| AsyncStorage para tokens | Siempre SecureStore para datos sensibles |
| Un solo contexto global para todo | Separar en contextos específicos: AuthContext, ThemeContext, etc. |
| No verificar expiración del token | Decodificar el JWT y validar `exp` antes de setear el user |

> Desarrollo completo de AsyncStorage, SecureStore y WatermelonDB paso a paso: [[Storage en React Native]]

---

## Bloque 03 · Biometría

### [[Biometría en React Native#Instalación y tipos soportados|expo-local-authentication]]

```bash
npx expo install expo-local-authentication
```

| Tipo | Constante |
|---|---|
| Huella digital | `FINGERPRINT` |
| Reconocimiento facial | `FACIAL_RECOGNITION` |
| Escaneo de iris | `IRIS` |

### Verificando disponibilidad biométrica

```javascript
import * as LocalAuthentication from 'expo-local-authentication';

async function checkBiometrics() {
    const hasHardware = await LocalAuthentication.hasHardwareAsync();
    const isEnrolled = await LocalAuthentication.isEnrolledAsync();
    const types = await LocalAuthentication.supportedAuthenticationTypesAsync();
    return { hasHardware, isEnrolled, types };
}
```

> ⚠️ Siempre verificar los tres antes de lanzar el prompt para evitar errores en runtime.

### Lanzando el prompt biométrico

```javascript
async function handleBiometricLogin() {
    const { hasHardware, isEnrolled } = await checkBiometrics();

    if (!hasHardware || !isEnrolled) {
        Alert.alert('No disponible', 'Biometría no configurada en el dispositivo');
        return;
    }

    const result = await LocalAuthentication.authenticateAsync({
        promptMessage: 'Autenticá tu identidad',
        fallbackLabel: 'Usar contraseña',
        cancelLabel: 'Cancelar',
        disableDeviceFallback: false,
    });

    if (result.success) {
        const token = await SecureStore.getItemAsync('token');
        login(token);
    } else {
        Alert.alert('Error', result.error);
    }
}
```

| Resultado | Significado |
|---|---|
| `success: true` | Autenticación exitosa |
| `user_cancel` | Usuario canceló el prompt |
| `lockout` | Demasiados intentos fallidos |
| `not_enrolled` | Sin credenciales registradas |
| `system_cancel` | El SO interrumpió la auth |

> Comparación completa con `androidx.biometric` de Android nativo (3 callbacks vs 1 promesa): [[Biometría en React Native#Comparación con Android nativo (AndroidX Biometric)]]. Desarrollo completo con la integración Context + SecureStore + biometría: [[Biometría en React Native]]

---

## Relacionado

- [[Clase 08 - React Native vs React DOM, APIs REST y React Navigation]]
- [[Context API (useContext)]]
- [[Storage en React Native]]
- [[Biometría en React Native]]
- [[React Hooks]]
- [[Axios y fetch API]]
- [[JWT]]
- [[Autenticación Biométrica]]
- [[EncryptedSharedPreferences]]
- [[Room]]
