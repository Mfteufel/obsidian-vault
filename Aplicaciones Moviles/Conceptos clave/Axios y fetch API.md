---
tags: [react-native, http, api-rest, axios, fetch, interceptors, desarrollo-aplicaciones-1]
aliases: [fetch, fetch API, Axios, Interceptors, Peticiones HTTP React Native]
---

# CC_Axios y fetch API

> Dos formas de consumir una API REST desde React Native: **`fetch`**, nativa de JavaScript, y **Axios**, una librería con funcionalidades extra.

## fetch API — peticiones HTTP nativas

`fetch` es una API **nativa de JavaScript** (no hace falta instalar nada). Devuelve una **promesa** y está soportada en React Native sin configuración adicional.

### GET

```javascript
fetch('https://api.example.com/data')
    .then(response => {
        if (!response.ok) {
            throw new Error('Error en la petición');
        }
        return response.json();
    })
    .then(data => {
        console.log('Datos:', data);
    })
    .catch(error => {
        console.error('Error:', error);
    });
```

> ⚠️ A diferencia de Axios, `fetch` **no rechaza la promesa** en errores HTTP (404, 500). Hay que chequear `response.ok` manualmente — si no, un 404 "pasa" como éxito.

### POST

```javascript
fetch('https://api.example.com/submit', {
    method: 'POST',
    headers: {
        'Content-Type': 'application/json'
    },
    body: JSON.stringify({
        name: 'John', age: 30
    })
})
    .then(res => res.json())
    .then(data => console.log(data))
    .catch(err => console.error(err));
```

---

## Axios — librería HTTP con superpoderes

```bash
npm install axios
```

| Ventaja | Detalle |
|---|---|
| ✓ Async/Await nativo | Sintaxis más limpia que encadenar `.then()` |
| ✓ Manejo automático de JSON | `response.data` ya viene parseado — no hace falta `.json()` |
| ✓ Interceptors de request/response | Modificar peticiones o respuestas **antes** de que lleguen a destino |
| ✓ Interfaz más simple que fetch | Menos código repetido, errores HTTP sí rechazan la promesa |

### GET con Axios

```javascript
axios.get('https://api.example.com/data')
    .then(response => {
        // response.data ya es JSON, sin .json()
        console.log(response.data);
    })
    .catch(error => {
        console.error('Error:', error);
    });
```

### Comparación directa fetch vs Axios

| | `fetch` | `axios` |
|---|---|---|
| Instalación | Ninguna (nativo) | `npm install axios` |
| Parseo de JSON | Manual (`response.json()`) | Automático (`response.data`) |
| Errores HTTP (404, 500) | **No** rechazan la promesa — hay que chequear `response.ok` | **Sí** rechazan la promesa — van directo al `.catch()` |
| Interceptors | No tiene (hay que armarlo a mano) | Built-in |
| Cancelar requests | `AbortController` | `CancelToken` / `AbortController` |

---

## Interceptors — la pieza clave para JWT

Los interceptors modifican **automáticamente** cada request o response que pasa por Axios — ideal para agregar un token de autenticación sin repetir código en cada llamada.

```javascript
axios.interceptors.request.use(
    config => {
        config.headers.Authorization = 'Bearer ' + token;
        return config;
    },
    error => Promise.reject(error)
);
```

> Este es el **mismo problema y la misma solución conceptual** que el interceptor de OkHttp visto en Android nativo con Hilt — agregar el header `Authorization` a **todos** los requests sin tocar cada llamada individual. Ver [[Hilt#Interceptor de OkHttp — agregar el token automáticamente]] y [[JWT]].

### Interceptor típico para una app con login

```javascript
// services/api.js
import axios from 'axios';
import { getToken } from './tokenStorage';

const api = axios.create({
    baseURL: 'https://api.midominio.com',
});

api.interceptors.request.use(async config => {
    const token = await getToken();
    if (token) {
        config.headers.Authorization = `Bearer ${token}`;
    }
    return config;
});

export default api;
```

### Interceptor de response — manejar 401 globalmente

```javascript
api.interceptors.response.use(
    response => response,
    error => {
        if (error.response?.status === 401) {
            // token vencido o inválido → redirigir a login
            logout();
        }
        return Promise.reject(error);
    }
);
```

---

## Dónde vive esto en el scaffold

Según la estructura de carpetas recomendada para React Native, la comunicación con APIs va en `services/`:

```
my-app/
└── services/
    ├── api.js          ← instancia de Axios + interceptors
    ├── authService.js  ← login, registro, OTP
    └── productosService.js
```

> Ver [[React Native#Scaffold — estructura de carpetas recomendada]] para el resto de la estructura.

---

## Uso típico dentro de un hook

```javascript
// hooks/useFetch.js
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

> Este patrón combina [[React Hooks|useState y useEffect]] con `fetch`/Axios — la base de cómo se consumen datos en cualquier pantalla de React Native.

---

## Errores típicos

| Síntoma | Causa |
|---|---|
| Un 404 se procesa como si fuera éxito | Usando `fetch` sin chequear `response.ok` |
| `response.data is undefined` | Se mezcló sintaxis de `fetch` (`response.json()`) con Axios (`response.data`) |
| El token no se agrega a los requests | El interceptor está registrado en una instancia de Axios distinta a la que usa el resto de la app |
| Loop infinito de 401 | El interceptor de response intenta refrescar el token, pero el refresh también devuelve 401 sin cortar el ciclo |

---

## Relacionado

- [[Clase 08 - React Native vs React DOM, APIs REST y React Navigation]]
- [[React Native]]
- [[React Hooks]]
- [[JWT]]
- [[Hilt]]
- [[Retrofit - Consumo de API REST]]
