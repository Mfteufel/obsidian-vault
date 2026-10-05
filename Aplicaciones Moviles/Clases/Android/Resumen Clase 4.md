# Resumen Clase 4 — Inyección de Dependencias & Storage

**Materia:** Desarrollo de Aplicaciones 1 (Android nativo, Java) — UADE, 2C 2026
**Profesor:** Horacio Brizuela

> Este documento es un resumen completo de la Clase 4 para dar contexto a una IA. Cubre: por qué la Inyección de Dependencias, Hilt como framework de DI, migración paso a paso de un singleton manual a Hilt, y los 4 métodos de persistencia de datos en Android.

---

## 1. Contexto previo — el problema que origina la clase

En la clase anterior (Retrofit) se armó un **singleton manual** para proveer una instancia de `Retrofit`:

```java
public class RetrofitClient {
    private static Retrofit instance;

    public static Retrofit getInstance() {
        if (instance == null) {
            instance = new Retrofit.Builder()
                    .baseUrl("https://pokeapi.co/api/v2/")
                    .addConverterFactory(GsonConverterFactory.create())
                    .build();
        }
        return instance;
    }
}
```

Este patrón "funciona", pero se vuelve insuficiente cuando la app crece. Los tres problemas concretos que dispara la clase:

1. **Agregar un header de `Authorization` a TODOS los requests** — con el singleton manual hay que tocar la clase a mano, no hay un punto de configuración centralizado y flexible.
2. **Testear los Fragments (o cualquier clase) que usan el service de Retrofit** — el campo `static` no se puede reemplazar por un mock en un test, porque está fuertemente acoplado a la implementación concreta.
3. **Compartir la misma instancia entre Fragments sin usar `static`** — el patrón singleton manual obliga al uso de `static`, lo cual es rígido y dificulta el control del ciclo de vida.

**Conclusión de la clase:** el singleton manual se queda corto. Se necesita una forma mejor de **construir y distribuir objetos**.

---

## 2. ¿Qué es la Inyección de Dependencias (DI)?

### La cadena conceptual completa

```
SOLID (principio) → IoC (principio) → DI (técnica) → Hilt (herramienta)
```

| Nivel | Concepto | Definición / frase clave |
|---|---|---|
| Principio (la "D" de SOLID) | **Dependency Inversion** | "Los módulos de alto nivel no deben depender de módulos de bajo nivel. Ambos deben depender de abstracciones." Ejemplo aplicado: el Fragment (alto nivel) no debería conocer directamente `RetrofitClient` (bajo nivel). |
| Principio | **Inversión de Control (IoC)** | "No busques tus dependencias, ya te las doy yo." El Fragment no controla el cuándo ni el cómo se construyen sus dependencias — un agente externo (el framework) se las entrega. |
| Técnica | **Inyección de Dependencias (DI)** | Patrón de diseño donde las dependencias se pasan **desde afuera** (por constructor, por campo o por método) para generar desacoplamiento. |
| Herramienta concreta | **Hilt** | Automatiza la DI en Android con anotaciones como `@Inject` y `@Provides`. |

### Comparación antes/después en código

```java
// ANTES — sin DI. El Fragment SABE cómo crear todo.
Retrofit retrofit = RetrofitClient.getInstance();
PokemonApiService api = retrofit.create(PokemonApiService.class);
// El Fragment conoce la implementación concreta y la busca activamente.

// DESPUÉS — con DI. El Fragment solo DECLARA qué necesita.
@Inject
PokemonApiService api;
// Hilt se encarga del resto: crear Retrofit, configurarlo,
// inyectar el service listo para usar.
// El Fragment depende de una abstracción. IoC en acción.
// El Fragment no sabe cómo se construye Retrofit. Solo pide lo que necesita.
```

### Las 3 formas de inyectar una dependencia

| Forma | Ejemplo |
|---|---|
| Por constructor | `public LoginViewModel(ApiService api) { this.api = api; }` |
| Por campo | `@Inject PokemonApiService api;` (la más común en Android con Hilt, usada en Activities/Fragments) |
| Por método | `@Inject public void setApi(ApiService api) { ... }` |

---

## 3. Hilt — DI para Android

**Definición:** framework de Google construido sobre **Dagger** que simplifica la inyección de dependencias en Android.

### Los 3 conceptos estructurales de Hilt

| Concepto | Explicación |
|---|---|
| **Contenedor** | Crea y guarda objetos. El desarrollador no hace `new` manualmente. |
| **Scopes** | Definen cuánto tiempo vive una instancia dentro del contenedor. Ejemplos: `@Singleton` (vive toda la app), `@ActivityScoped` (vive lo que dura la Activity). |
| **Ciclo de vida** | Hilt sabe inyectar las dependencias en el momento correcto del ciclo de vida de Android (después de `onCreate`, antes de que el Fragment/Activity intente usarlas). Sin esta integración, se podría terminar inyectando en un objeto que todavía no está listo, o que ya fue destruido. |

### Anotaciones clave usadas en la clase

| Anotación | Dónde se usa | Función |
|---|---|---|
| `@HiltAndroidApp` | En la clase `Application` | Arranca el contenedor de Hilt para toda la app. Se usa **una sola vez**. |
| `@AndroidEntryPoint` | En una Activity o Fragment | Habilita la inyección de dependencias en ese componente. Sin esta anotación, `@Inject` falla en tiempo de ejecución. |
| `@Module` | En una clase que agrupa proveedores | Contiene los métodos que crean/proveen objetos. |
| `@InstallIn(SingletonComponent.class)` | Junto a `@Module` | Define en qué scope vive ese módulo (`SingletonComponent` = toda la app). |
| `@Provides` | En un método dentro de un `@Module` | Indica que ese método devuelve (provee) una dependencia. |
| `@Singleton` | Junto a `@Provides` | Garantiza una sola instancia de ese objeto, siempre la misma, mientras viva la app. |
| `@Inject` | En un campo de una Activity/Fragment anotada con `@AndroidEntryPoint` | Pide/recibe una dependencia que Hilt ya sabe construir. |

---

## 4. Migración paso a paso: de RetrofitClient manual a Hilt

### Paso 1 — Configurar Gradle

**build.gradle (nivel Project):**

```gradle
plugins {
    id 'com.android.application'
    id 'com.google.dagger.hilt.android'
}

dependencies {
    implementation "com.google.dagger:hilt-android:<latest>"
    annotationProcessor "com.google.dagger:hilt-android-compiler:<latest>"
}
```

**build.gradle (nivel Module: app) — usando Version Catalog:**

```gradle
dependencies {
    // Ya estaban de la clase pasada (Retrofit)
    implementation(libs.retrofit)
    implementation(libs.retrofit.gson)
    implementation(libs.glide)

    // NUEVO: Hilt
    implementation(libs.hilt.android)
    annotationProcessor(libs.hilt.compiler)
}
```

> **Importante:** después de agregar las dependencias, hay que hacer **Sync Now** en Android Studio y esperar a que compile.

---

### Paso 2 — Application + @AndroidEntryPoint

**MyApp.java** (clase Application nueva, vacía):

```java
@HiltAndroidApp
public class MyApp extends Application {
    // Vacía — Hilt hace todo
}
```

**MainActivity.java** (sin cambios internos, solo se anota):

```java
@AndroidEntryPoint
public class MainActivity extends AppCompatActivity {
    // Sin cambios internos
}
```

**AndroidManifest.xml** (hay que registrar la clase Application):

```xml
<application
    android:name=".MyApp"
    android:label="@string/app_name" ... >
```

**Por qué cada pieza es necesaria:**
- `@HiltAndroidApp` arranca el contenedor de Hilt. Va en la clase `Application`, una sola vez en todo el proyecto.
- `@AndroidEntryPoint` habilita la inyección en esa Activity/Fragment específica. Sin esto, cualquier `@Inject` en esa clase falla.

---

### Paso 3 — El NetworkModule (reemplaza al RetrofitClient)

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

**Anotación por anotación:**
- `@Module`: contiene métodos que crean objetos.
- `@InstallIn(SingletonComponent.class)`: define en qué scope vive (acá, toda la app).
- `@Provides`: este método devuelve una dependencia.
- `@Singleton`: garantiza una sola instancia, siempre la misma.

Nota estructural: `provideApiService` recibe un parámetro `Retrofit retrofit` — Hilt resuelve automáticamente esa dependencia porque ya está provista más arriba, en el mismo módulo, por `provideRetrofit()`.

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

    // Se usa apiService directamente:
    apiService.getPokemon(20)
        .enqueue(...);
}
```

**Qué cambia y qué no:**

| Se borra | Se mantiene |
|---|---|
| `RetrofitClient.java` — ya no se necesita, Hilt reemplaza al singleton manual | `PokemonApiService.java` (la interfaz de endpoints) |
| | Modelos (POJOs) |
| | Adapter + RecyclerView |

> Conclusión de la clase: **"La lógica de red no cambia, solo cambia quién la arma."**

---

### Hands-On #1 — Checklist completo para migrar un proyecto existente a Hilt

1. Agregar dependencias de Hilt en Gradle y sincronizar.
2. Crear `MyApp` con `@HiltAndroidApp` y registrarla en el Manifest.
3. Anotar `MainActivity` con `@AndroidEntryPoint`.
4. Crear `NetworkModule` con `@Provides` para Retrofit y ApiService.
5. Reemplazar `RetrofitClient.getInstance()` por `@Inject` en los Fragments.
6. Correr la app y verificar que todo sigue funcionando.

---

## 5. Persistencia de Datos en Android

**Pregunta guía de la clase:** ¿cómo y dónde guardar información en el dispositivo?

Se presentan 4 métodos, cada uno con un caso de uso claramente diferenciado:

| Caso de uso | Método | Por qué |
|---|---|---|
| Configuraciones simples | **DataStore** | Modo oscuro, idioma, flags de onboarding, último usuario logueado |
| Datos estructurados (relacionales, SQL) | **Room** | Notas, tareas, productos. Control total sobre SQL. Funciona offline |
| Archivos multimedia / documentos | **Files** (interno/externo) | Logs, imágenes descargadas, PDFs, archivos temporales |
| Datos sensibles (tokens, credenciales) | **EncryptedSharedPreferences** | Tokens JWT, credenciales cifradas, claves API |

### 5.1 — SharedPreferences vs DataStore (por qué DataStore lo reemplaza)

Ambos cumplen el mismo rol (guardar datos simples en clave-valor), pero son muy distintos por dentro:

| | SharedPreferences (legado) | DataStore (moderno) |
|---|---|---|
| Modo de ejecución | **Sincrónico.** `commit()` bloquea el hilo principal. `apply()` es asíncrono pero sin forma de saber si falló. | **Asíncrono con Kotlin Coroutines.** Nunca bloquea el hilo principal; las operaciones corren en segundo plano. |
| Manejo de errores | **Sin manejo de errores.** Si falla una escritura, no hay forma de saberlo. No lanza excepciones. | **Manejo de errores explícito.** Usa `Flow`: si algo falla, se puede capturar la excepción y reaccionar. |
| Consistencia | **Inconsistencia posible.** Si la app se mata a mitad de una escritura, los datos pueden quedar corruptos. | **Consistencia garantizada.** Las escrituras son transaccionales. Si la app muere, los datos no quedan a medias. |
| Vigencia | **API antigua.** Google ya no la recomienda para código nuevo. | **Recomendado por Google.** Es el reemplazo oficial de SharedPreferences desde 2020. |

**DataStore — código de ejemplo:**

```kotlin
// Dependencia (build.gradle)
implementation "androidx.datastore:datastore-preferences:1.0.0"

// Guardar un valor
val KEY = stringPreferencesKey("user")

suspend fun saveUser(name: String) {
    context.dataStore.edit { prefs ->
        prefs[KEY] = name
    }
}

// Leer un valor
val userFlow: Flow<String?> =
    context.dataStore.data.map { prefs ->
        prefs[KEY]
    }
```

Casos de uso mencionados explícitamente: configuraciones de la app (modo oscuro, idioma), recordar el último usuario logueado, flags de primera ejecución (onboarding completado).

---

### 5.2 — Room (base de datos relacional)

**Qué es:** capa de abstracción sobre SQLite. Usa anotaciones Java para definir la base de datos, entidades y consultas.

**Casos de uso:** grandes volúmenes de datos (notas, tareas, productos), control total sobre consultas SQL, apps que deben funcionar sin conexión.

**Componentes clave:** Entity · DAO · Database

**Entidad (tabla):**

```java
@Entity(tableName = "usuarios")
public class Usuario {
    @PrimaryKey(autoGenerate = true)
    public int id;
    public String nombre;
    public String email;
}
```

**DAO (acceso a datos):**

```java
@Dao
public interface UsuarioDao {
    @Insert
    void insertar(Usuario u);

    @Query("SELECT * FROM usuarios")
    List<Usuario> obtenerTodos();
}
```

---

### 5.3 — Files (almacenamiento de archivos)

**Almacenamiento Interno:**
- Solo accesible por la propia app
- Se elimina al desinstalar la app
- No requiere permisos especiales
- Ideal para datos privados, caché y configuración

```java
File file = new File(getFilesDir(), "log.txt");
FileWriter fw = new FileWriter(file);
fw.write("error: timeout");
fw.close();
```

**Almacenamiento Externo:**
- Accesible por otras apps y por el usuario
- Requiere el permiso `WRITE_EXTERNAL_STORAGE`
- Persiste tras la desinstalación de la app
- Ideal para imágenes, PDFs y descargas

```java
File dir = Environment.getExternalStoragePublicDir(
        Environment.DIRECTORY_DOWNLOADS);
File file = new File(dir, "doc.pdf");
```

---

### 5.4 — EncryptedSharedPreferences

**Qué es:** implementación segura de SharedPreferences que cifra automáticamente los datos. Parte de AndroidX Security.

**Cuándo usarlo:** tokens JWT para sesiones seguras, credenciales temporales (nunca contraseñas en texto plano), claves API y configuraciones de seguridad.

```java
// Dependencia
implementation "androidx.security:security-crypto:1.1.0-alpha06"

// Crear instancia
SharedPreferences sp = EncryptedSharedPreferences.create(
    "secure_prefs",
    MasterKeys.getOrCreate(MasterKeys.AES256_GCM_SPEC),
    context,
    PrefKeyEncryptionScheme.AES256_SIV,
    PrefValueEncryptionScheme.AES256_GCM
);

// Guardar / Leer
sp.edit().putString("token", jwt).apply();
String t = sp.getString("token", null);
```

---

### 5.5 — Árbol de decisión final: ¿cuándo usar cada uno?

```
¿Necesitás guardar una configuración, preferencia o flag?
    → DataStore / SharedPreferences

¿Tenés múltiples objetos relacionados entre sí?
    → Room

¿Es una imagen, PDF o archivo multimedia?
    → Files

¿Es un token, clave API o credencial sensible?
    → EncryptedSharedPreferences
```

---

## 6. Glosario rápido de términos nuevos de la clase

| Término | Definición corta |
|---|---|
| **SOLID (Dependency Inversion)** | Principio: los módulos de alto nivel no dependen de los de bajo nivel; ambos dependen de abstracciones. |
| **IoC (Inversión de Control)** | Principio: un agente externo entrega las dependencias, en vez de que la clase las busque. |
| **DI (Inyección de Dependencias)** | Técnica: pasar dependencias desde afuera (constructor, campo o método). |
| **Hilt** | Framework de Google sobre Dagger que automatiza la DI en Android. |
| **@HiltAndroidApp** | Anotación en la clase Application que arranca el contenedor de Hilt. |
| **@AndroidEntryPoint** | Anotación en Activity/Fragment que habilita la inyección. |
| **@Module** | Clase que agrupa métodos proveedores de dependencias. |
| **@InstallIn** | Define el scope de un módulo. |
| **@Provides** | Marca un método que construye y devuelve una dependencia. |
| **@Singleton** | Garantiza una única instancia de un objeto en todo el contenedor. |
| **@Inject** | Solicita una dependencia ya resuelta por Hilt. |
| **DataStore** | Almacenamiento clave-valor moderno, asíncrono, basado en Coroutines/Flow. |
| **Room** | ORM/capa de abstracción sobre SQLite para datos relacionales. |
| **Entity** | Clase Java anotada que representa una tabla en Room. |
| **DAO** | Interfaz con los métodos de acceso a una tabla de Room. |
| **Files (interno/externo)** | Almacenamiento de archivos en disco, privado o compartido. |
| **EncryptedSharedPreferences** | SharedPreferences con cifrado automático (AndroidX Security), para datos sensibles. |

---

## 7. Notas de contexto adicional (para la IA que reciba este resumen)

- Esta es la **Clase 4** de la materia *Desarrollo de Aplicaciones 1* (Android nativo en Java, cátedra Horacio Brizuela, UADE, 2C 2026).
- Es continuación directa de la clase de **Retrofit** (donde se armó el `RetrofitClient` singleton manual que acá se reemplaza) y de la clase de **Fragments + Navigation Component**.
- El alumno está cursando en paralelo el **TPO "Ronda"**: una app de compra-venta entre particulares con autenticación por OTP, listado paginado de publicaciones, filtros, y una API REST propia. La primera entrega es en **Android Nativo (Java)**, la segunda en **React Native con Expo**.
- Todo lo visto en esta clase (Hilt para inyectar Retrofit, y los métodos de Storage) es **directamente aplicable al TPO**: por ejemplo, usar Hilt para proveer el `ApiService` del backend propio, `EncryptedSharedPreferences` para guardar el token de sesión tras el login con OTP, y `DataStore` para preferencias de la app.
