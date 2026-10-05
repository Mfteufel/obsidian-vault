---
tags: [android, datastore, storage, coroutines, jetpack, desarrollo-aplicaciones-1]
aliases: [DataStore, Preferences DataStore, stringPreferencesKey]
---

# CC_DataStore

> Reemplazo moderno de `SharedPreferences` para guardar datos simples clave-valor. Es **asíncrono**, usa **Kotlin Coroutines** y `Flow`, y es el recomendado por Google desde 2020.

## Cuándo usarlo

- Configuraciones de la app (modo oscuro, idioma)
- Recordar el último usuario logueado
- Flags de primera ejecución (onboarding completado)

> Para elegir entre los 4 métodos de persistencia: ver la tabla comparativa en [[Clase 04 - Inyección de Dependencias y Storage]]

---

## Por qué reemplaza a SharedPreferences

Cumplen el mismo rol (guardar clave-valor), pero por dentro son muy distintos:

| | **SharedPreferences** (legado) | **DataStore** (moderno) |
|---|---|---|
| Ejecución | Sincrónico. `commit()` bloquea el hilo principal. `apply()` es asíncrono pero sin forma de saber si falló | **Asíncrono con Coroutines** — nunca bloquea el hilo principal |
| Manejo de errores | Si falla una escritura, no te enterás. No lanza excepciones | **Explícito con `Flow`** — podés capturar la excepción y reaccionar |
| Consistencia | Si la app se mata a mitad de una escritura, los datos pueden quedar corruptos | **Transaccional** — si la app muere, los datos no quedan a medias |
| Recomendación de Google | Ya no se recomienda para código nuevo | **Reemplazo oficial** desde 2020 |

---

## Dependencia

```gradle
implementation "androidx.datastore:datastore-preferences:1.0.0"
```

---

## Guardar un valor

```kotlin
val KEY = stringPreferencesKey("user")

suspend fun saveUser(name: String) {
    context.dataStore.edit { prefs ->
        prefs[KEY] = name
    }
}
```

## Leer un valor

```kotlin
val userFlow: Flow<String?> =
    context.dataStore.data.map { prefs ->
        prefs[KEY]
    }
```

> `edit {}` es una función `suspend` — hay que llamarla desde una coroutine. `data` devuelve un `Flow`, que se observa (no se lee una sola vez como con `SharedPreferences.getString()`).

---

## Tipos de keys disponibles

| Función | Tipo |
|---|---|
| `stringPreferencesKey("k")` | `String` |
| `intPreferencesKey("k")` | `Int` |
| `booleanPreferencesKey("k")` | `Boolean` |
| `floatPreferencesKey("k")` | `Float` |
| `doublePreferencesKey("k")` | `Double` |
| `stringSetPreferencesKey("k")` | `Set<String>` |

---

## Ejemplo completo — flag de onboarding

```kotlin
private val ONBOARDING_KEY = booleanPreferencesKey("onboarding_completo")

// Guardar
suspend fun marcarOnboardingCompleto() {
    context.dataStore.edit { prefs ->
        prefs[ONBOARDING_KEY] = true
    }
}

// Leer (como Flow, se puede observar en tiempo real)
val onboardingCompletoFlow: Flow<Boolean> =
    context.dataStore.data.map { prefs ->
        prefs[ONBOARDING_KEY] ?: false
    }
```

---

## Ojo con esto

- `DataStore` **no reemplaza** a [[Room]] — para datos relacionados entre sí (listas, tablas) seguís usando Room
- No es para **datos sensibles** — para eso está [[EncryptedSharedPreferences]]
- Cada operación de lectura/escritura es asíncrona: si necesitás el valor **ya mismo** en el hilo principal, `DataStore` no es la herramienta (usá un `Flow.collect` o `runBlocking` solo si es estrictamente necesario)

---

## Relacionado

- [[Clase 04 - Inyección de Dependencias y Storage]]
- [[Room]]
- [[EncryptedSharedPreferences]]
- [[Almacenamiento de Archivos (Files)]]
