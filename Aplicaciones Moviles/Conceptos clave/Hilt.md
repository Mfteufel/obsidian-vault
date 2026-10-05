---
tags: [android, hilt, dagger, dependency-injection, jetpack, jwt, interceptor, desarrollo-aplicaciones-1]
aliases: [Hilt, HiltAndroidApp, AndroidEntryPoint, Module, Provides, NetworkModule, OkHttp Interceptor]
---

# CC_Hilt

> Framework de Google, construido sobre **Dagger**, que simplifica la [[Inyección de Dependencias (DI)|Inyección de Dependencias]] en Android. Reemplaza los singletons manuales como `RetrofitClient.getInstance()`.

## Las 3 piezas conceptuales

| Pieza | Qué hace |
|---|---|
| **Contenedor** | Crea y guarda objetos. Vos **no hacés `new`** |
| **Scopes** | Definen cuánto tiempo vive una instancia dentro del contenedor (`@Singleton`, `@ActivityScoped`) |
| **Ciclo de vida** | Hilt inyecta en el momento correcto (después del `onCreate`, antes de que el Fragment la use). Sin esto podrías inyectar en un objeto no listo o ya destruido |

---

## Anotaciones clave

| Anotación | Dónde va | Para qué |
|---|---|---|
| `@HiltAndroidApp` | Clase `Application` | Arranca el contenedor de Hilt (**una sola vez**) |
| `@AndroidEntryPoint` | Activity / Fragment | Habilita la inyección ahí. Sin esto, `@Inject` falla |
| `@Module` | Una clase | Contiene métodos que crean objetos |
| `@InstallIn` | Junto a `@Module` | Define en qué scope vive (`SingletonComponent` = toda la app) |
| `@Provides` | Un método dentro del `@Module` | Ese método devuelve una dependencia |
| `@Singleton` | Junto a `@Provides` | Una sola instancia, siempre la misma |
| `@Inject` | Un campo en Activity/Fragment | Pide una dependencia |

---

## Setup paso a paso — migrar Retrofit a Hilt

### Paso 1 — Configurar Gradle

```gradle
// build.gradle (project)
plugins {
    id 'com.android.application'
    id 'com.google.dagger.hilt.android'
}

dependencies {
    implementation "com.google.dagger:hilt-android:<latest>"
    annotationProcessor "com.google.dagger:hilt-android-compiler:<latest>"
}
```

```gradle
// build.gradle (module: app)
dependencies {
    // Ya estaban (Retrofit + Gson + Glide)
    implementation(libs.retrofit)
    implementation(libs.retrofit.gson)
    implementation(libs.glide)

    // NUEVO: Hilt
    implementation(libs.hilt.android)
    annotationProcessor(libs.hilt.compiler)
}
```

> ⚠️ Después de agregar las dependencias, hacer **Sync Now** en Android Studio y esperar a que compile. Más sobre esto en [[Gradle]].

---

### Paso 2 — Application + @AndroidEntryPoint

```java
// MyApp.java
@HiltAndroidApp
public class MyApp extends Application {
    // Vacía — Hilt hace todo
}
```

```java
// MainActivity.java
@AndroidEntryPoint
public class MainActivity extends AppCompatActivity {
    // Sin cambios internos
}
```

```xml
<!-- AndroidManifest.xml -->
<application
    android:name=".MyApp"
    android:label="@string/app_name" ... >
```

| Anotación | Rol |
|---|---|
| `@HiltAndroidApp` | Arranca el contenedor de Hilt. Va en la `Application`, una sola vez |
| `@AndroidEntryPoint` | Habilita la inyección en esa Activity/Fragment. Sin esto, `@Inject` falla |

> No te olvides de registrar `MyApp` en el [[AndroidManifest]] con `android:name=".MyApp"`.

---

### Paso 3 — El NetworkModule

Reemplaza al `RetrofitClient` manual — ahora Hilt sabe cómo crear `Retrofit` y el `ApiService`.

```java
@Module
@InstallIn(SingletonComponent.class)
public class NetworkModule {

    @Provides
    @Singleton
    public Retrofit provideRetrofit() {
        return new Retrofit.Builder()
                .baseUrl("https://pokeapi.co/api/v2/")
                .addConverterFactory(GsonConverterFactory.create())
                .build();
    }

    @Provides
    @Singleton
    public PokemonApiService provideApiService(Retrofit retrofit) {
        return retrofit.create(PokemonApiService.class);
    }
}
```

| Anotación | Significa |
|---|---|
| `@Module` | Contiene métodos que crean objetos |
| `@InstallIn(SingletonComponent.class)` | Vive en el scope de **toda la app** |
| `@Provides` | Este método devuelve una dependencia |
| `@Singleton` | Una sola instancia, siempre la misma |

> Fijate que `provideApiService` **recibe** un `Retrofit` como parámetro — Hilt sabe resolverlo solo, porque ya está definido arriba en el mismo módulo.

---

### Paso 4 — Inyectar en el Fragment

```java
@AndroidEntryPoint
public class PokemonListFragment extends Fragment {

    @Inject
    PokemonApiService apiService;

    // Ya no hay RetrofitClient
    // ni .getInstance()
    // ni .create()

    // Usás apiService directamente:
    apiService.getPokemon(20).enqueue(...);
}
```

| Qué pasa | Detalle |
|---|---|
| **Se borra** `RetrofitClient.java` | Ya no se necesita — Hilt lo reemplaza |
| **Se mantiene** `PokemonApiService.java`, modelos (POJOs), Adapter + [[RecyclerView]] | La lógica de red **no cambia**, solo cambia **quién la arma** |

---

## Hands-on #1 — Migrar un proyecto existente a Hilt

1. Agregar dependencias de Hilt en Gradle y sincronizar
2. Crear `MyApp` con `@HiltAndroidApp` y registrarla en el Manifest
3. Anotar `MainActivity` con `@AndroidEntryPoint`
4. Crear `NetworkModule` con `@Provides` para Retrofit y `ApiService`
5. Reemplazar `RetrofitClient.getInstance()` por `@Inject` en los Fragments
6. Correr la app y verificar que todo sigue funcionando

---

## Interceptor de OkHttp — agregar el token automáticamente

Una vez migrado a Hilt, agregar el header `Authorization` a **todos** los requests (uno de los problemas originales del singleton manual) se resuelve con un **interceptor de OkHttp** inyectado por Hilt.

**La idea:** en vez de agregar el header a mano en cada llamada, se intercepta **cada request antes de salir** y se le agrega el token automáticamente — si existe.

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

`provideRetrofit()` ahora recibe el `OkHttpClient` en vez de construir Retrofit "pelado":

```java
@Provides
@Singleton
public Retrofit provideRetrofit(OkHttpClient okHttpClient) {
    return new Retrofit.Builder()
            .client(okHttpClient)
            .baseUrl("https://api.tudominio.com/")
            .addConverterFactory(GsonConverterFactory.create())
            .build();
}
```

> El interceptor recibe `TokenManager` como parámetro — Hilt lo resuelve solo, siempre y cuando `TokenManager` también esté anotado con `@Inject` en su constructor.

### TokenManager — quién guarda y expone el token

```java
public class TokenManager {

    private final SharedPreferences prefs;

    @Inject
    public TokenManager(@ApplicationContext Context context) {
        prefs = context.getSharedPreferences("auth_prefs", Context.MODE_PRIVATE);
    }

    public void saveToken(String token) {
        prefs.edit().putString("token", token).apply();
    }

    public String getToken() {
        return prefs.getString("token", null);
    }

    public void clearToken() {
        prefs.edit().remove("token").apply();
    }
}
```

> ⚠️ Para el TPO o cualquier proyecto real, conviene reemplazar `SharedPreferences` por [[EncryptedSharedPreferences]] dentro de `TokenManager` — la lógica de `saveToken`/`getToken` no cambia, solo cómo se crea la instancia de `prefs`.

### Flujo completo

```
Request sale del Fragment
        │
        ▼
apiService.getProductos() ──► OkHttpClient (con interceptor)
                                      │
                          ¿hay token guardado?
                              │           │
                            sí            no
                              │           │
                  agrega header      lo deja igual
                  Authorization
                              │           │
                              ▼           ▼
                          Request sale al servidor
```

> Ver [[JWT]] para entender qué es el token que viaja en ese header, y por qué se manda como `Bearer <token>`.

---

## Hands-on #2 — Persistir y usar el token (JWT)

1. Crear la clase `TokenManager` con `@Inject` y `SharedPreferences`
2. Agregar `provideOkHttp()` con el interceptor en `NetworkModule`
3. Modificar `provideRetrofit()` para que reciba el `OkHttpClient`
4. Simular un login guardando un token hardcodeado con `saveToken()`
5. Agregar un `Log` en el interceptor para verificar que el header se agrega
6. Correr la app, ver el Logcat y confirmar que el token viaja

---

## Errores típicos

| Síntoma | Causa |
|---|---|
| `@Inject` no inyecta nada (NullPointerException) | Falta `@AndroidEntryPoint` en la Activity/Fragment |
| "Cannot create an instance of..." | Falta `@HiltAndroidApp` en la `Application`, o no está registrada en el Manifest |
| "[Dagger/MissingBinding]" en compilación | Ningún `@Module` provee ese tipo — falta el `@Provides` correspondiente |
| Dos instancias distintas cuando esperabas una sola | Falta `@Singleton` en el `@Provides` |
| El header `Authorization` nunca llega al servidor | `provideRetrofit()` no está recibiendo el `OkHttpClient`, o el interceptor no está agregado al `Builder` |
| `401 Unauthorized` aunque el token parezca correcto | El token expiró (`exp` vencido), o el formato del header no es exactamente `"Bearer " + token` |

---

## Relacionado

- [[Clase 04 - Inyección de Dependencias y Storage]]
- [[Clase 05 - JWT y LocalStorage]]
- [[Inyección de Dependencias (DI)]]
- [[JWT]]
- [[EncryptedSharedPreferences]]
- [[Retrofit - Consumo de API REST]]
- [[Fragment]]
- [[Gradle]]
- [[AndroidManifest]]
