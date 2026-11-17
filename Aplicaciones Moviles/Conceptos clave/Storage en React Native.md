---
tags: [react-native, storage, asyncstorage, securestore, watermelondb, expo, desarrollo-aplicaciones-1]
aliases: [AsyncStorage, SecureStore, WatermelonDB, Storage React Native]
---

# CC_Storage en React Native

> Tres niveles de persistencia en React Native, según qué tan sensibles y relacionales son los datos: **AsyncStorage** (clave-valor simple), **SecureStore** (clave-valor encriptado) y **WatermelonDB** (base de datos relacional).

## ¿Cómo elegir la herramienta correcta?

| | AsyncStorage | SecureStore |
|---|---|---|
| **Encriptado** | ❌ No | ✅ Sí (Keychain / Keystore) |
| **Tamaño máximo** | Sin límite definido | 2 KB por valor |
| **Ideal para** | Preferencias, caché | Tokens JWT, credenciales |
| **Instalación** | `@react-native-async-storage/async-storage` | `expo-secure-store` |
| **Síncrono** | ❌ Async | ❌ Async |

> 🔐 **Regla de oro:** si es sensible (tokens, contraseñas, datos de sesión) → **siempre SecureStore**.

---

## AsyncStorage — uso básico

```javascript
import AsyncStorage from '@react-native-async-storage/async-storage';

// Guardar
await AsyncStorage.setItem('theme', 'dark');
await AsyncStorage.setItem('user', JSON.stringify({ id: 1, nombre: 'Ana' }));

// Leer
const theme = await AsyncStorage.getItem('theme');
const raw = await AsyncStorage.getItem('user');
const user = raw ? JSON.parse(raw) : null;

// Eliminar
await AsyncStorage.removeItem('theme');

// Limpiar todo
await AsyncStorage.clear();
```

```bash
npx expo install @react-native-async-storage/async-storage
```

| Idea clave | Detalle |
|---|---|
| **Siempre async/await** | Todas las operaciones son asíncronas, nunca bloqueantes |
| **Solo strings** | Para objetos, usar `JSON.stringify()` al guardar y `JSON.parse()` al leer |

---

## SecureStore — uso básico

```javascript
import * as SecureStore from 'expo-secure-store';

// Guardar (encriptado en Keychain / Keystore)
await SecureStore.setItemAsync('token', 'eyJhbGciOiJIUzI1...');
await SecureStore.setItemAsync('user', JSON.stringify(userData));

// Leer
const token = await SecureStore.getItemAsync('token');
const raw = await SecureStore.getItemAsync('user');
const user = raw ? JSON.parse(raw) : null;

// Eliminar
await SecureStore.deleteItemAsync('token');
```

```bash
npx expo install expo-secure-store
```

### ¿Cómo funciona por debajo?

| Plataforma | Mecanismo |
|---|---|
| **Android** | Android Keystore System (cifrado a nivel hardware) |
| **iOS** | Keychain Services (cifrado con Face ID / Touch ID) |

> ⚠️ **Límite: 2 KB por clave.** Para datos grandes, guardar el token acá y el resto en AsyncStorage.

> Esto es el equivalente directo en React Native de [[EncryptedSharedPreferences]] en Android nativo — mismo propósito (clave-valor encriptado para datos sensibles), distinta implementación por plataforma.

---

## Integrando Storage con el contexto de sesión

```javascript
// context/AuthContext.js — con persistencia
const login = async (token, userData) => {
    await SecureStore.setItemAsync('token', token);
    setUser(userData);
};

const logout = async () => {
    await SecureStore.deleteItemAsync('token');
    setUser(null);
};
```

> Desarrollo completo del patrón, incluyendo la restauración de sesión al abrir la app: [[Context API (useContext)#Integrando Storage + Context]]

---

## ¿Y si necesito más? — WatermelonDB

`AsyncStorage` y `SecureStore` son clave-valor. Cuando se necesitan **datos relacionales, queries y reactividad**, se usa una base de datos embebida: **WatermelonDB**.

### Comparación con Room (Android nativo)

| Concepto | Room (Android Nativo) | WatermelonDB (React Native) |
|---|---|---|
| Definir estructura | `@Entity` | `tableSchema()` |
| Modelo de datos | `@Dao` + clase Java/Kotlin | `class extends Model` |
| Reactividad | `LiveData` / `Flow` | `observe()` |
| Motor | SQLite (abstraído) | SQLite (abstraído) |
| Lenguaje | Java / Kotlin | **JavaScript puro** ✅ |
| Migración de schema | Migration classes | `migrations` array |

> 💡 WatermelonDB es **lazy** — solo carga en memoria los registros que necesitás, lo que lo hace muy performante en mobile. Ver [[Room]] para el equivalente conceptual en Android.

### Paso a paso — JS puro

**1. Instalación**

```bash
npx expo install @nozbe/watermelondb @nozbe/with-observables expo-sqlite
```

**2. Schema (estructura de la tabla)**

```javascript
import { tableSchema, appSchema } from '@nozbe/watermelondb';

export const schema = appSchema({
    version: 1,
    tables: [
        tableSchema({
            name: 'posts',
            columns: [
                { name: 'title', type: 'string' },
                { name: 'body', type: 'string' },
                { name: 'user_id', type: 'string' },
            ]
        })
    ]
});
```

**3. Modelo (clase JS, sin decoradores)**

```javascript
import { Model } from '@nozbe/watermelondb';

export class Post extends Model {
    static table = 'posts';
    // Los campos se leen desde el schema
    // No necesitás declararlos en la clase
    // Accedés con: post.title, post.body, post.user_id
}
```

**4. Leer y escribir**

```javascript
// Leer todos los posts
const posts = await database.get('posts').query().fetch();

// Filtrar por campo
const mine = await database.get('posts').query(Q.where('user_id', userId)).fetch();

// Crear (siempre dentro de database.write)
await database.write(async () => {
    await database.get('posts').create(post => {
        post.title = 'Hola mundo';
        post.body = 'Contenido del post';
        post.user_id = '123';
    });
});
```

### Schema multi-tabla + migraciones

```javascript
// schema.js
export const schema = appSchema({
    version: 3, // sube con cada cambio
    tables: [
        tableSchema({ name: 'users', columns: [
            { name: 'name', type: 'string' },
            { name: 'email', type: 'string' },
        ]}),
        tableSchema({ name: 'posts', columns: [
            { name: 'title', type: 'string' },
            { name: 'user_id', type: 'string' },
            { name: 'likes', type: 'number' }, // v2
        ]}),
    ]
});

// migrations.js
export const migrations = schemaMigrations({
    migrations: [
        {
            toVersion: 2,
            steps: [ addColumns({
                table: 'posts',
                columns: [{ name: 'likes', type: 'number' }]
            })]
        },
        {
            toVersion: 3,
            steps: [ createTable({
                name: 'comments',
                columns: [{ name: 'body', type: 'string' }]
            })]
        },
    ]
});
```

### Database — singleton (se crea una sola vez)

```javascript
// database.js
import { Database } from '@nozbe/watermelondb';
import SQLiteAdapter from '@nozbe/watermelondb/adapters/sqlite';
import { schema } from './schema';
import { migrations } from './migrations';
import { Post } from './models/Post';
import { User } from './models/User';

const adapter = new SQLiteAdapter({ schema, migrations });

export const database = new Database({
    adapter,
    modelClasses: [Post, User],
});
```

```javascript
// Toda escritura va dentro de write()
// Si algo falla → rollback automático
// Agrupa ops en 1 sola transacción SQLite
await database.write(async () => {
    const user = await database.get('users').create(u => {
        u.name = 'Ana';
        u.email = 'ana@mail.com';
    });
    await database.get('posts').create(p => {
        p.title = 'Mi primer post';
        p.user_id = user.id;
    });
    // Ambas ops: 1 sola transacción ✅
});
```

---

## Manejo de token expirado — interceptor + storage

El flujo completo cuando el servidor rechaza el token:

```
api.get('/perfil')
      ↓
Adjunta Bearer token
      ↓
Servidor responde 401
      ↓
Interceptor lo captura
      ↓
deleteItemAsync('token')
      ↓
logout() → Login
```

```javascript
// services/api.js
import axios from 'axios';
export const api = axios.create({ baseURL: 'https://mi-api.com' });

// REQUEST: adjunta el token en cada llamada
api.interceptors.request.use(async (config) => {
    const token = await SecureStore.getItemAsync('token');
    if (token) config.headers.Authorization = `Bearer ${token}`;
    return config;
});

// RESPONSE: detecta 401 y limpia la sesión
api.interceptors.response.use(
    (response) => response,
    async (error) => {
        if (error.response?.status === 401) {
            await SecureStore.deleteItemAsync('token');
            logoutFn(); // inyectado — ver Context API
        }
        return Promise.reject(error);
    }
);
```

> El detalle de por qué `logoutFn` se **inyecta** en vez de usar `useContext` directamente acá: [[Context API (useContext)#El interceptor vive fuera del árbol de React]]

---

## Errores comunes

| ❌ Qué NO hacer | Por qué | ✅ Qué hacer |
|---|---|---|
| Guardar JWT o passwords en AsyncStorage | No está encriptado — cualquier app con acceso root puede leerlo | Siempre SecureStore para datos sensibles |
| No verificar expiración del token | Restaurar el token sin chequear `exp` → llamadas 401 en runtime | Decodificar el JWT y validar `exp` antes de setear el user en el contexto — ver [[JWT]] |
| Guardar objetos sin `JSON.stringify` | AsyncStorage/SecureStore solo aceptan strings | Siempre serializar/deserializar con JSON |

---

## Relacionado

- [[Clase 09 - Gestión de Sesión en React Native]]
- [[Context API (useContext)]]
- [[Biometría en React Native]]
- [[Axios y fetch API]]
- [[JWT]]
- [[EncryptedSharedPreferences]]
- [[Room]]
- [[DataStore]]
