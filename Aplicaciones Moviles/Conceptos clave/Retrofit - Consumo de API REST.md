---
tags: [android, retrofit, api-rest, gson, threads, desarrollo-aplicaciones-1]
aliases: [Retrofit, API REST Android, Consumo de API, enqueue, Gson]
---

# CC_Retrofit — Consumo de API REST

> Retrofit convierte una **interfaz Java** con anotaciones en llamadas HTTP reales. Es la forma estándar de consumir una API REST en Android nativo.

## Piezas necesarias

```
data/
├── model/        ← clases que "espejan" el JSON de la respuesta
└── network/
    ├── ApiService.java       ← interfaz con los endpoints (@GET, @POST...)
    └── RetrofitClient.java   ← instancia única (singleton) de Retrofit
```

---

## Paso 1 — Dependencias

```toml
[versions]
retrofit = "2.9.0"

[libraries]
retrofit      = { group = "com.squareup.retrofit2", name = "retrofit",       version.ref = "retrofit" }
retrofit-gson = { group = "com.squareup.retrofit2", name = "converter-gson", version.ref = "retrofit" }
```

```kotlin
// app/build.gradle.kts
implementation(libs.retrofit)
implementation(libs.retrofit.gson)
```

Y el permiso en el [[AndroidManifest]]:

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

> Cómo agregar dependencias en general: [[Gradle]]

---

## Paso 2 — RetrofitClient (Singleton manual)

Alcanza con una clase que crea la instancia **una sola vez** y la reutiliza — es la forma más simple de empezar.

```java
public class RetrofitClient {

    private static final String BASE_URL = "https://api.tudominio.com/";
    private static Retrofit instance;

    public static Retrofit getInstance() {
        if (instance == null) {
            instance = new Retrofit.Builder()
                    .baseUrl(BASE_URL)
                    .addConverterFactory(GsonConverterFactory.create())
                    .build();
        }
        return instance;
    }
}
```

> **¿Por qué singleton?** Retrofit y OkHttp son objetos "pesados" (thread pools, caché de conexiones). Crear uno por request sería un desperdicio.

> ⚠️ **Este singleton manual se queda corto cuando la app crece**: agregar un header de `Authorization` a todos los requests, testear Fragments que lo usan, o compartir la instancia sin `static` se vuelve difícil. La solución es migrar a **[[Hilt]]**, que reemplaza esta clase entera por un `@Module` con `@Provides`. Ver [[Inyección de Dependencias (DI)]] para el por qué, y [[Hilt]] para el paso a paso de la migración.

---

## Paso 3 — La interfaz de endpoints

```java
public interface ApiService {

    @GET("productos")
    Call<ProductosResponse> getProductos(@Query("limit") int limit);
    // → GET https://api.tudominio.com/productos?limit=20

    @GET("productos/{id}")
    Call<Producto> getProducto(@Path("id") String id);
    // → GET https://api.tudominio.com/productos/123

    @POST("productos")
    Call<Producto> crearProducto(@Body Producto nuevo);
}
```

| Anotación | Qué hace |
|---|---|
| `@GET("path")` | Define el endpoint, relativo a la `baseUrl` |
| `@POST` / `@PUT` / `@DELETE` | Otros métodos HTTP |
| `@Query("x")` | Query param: `?x=valor` |
| `@Path("x")` | Reemplaza `{x}` en la URL |
| `@Body` | Envía un objeto como JSON en el body |
| `Call<T>` | Representa la llamada. `T` = tipo que Gson va a parsear |

---

## Paso 4 — enqueue(): por qué no bloquea la app

Android corre todo en un único hilo por defecto: el **Main Thread** (o UI Thread), responsable de dibujar la pantalla y responder al usuario.

Si hacés la llamada de red **en ese hilo**, queda bloqueado esperando la respuesta → la app se congela → Android tira:

```
NetworkOnMainThreadException
```

`enqueue()` resuelve esto ejecutando la llamada en un **hilo aparte**:

```
Main Thread                         Hilo de red (OkHttp)
     │  call.enqueue(callback)              │
     │─────────────────────────────────────►│ hace la request
     │  (la UI sigue respondiendo)          │ espera respuesta...
     │                                       │ parsea el JSON
     │◄─────────────────────────────────────│ vuelve y llama onResponse()
     │  actualiza la UI                     │
```

```java
ApiService apiService = RetrofitClient.getInstance().create(ApiService.class);
Call<ProductosResponse> call = apiService.getProductos(20);

call.enqueue(new Callback<ProductosResponse>() {

    @Override
    public void onResponse(Call<ProductosResponse> call, Response<ProductosResponse> response) {
        // Corre en el Main Thread — podés tocar la UI acá
        if (response.isSuccessful()) {
            List<Producto> lista = response.body().getResultados();
        } else {
            // response.code() → 404, 500, etc.
        }
    }

    @Override
    public void onFailure(Call<ProductosResponse> call, Throwable t) {
        // Error de red: sin internet, timeout, etc. También en Main Thread
    }
});
```

> ⚠️ **Regla de oro:** siempre `enqueue()`, nunca `execute()`. `execute()` es sincrónico, bloquea el Main Thread y tira `NetworkOnMainThreadException`.

---

## Paso 5 — Modelos: cómo Gson parsea el JSON

Cada clase Java "espeja" la forma del JSON. `@SerializedName` mapea el campo:

```json
{
  "count": 1350,
  "results": [
    { "name": "bulbasaur", "url": "https://.../1/" }
  ]
}
```

```java
public class ProductosResponse {
    @SerializedName("count")
    private int count;

    @SerializedName("results")
    private List<Producto> results;
    // getters...
}

public class Producto {
    @SerializedName("name")
    private String name;

    @SerializedName("url")
    private String url;
    // getters...
}
```

---

## Glide — cargar imágenes desde una URL

Retrofit trae el JSON, pero **no** carga imágenes. Para eso se usa **Glide**:

```java
Glide.with(context)
     .load(producto.getImagenUrl())
     .into(ivProducto);
```

| | **Retrofit** | **Glide** |
|---|---|---|
| ¿Qué descarga? | JSON de una API | Imágenes desde una URL |
| ¿En qué hilo? | Hilo separado (`enqueue`) | Hilo separado (automático) |
| Caché | No por defecto | Sí, automático |

---

## Errores típicos

| Síntoma | Causa |
|---|---|
| `NetworkOnMainThreadException` | Usaste `execute()` en vez de `enqueue()` |
| `SecurityException` | Falta `<uses-permission INTERNET>` en el Manifest |
| Campos en `null` | El `@SerializedName` no coincide con la key del JSON |
| App se traba al pedir datos | Estás bloqueando el Main Thread |
| `response.body()` es null | Chequeá `response.isSuccessful()` antes de leerlo |

---

## Relacionado

- [[Clase 04 - Inyección de Dependencias y Storage]]
- [[Hilt]]
- [[Inyección de Dependencias (DI)]]
- [[RecyclerView]]
- [[Fragment]]
- [[Navigation Component]]
- [[Gradle]]
- [[AndroidManifest]]
