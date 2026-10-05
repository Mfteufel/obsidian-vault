---
tags: [clase, android, dependency-injection, hilt, storage, desarrollo-aplicaciones-1]
---
![[Clase 4 - DI & Storage.pdf]]
# Clase 04 — Inyección de Dependencias & Storage

**Materia:** Desarrollo de Aplicaciones 1 — 2C 2026

---

## Agenda

| # | Bloque | Contenido |
|---|---|---|
| 01 | Repaso rápido | `RetrofitClient` como singleton manual |
| 02 | ¿Por qué DI? | Los problemas del acoplamiento |
| 03 | Hilt + Retrofit | Setup, módulos y configuración |
| 04 | Hands-on #1 | Migrar el proyecto a Hilt |
| 05 | Storage | Persistencia en Android |

---

## 1. [[Retrofit - Consumo de API REST|¿Dónde quedamos?]]

La clase pasada armamos un **singleton manual** para Retrofit:

```java
public class RetrofitClient {
    private static Retrofit instance;

    public static Retrofit getInstance() {
        if (instance == null) {
            instance = new Retrofit.Builder()
                    .baseUrl("https://pokeapi.co/api/v2/")
                    .addConverterFactory(...).build();
        }
        return instance;
    }
}
```

> Funciona, pero **¿es suficiente cuando la app crece?**

---

## 2. [[Inyección de Dependencias (DI)|¿Por qué DI?]]

### Los problemas del singleton manual

| Necesitamos...                                                   | Problema                                            |
| ---------------------------------------------------------------- | --------------------------------------------------- |
| Agregar un header de `Authorization` a **todos** los requests    | Hay que tocar `RetrofitClient` a mano               |
| Testear Fragments que usan los services de Retrofit              | El `static` hace imposible reemplazarlo por un mock |
| Compartir la misma instancia entre Fragments **sin usar static** | El singleton manual obliga a usar `static`          |
|                                                                  |                                                     |

> El singleton manual se queda corto. Necesitamos una forma mejor de **construir y distribuir objetos**.

### La cadena de conceptos

```
SOLID (principio) → IoC (principio) → DI (técnica) → Hilt (herramienta)
```

| Nivel             | Concepto                           | Frase clave                                                                                         |
| ----------------- | ---------------------------------- | --------------------------------------------------------------------------------------------------- |
| Principio (SOLID) | **Dependency Inversion**           | "Los módulos de alto nivel no deben depender de los de bajo nivel. Ambos dependen de abstracciones" |
| Principio         | **Inversión de Control (IoC)**     | "No busques tus dependencias, ya te las doy yo"                                                     |
| Técnica           | **Inyección de Dependencias (DI)** | Pasar las dependencias desde afuera (constructor, campo o método)                                   |
| Herramienta       | **Hilt**                           | Automatiza la DI con `@Inject` y `@Provides`                                                        |

> Desarrollo completo en [[Inyección de Dependencias (DI)]]

### Antes vs Después

```java
// ANTES — el Fragment SABE cómo crear todo
Retrofit retrofit = RetrofitClient.getInstance();
PokemonApiService api = retrofit.create(PokemonApiService.class);

// DESPUÉS — el Fragment solo DECLARA qué necesita
@Inject
PokemonApiService api;
// Hilt se encarga de crear Retrofit, configurarlo e inyectar el service
```

---

## 3. [[Hilt|Hilt + Retrofit]]

Framework de Google construido sobre **Dagger** que simplifica la DI en Android.

| Pieza | Qué hace |
|---|---|
| **Contenedor** | Crea y guarda objetos. Vos no hacés `new` |
| **Scopes** | Definen cuánto vive una instancia (`@Singleton`, `@ActivityScoped`) |
| **Ciclo de vida** | Hilt inyecta en el momento correcto del ciclo de vida de Android |

### Anotaciones clave

| Anotación | Para qué |
|---|---|
| `@HiltAndroidApp` | En la `Application` — arranca el contenedor |
| `@AndroidEntryPoint` | En Activity/Fragment — habilita `@Inject` |
| `@Module` + `@Provides` | Define cómo crear un objeto |
| `@Singleton` | Una sola instancia en toda la app |
| `@Inject` | Pide una dependencia |

> Desarrollo completo, paso a paso con código, en [[Hilt]]

---

## 4. [[Hilt#Hands-on — Migrar un proyecto existente a Hilt|Hands-on #1 — Migrar el proyecto a Hilt]]

1. Agregar dependencias de Hilt en Gradle y sincronizar
2. Crear `MyApp` con `@HiltAndroidApp` y registrarla en el Manifest
3. Anotar `MainActivity` con `@AndroidEntryPoint`
4. Crear `NetworkModule` con `@Provides` para Retrofit y `ApiService`
5. Reemplazar `RetrofitClient.getInstance()` por `@Inject` en los Fragments
6. Correr la app y verificar que todo sigue funcionando

> Checklist detallado con código: [[Hilt#Hands-on — Migrar un proyecto existente a Hilt]]

---

## 5. Persistencia de Datos en Android

**¿Cómo y dónde guardar información en el dispositivo?**

| Caso de uso | Método | Por qué |
|---|---|---|
| Configuraciones simples | [[DataStore]] | Modo oscuro, idioma, flags de onboarding, último usuario logueado |
| Datos estructurados (SQL) | [[Room]] | Notas, tareas, productos. Control total sobre SQL. Funciona offline |
| Archivos multimedia / documentos | [[Almacenamiento de Archivos (Files)]] | Logs, imágenes descargadas, PDFs, temporales |
| Datos sensibles (tokens, credenciales) | [[EncryptedSharedPreferences]] | Tokens JWT, credenciales cifradas, claves API |

### [[DataStore|SharedPreferences vs DataStore]]

Cumplen el mismo rol (clave-valor), pero por dentro son muy distintos:

| | SharedPreferences (legado) | DataStore (moderno) |
|---|---|---|
| Ejecución | Sincrónico — `commit()` bloquea el hilo principal | Asincrónico con Coroutines — nunca bloquea |
| Manejo de errores | No lanza excepciones, no sabés si falló | Explícito con `Flow` |
| Consistencia | Puede corromperse si la app muere a mitad de escritura | Escrituras transaccionales garantizadas |
| Recomendación de Google | Ya no se recomienda para código nuevo | Reemplazo oficial desde 2020 |

### Árbol de decisión — ¿cuál uso?

```
¿Necesitás guardar una configuración, preferencia o flag?
    → DataStore

¿Tenés múltiples objetos relacionados entre sí?
    → Room

¿Es una imagen, PDF o archivo multimedia?
    → Files

¿Es un token, clave API o credencial sensible?
    → EncryptedSharedPreferences
```

---

## Relacionado

- [[Clase 03 - Fragments y Navigation Component]]
- [[Inyección de Dependencias (DI)]]
- [[Hilt]]
- [[Retrofit - Consumo de API REST]]
- [[DataStore]]
- [[Room]]
- [[Almacenamiento de Archivos (Files)]]
- [[EncryptedSharedPreferences]]
