---
tags: [android, jwt, autenticacion, hilt, interceptor, desarrollo-aplicaciones-1]
aliases: [Clase JWT, Auth JWT]
---

# Clase 05 — JWT + LocalStorage

**Materia:** Desarrollo de Aplicaciones 1 — 2C 2026

> Objetivo de la clase: **persistir un token y enviarlo automáticamente en cada request**.

---

## ¿Cómo se maneja el estado en HTTP?

HTTP es un protocolo **stateless**: cada request llega sin memoria del anterior. Si no se hace nada, el usuario tendría que loguearse en cada pantalla.

| Estrategia | Cómo funciona |
|---|---|
| **Sesión + Cookie** | El servidor genera un `sessionId` y lo guarda en memoria, DB o Redis. El cliente solo manda el ID |
| **Token ([[JWT]])** | El propio token trae los datos del usuario y va firmado. El servidor **no guarda nada** — el estado vive en el cliente |
| **OAuth / SSO** | Un proveedor externo (Google, Auth0...) autentica una vez y entrega un token que varias apps aceptan |

> En esta materia se profundiza en **JWT**.

---

## [[JWT|Estructura de un JWT]]

Desarrollo completo de las 3 partes, el algoritmo de firma y por qué **no es lo mismo firmar que encriptar**: ver [[JWT]].

---

## ¿Cómo viaja el token? (flujo completo)

| # | Paso | Detalle |
|---|---|---|
| 1 | Login exitoso | El servidor devuelve el JWT en el response |
| 2 | Persistencia | Se guarda el token en alguna persistencia (ej: [[EncryptedSharedPreferences]]) |
| 3 | Cualquier request HTTP | El interceptor lee el token con `tokenManager.getToken()` |
| 4 | Header | `header("Authorization", "Bearer " + token)` — OkHttp lo agrega automáticamente antes de enviar |
| 5 | Validación | El servidor valida el token. Si es válido, responde con los datos. Si no, devuelve `401` |

> **Sin DI, cablear todo esto sería un espagueti. Con [[Hilt]], cada pieza se conecta declarativamente.**

---

## [[Hilt#Interceptor de OkHttp — agregar el token automáticamente|Interceptor — agregar el token automáticamente]]

Se modifica el `NetworkModule` para que Retrofit use un `OkHttpClient` con un interceptor que lee el token del `TokenManager`.

```java
@Provides
@Singleton
public OkHttpClient provideOkHttp(TokenManager tokenManager) {
    return new OkHttpClient.Builder()
            .addInterceptor(chain -> {
                Request original = chain.request();
                String token = tokenManager.getToken();

                if (token != null) {
                    original = original.newBuilder()
                            .header("Authorization", "Bearer " + token)
                            .build();
                }
                return chain.proceed(original);
            }).build();
}
```

> `provideRetrofit` ahora recibe el `OkHttpClient`:
> `new Retrofit.Builder().client(okHttpClient).baseUrl(...).build()`

Desarrollo completo con `TokenManager`, `SharedPreferences` y el flujo entero de Hilt: ver [[Hilt]].

---

## Hands-On #2 — Persistir y usar el token

1. Crear la clase `TokenManager` con `@Inject` y `SharedPreferences`
2. Agregar `provideOkHttp()` con el interceptor en `NetworkModule`
3. Modificar `provideRetrofit()` para que reciba el `OkHttpClient`
4. Simular un login guardando un token hardcodeado con `saveToken()`
5. Agregar un `Log` en el interceptor para verificar que el header se agrega
6. Correr la app, ver el Logcat y confirmar que el token viaja

---

## Relacionado

- [[Clase 04 - Inyección de Dependencias y Storage]]
- [[JWT]]
- [[Hilt]]
- [[EncryptedSharedPreferences]]
- [[Retrofit - Consumo de API REST]]
- [[Inyección de Dependencias (DI)]]
