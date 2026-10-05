---
tags: [android, jwt, autenticacion, seguridad, criptografia, desarrollo-aplicaciones-1]
aliases: [JWT, JSON Web Token, Bearer Token, HMAC-SHA256]
---

# CC_JWT (JSON Web Token)

> Token que trae los datos del usuario **incrustados y firmados**. El servidor no guarda nada — el estado de la sesión vive en el cliente.

## Las 3 estrategias para manejar estado en HTTP

HTTP es **stateless**: cada request llega sin memoria del anterior. Sin nada, el usuario debería loguearse en cada pantalla.

| Estrategia | Cómo funciona | Dónde vive el estado |
|---|---|---|
| **Sesión + Cookie** | El servidor genera un `sessionId` y lo guarda en memoria, DB o Redis | En el servidor |
| **JWT** | El propio token trae los datos y va firmado | En el cliente |
| **OAuth / SSO** | Un proveedor externo (Google, Auth0) autentica y entrega un token | En el proveedor externo |

---

## Estructura de un JWT

Un JWT tiene **3 partes** separadas por un punto (`.`):

```
eyJhbGciOiJIUzI1NiJ9.eyJ1c2VySWQiOjEsImVtYWlsIjoiLi4uIn0.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
└──────── HEADER ────────┘└──────────── PAYLOAD ────────────┘└─────────── SIGNATURE ───────────┘
```

| Parte | Contenido | Ejemplo |
|---|---|---|
| **HEADER** | Algoritmo de firma y tipo de token | `{ "alg": "HS256", "typ": "JWT" }` |
| **PAYLOAD** | Datos del usuario (claims). Van en **Base64**, **no encriptados** | `{ "userId": 1, "email": "...", "exp": 1720000000 }` |
| **SIGNATURE** | Garantiza que el token no fue alterado | `HMACSHA256(base64(header) + "." + base64(payload), secretKey)` |

> ⚠️ **Nunca guardes contraseñas en el payload.** Cualquiera puede decodificar Base64 y leerlo.

---

## Encodear vs Encriptar vs Firmar — la distinción clave

| Operación | Qué hace | ¿Es reversible sin clave? | ¿La usa JWT? |
|---|---|---|---|
| **Encodear** (Base64) | Cambia el formato a texto | Sí, cualquiera lo decodifica — **no es seguridad** | Sí, para header y payload |
| **Encriptar** (AES, RSA...) | Oculta el contenido | Sí, pero solo con la clave | **No**, JWT no lo usa por defecto |
| **Firmar** (HMAC-SHA256) | No oculta nada, pero detecta cualquier cambio | No es reversible | **Sí**, para la signature |

> **Por eso un JWT no es secreto — es a prueba de manipulación.** Cualquiera puede leer el contenido (Base64), pero nadie puede modificarlo sin que la firma deje de coincidir.

---

## ¿Cómo garantiza JWT la integridad del mensaje?

**Integridad no es ocultar el contenido: es detectar si alguien lo modificó.**

| Paso | Qué pasa |
|---|---|
| 1. El servidor calcula una firma al **emitir** | `firma = HMACSHA256(contenido, claveSecreta)` — solo el servidor conoce la clave |
| 2. Un carácter distinto rompe todo el hash | `HMACSHA256("userId":1)` → `4f8a9c2e1b7d3f60...` <br> `HMACSHA256("userId":2)` → `91e2b8f0a3c7d215...` (totalmente distinto) |
| 3. El servidor recalcula y compara al **recibir** | `if (firmaEsperada != firmaRecibida) → 401 Unauthorized` |
| 4. Sin la clave, nadie puede forjarla | El algoritmo (HMAC-SHA256) es público, pero la `claveSecreta` **nunca viaja** — sin ella no se puede calcular una firma válida |

> La integridad no oculta el contenido — solo garantiza que **nadie lo tocó sin que se note**.

---

## Cómo viaja el token — flujo completo

| # | Paso | Detalle |
|---|---|---|
| 1 | **Login exitoso** | El servidor devuelve el JWT en el response |
| 2 | **Persistencia** | Se guarda el token (ej: en [[EncryptedSharedPreferences]]) |
| 3 | **Cualquier request HTTP** | El interceptor lee el token con `tokenManager.getToken()` |
| 4 | **Header** | `header("Authorization", "Bearer " + token)` — OkHttp lo agrega automáticamente antes de enviar |
| 5 | **Validación** | El servidor valida el token. Si es válido responde con datos, si no devuelve `401` |

```
Cliente                                    Servidor
   │  login(user, pass)                       │
   │──────────────────────────────────────────►│
   │                                            │ genera JWT firmado
   │◄──────────────────────────────────────────│
   │  { token: "eyJ..." }                      │
   │                                            │
   │  [guarda el token localmente]             │
   │                                            │
   │  GET /productos                           │
   │  Authorization: Bearer eyJ...             │
   │──────────────────────────────────────────►│
   │                                            │ recalcula firma y compara
   │◄──────────────────────────────────────────│
   │  200 OK (datos) o 401 Unauthorized        │
```

> **Sin DI, cablear todo esto sería un espagueti. Con [[Hilt]], cada pieza se conecta declarativamente** mediante un interceptor de OkHttp. Ver [[Hilt#Interceptor de OkHttp — agregar el token automáticamente|el interceptor implementado]].

---

## Formato del header

```
Authorization: Bearer <token>
```

El prefijo `Bearer` (portador) indica el **esquema de autenticación**: quien tenga el token, lo porta, sin importar quién es.

---

## Buenas prácticas

- Nunca metas datos sensibles (contraseñas, tarjetas) en el payload — es legible por cualquiera
- Verificá siempre el campo `exp` (expiración) — un JWT vencido debe rechazarse
- Guardá el token en un lugar seguro del dispositivo, nunca en texto plano si es posible: ver [[EncryptedSharedPreferences]]
- La `claveSecreta` del servidor nunca debe estar en el cliente, ni en el repo de código

---

## Relacionado

- [[Clase 05 - JWT y LocalStorage]]
- [[Hilt]]
- [[EncryptedSharedPreferences]]
- [[Retrofit - Consumo de API REST]]
- [[Inyección de Dependencias (DI)]]
