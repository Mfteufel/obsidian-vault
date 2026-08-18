---
tags: [android, manifest, permisos, configuracion, desarrollo-aplicaciones-1]
aliases: [AndroidManifest.xml, Manifest, Manifiesto]
---

# CC_AndroidManifest.xml

> Archivo de configuración central de la app. Sin él, Android **no sabe qué pantallas tiene tu app ni cómo iniciarlas**.

## Para qué sirve

| Función | Detalle |
|---|---|
| **Declara componentes** | Activity, Service, BroadcastReceiver, ContentProvider |
| **Pide permisos** | Cámara, internet, ubicación, almacenamiento |
| **Define la pantalla de inicio** | Qué Activity se abre al tocar el ícono |
| **Metadatos globales** | Tema, nombre, ícono, backup, múltiples pantallas |

---

## Ejemplo completo

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.tuapp">

    <!-- Permisos necesarios -->
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="Mi Aplicación"
        android:theme="@style/Theme.MiApp">

        <!-- Actividad principal -->
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>

        <!-- Actividad secundaria -->
        <activity android:name=".SecondActivity"/>

    </application>
</manifest>
```

---

## El bloque que define la pantalla de inicio

```xml
<intent-filter>
    <action android:name="android.intent.action.MAIN" />
    <category android:name="android.intent.category.LAUNCHER" />
</intent-filter>
```

| Línea | Significa |
|---|---|
| `action.MAIN` | Este es el punto de entrada |
| `category.LAUNCHER` | Mostrar el ícono en el menú de apps |

> Solo **una** Activity debería tener este bloque.

---

## Permisos comunes

| Permiso | Para qué |
|---|---|
| `INTERNET` | Llamadas a APIs, descargar datos |
| `ACCESS_FINE_LOCATION` | GPS preciso |
| `CAMERA` | Usar la cámara |
| `READ_EXTERNAL_STORAGE` | Leer archivos del dispositivo |
| `POST_NOTIFICATIONS` | Mostrar notificaciones (Android 13+) |

> ⚠️ Los permisos **peligrosos** (cámara, ubicación, contactos) además hay que pedirlos **en runtime**, no alcanza con el Manifest.

---

## Declarar los otros componentes

```xml
<!-- Service -->
<service android:name=".MusicService" />

<!-- BroadcastReceiver -->
<receiver android:name=".MiReceptor">
    <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
    </intent-filter>
</receiver>

<!-- ContentProvider -->
<provider
    android:name=".MiProveedor"
    android:authorities="com.tuapp.provider" />
```

---

## Errores típicos

| Síntoma | Causa |
|---|---|
| `ActivityNotFoundException` | La Activity no está declarada en el Manifest |
| La app no aparece en el menú | Falta el `intent-filter` con MAIN/LAUNCHER |
| Falla la llamada a la API | Falta `<uses-permission INTERNET>` |
| Crash al usar la cámara | Permiso declarado pero no pedido en runtime |

---

## Relacionado

- [[Clase 02 - Android Primeros Pasos]]
- [[Actividad]]
- [[Servicio]]
- [[Receptor de Transmisión]]
- [[Proveedor de Contenido]]
- [[Gradle]]
- [[Android Studio]]
