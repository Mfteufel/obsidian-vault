---
tags: [android, android-studio, ide, herramientas, desarrollo-aplicaciones-1]
aliases: [Android Studio, IDE Android, AVD, Emulador Android]
---

# CC_Android Studio

> IDE oficial de Google para desarrollar apps Android. Incluye editor, emulador, debugger y sistema de build (Gradle).

## Qué trae

- Editor de código (Java / Kotlin)
- **Layout Editor** visual para las pantallas XML
- **AVD Manager** → emuladores de dispositivos virtuales
- **Logcat** → consola de logs del dispositivo
- **Gradle** → compilación y dependencias

---

## Instalación

| Paso | Detalle |
|---|---|
| 1 | Descargar desde `developer.android.com/studio` |
| 2 | Instalar el **Android SDK** (viene en el asistente) |
| 3 | Activar **virtualización** si no tenés celular Android físico |
| 4 | Crear un dispositivo virtual en **AVD Manager** |

### Virtualización (importante)

Si vas a usar emulador, necesitás la virtualización activada:

- **Windows/Intel** → habilitar `VT-x` en la BIOS + instalar HAXM / usar WHPX
- **Windows/AMD** → habilitar `SVM` en la BIOS + Hyper-V
- **Mac (Apple Silicon)** → ya viene, usar imágenes ARM64

> Si el emulador va lento o no arranca, casi siempre es esto.

---

## Estructura de un proyecto

```
MiApp/
├── app/
│   ├── manifests/
│   │   └── AndroidManifest.xml   ← permisos y declaración de Activities
│   ├── java/
│   │   └── com.ejemplo.miapp/
│   │       └── MainActivity.java ← lógica
│   └── res/
│       ├── layout/               ← pantallas XML
│       │   └── activity_main.xml
│       ├── drawable/             ← imágenes
│       ├── values/               ← strings, colores, temas
│       └── mipmap/               ← íconos
└── build.gradle                  ← dependencias y config
```

---

## Archivos clave

| Archivo | Para qué sirve |
|---|---|
| `AndroidManifest.xml` | Declara Activities, permisos, ícono, nombre de la app |
| `activity_main.xml` | Diseño de la pantalla |
| `MainActivity.java` | Código que controla esa pantalla |
| `build.gradle (Module)` | Dependencias, `minSdk`, `targetSdk` |
| `strings.xml` | Textos de la app (no hardcodear textos) |

### Ejemplo de `AndroidManifest.xml`

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android">
    <application
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name">

        <activity android:name=".MainActivity" android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>

        <activity android:name=".GameActivity" />
    </application>
</manifest>
```

> Toda Activity nueva **debe** declararse acá o la app crashea al abrirla.

### Ejemplo de `build.gradle` (Module: app)

```gradle
android {
    compileSdk 34

    defaultConfig {
        applicationId "com.ejemplo.miapp"
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

---

## Atajos útiles

| Atajo (Win/Linux) | Qué hace |
|---|---|
| `Shift` x2 | Buscar cualquier cosa |
| `Ctrl + Alt + L` | Formatear código |
| `Alt + Enter` | Sugerencia / importar clase |
| `Ctrl + /` | Comentar línea |
| `Shift + F10` | Ejecutar la app |

---

## Problemas típicos

| Síntoma | Causa habitual |
|---|---|
| Emulador no arranca | Virtualización desactivada en BIOS |
| "Gradle sync failed" | Sin internet o versión de Gradle desalineada |
| App crashea al abrir | Activity no declarada en el Manifest |
| `NullPointerException` en `findViewById` | ID del XML no coincide con el del código |

---

## Relacionado

- [[Clase 01 - Presentación de la materia]]
- [[Android - Estructura Básica y Pasaje de Actividades]]
- [[scrcpy]]
- [[Aplicaciones Nativas]]
- [[Actividad]]
