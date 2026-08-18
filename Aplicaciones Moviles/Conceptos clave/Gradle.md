---
tags: [android, gradle, build, dependencias, desarrollo-aplicaciones-1]
aliases: [Gradle, build.gradle, Dependencias Android, Sync Now]
---

# CC_Gradle

> Sistema de build de Android. Compila la app y maneja las **dependencias** (librerías externas).

## Los 2 archivos importantes

| Archivo | Nivel | Qué configura |
|---|---|---|
| `build.gradle` | **Project Level** | Configuración **global** del proyecto |
| `build.gradle` | **Module Level (app)** | Configuración **específica de la app** ← el que más usás |

> 📌 Las dependencias se agregan en el **Module: app**, dentro del bloque `dependencies { }`.

---

## Cómo agregar una librería

1. Abrir `build.gradle (Module: app)`
2. Agregar la línea dentro de `dependencies { }`
3. Click en **"Sync Now"** (aparece arriba a la derecha)

```gradle
dependencies {
    implementation 'com.google.code.gson:gson:2.10.1'
}
```

> Sin el **Sync Now** la librería no se descarga y el import no funciona.

---

## Anatomía del build.gradle (Module: app)

```gradle
android {
    namespace 'com.tuapp'
    compileSdk 34

    defaultConfig {
        applicationId "com.tuapp"
        minSdk 24
        targetSdk 34
        versionCode 1
        versionName "1.0"
    }
}

dependencies {
    implementation 'androidx.appcompat:appcompat:1.6.1'
    implementation 'com.google.android.material:material:1.11.0'
}
```

### Qué significa cada cosa

| Campo | Significa |
|---|---|
| `applicationId` | ID único de la app en Play Store |
| `minSdk` | Versión **mínima** de Android soportada |
| `targetSdk` | Versión para la que está optimizada |
| `compileSdk` | Versión del SDK con la que se compila |
| `versionName` | Versión visible para el usuario ("1.0") |
| `versionCode` | Versión interna, número entero que sube en cada release |

---

## Formato de una dependencia

```
'grupo:artefacto:versión'
 └─────┘ └───────┘ └────┘
 quién   qué       cuál
```

**Ejemplo:** `'com.squareup.retrofit2:retrofit:2.9.0'`

---

## Librerías útiles para el TPO

| Librería | Para qué |
|---|---|
| `retrofit` | Consumir APIs REST |
| `gson` | Convertir JSON ↔ objetos Java |
| `glide` / `picasso` | Cargar imágenes desde URL |
| `room` | Base de datos local sobre SQLite |
| `material` | Componentes de Material Design |

---

## Otros archivos root

| Archivo | Para qué |
|---|---|
| `settings.gradle` | Módulos que forman parte del proyecto |
| `gradle.properties` | Propiedades globales (memoria, flags) |
| `gradle-wrapper.properties` | Versión de Gradle a usar |
| `libs.versions.toml` | Catálogo centralizado de versiones |
| `local.properties` | Ruta del SDK — **no se sube a Git** |
| `proguard-rules.pro` | Reglas de ofuscación para el release |

---

## Problemas típicos

| Error | Solución |
|---|---|
| "Gradle sync failed" | Revisar internet / versión de la librería |
| "Duplicate class" | Dos librerías traen lo mismo → sacar una |
| "minSdk conflict" | La librería pide un `minSdk` mayor al tuyo |
| El import no aparece | Faltó tocar **Sync Now** |

---

## Relacionado

- [[Clase 02 - Android Primeros Pasos]]
- [[Android Studio]]
- [[AndroidManifest]]
- [[Git Flow - Ramas y Pull Requests]]
