---
tags: [clase, react-native, expo, development-build, app-json, expo-image-picker, desarrollo-aplicaciones-1]
---

# Clase 10 — Development Build y expo-image-picker

**Materia:** Desarrollo de Aplicaciones 1 — 2C 2026

> ℹ️ **Nota:** el PDF de esta clase arranca con los mismos bloques 01–03 de la [[Clase 09 - Gestión de Sesión en React Native]] (useContext, Storage, Biometría). Lo **nuevo** son los bloques **04 y 05**.

---

## Agenda

| # | Bloque | Contenido | Estado |
|---|---|---|---|
| 01 | [[Context API (useContext)]] | Estado global · AuthContext · `useAuth()` | 🔁 Repaso (Clase 9) |
| 02 | [[Storage en React Native]] | AsyncStorage · SecureStore · WatermelonDB | 🔁 Repaso (Clase 9) |
| 03 | [[Biometría en React Native]] | `expo-local-authentication` | 🔁 Repaso (Clase 9) |
| 04 | [[Development Build]] | Cómo se activa en la app y cómo funciona | 🆕 Nuevo |
| 05 | [[expo-image-picker\|Select Pick Image]] | Cómo se seleccionan imágenes | 🆕 Nuevo |

---

## Bloques 01–03 · Repaso de la Clase 9

Resumen de una línea por bloque. El desarrollo completo está en las notas linkeadas.

| Tema | Idea clave | Detalle |
|---|---|---|
| **useContext** | Evita el *prop drilling* compartiendo estado sin pasar props nivel por nivel | [[Context API (useContext)#El problema: Prop Drilling]] |
| **Custom hook `useAuth()`** | Encapsula `useContext` y valida que se use dentro del Provider | [[Context API (useContext)#Custom Hook — useAuth()]] |
| **Separar contextos** | Un contexto por responsabilidad (Auth, Theme, Cart) | [[Context API (useContext)#Best practice — separación de contextos]] |
| **SecureStore vs AsyncStorage** | Tokens y datos sensibles → **siempre SecureStore** | [[Storage en React Native#¿Cómo elegir la herramienta correcta?]] |
| **WatermelonDB** | Base relacional en JS puro; equivalente a Room | [[Storage en React Native#¿Y si necesito más? — WatermelonDB]] |
| **Token expirado** | Interceptor de Axios + `logout` **inyectado** (no `useContext`) | [[Context API (useContext)#El interceptor vive fuera del árbol de React]] |
| **Biometría** | Verificar hardware + enrolamiento **antes** del prompt | [[Biometría en React Native#Verificando disponibilidad biométrica]] |

---

## Bloque 04 · [[Development Build]]

### [[app.json y Config Plugins|¿Qué es app.json?]]

La **configuración de la app Expo en un solo archivo**: identidad, íconos, permisos y plugins nativos. Expo lo lee cada vez que genera o compila el proyecto nativo.

| Concepto | Android nativo (Java) | Expo — `app.json` |
|---|---|---|
| Identificador de la app | `applicationId` (build.gradle) | `android.package` |
| Versión interna / visible | `versionCode` / `versionName` | `android.versionCode` / `version` |
| Permisos | `<uses-permission>` (Manifest) | `android.permissions` / `plugins` |
| Nombre e ícono | `android:label` / `android:icon` | `name` / `icon` |
| Orientación | `android:screenOrientation` | `orientation` |

> 💡 Lo que en Android se reparte entre `build.gradle` y `AndroidManifest.xml`, en Expo vive en `app.json`.

### [[app.json y Config Plugins#Anatomía de un app.json|Anatomía de un app.json]]

```json
{
  "expo": {
    "name": "Mi App",
    "slug": "mi-app",
    "version": "1.0.0",
    "icon": "./assets/icon.png",
    "orientation": "portrait",
    "android": {
      "package": "com.uade.miapp",
      "versionCode": 1
    },
    "ios": {
      "bundleIdentifier": "com.uade.miapp"
    },
    "plugins": ["expo-image-picker"]
  }
}
```

| Grupo | Campos |
|---|---|
| **Identidad** | `name` (visible) · `slug` (ID en Expo) · `version` |
| **Visual** | `icon` · `orientation` |
| **Plataformas** | `android.package` / `ios.bundleIdentifier` — **no se cambian una vez publicado** |
| **Extensiones nativas** | `plugins` — modifican permisos, Manifest, Info.plist |

> ¿Valores dinámicos (variables de entorno)? Usar `app.config.js` en vez de `app.json`.

### [[app.json y Config Plugins#Config plugins — app.json modifica el código nativo|Config plugins — app.json modifica el código nativo]]

Los plugins se ejecutan **al generar el proyecto nativo** (*prebuild*):

```
app.json  →  npx expo prebuild  →  android/ · ios/  →  Manifest · Info.plist
plugins      ejecuta cada plugin    carpetas nativas     permisos y textos
                                    generadas            aplicados
```

| Idea | Detalle |
|---|---|
| **CNG** (Continuous Native Generation) | `android/` e `ios/` se generan desde `app.json` y `package.json` |
| **No se editan a mano** | Si cambia la configuración, se regeneran |
| **Ejemplo: `expo-image-picker`** | Su plugin configura textos de permiso en iOS y bloquea permisos no usados en Android |

> 💡 Equivale a lo que en Android nativo editás directamente en `AndroidManifest.xml`.

### [[app.json y Config Plugins#¿Por qué Expo Go no aplica esta configuración?|Expo Go no aplica tu configuración nativa]]

Expo Go es un **binario precompilado, el mismo para todos los proyectos**. Solo descarga y ejecuta tu JS.

| Cambio | En Expo Go | ¿Rebuild? |
|---|---|---|
| Código JS / TS | ✅ Hot reload | No |
| `name` / `icon` | ❌ Se ve "Expo Go" | Sí |
| Permisos / plugins | ❌ Se ignoran | Sí |
| Librería nativa no incluida en Expo Go | ❌ No funciona | Sí |

> Para ver estos cambios hace falta **nuestro propio binario → Development Build**.

### [[Development Build|¿Qué es un Development Build?]]

**Tu propia versión de Expo Go**: incluye las librerías nativas y la configuración de tu proyecto.

| | Expo Go | Development Build |
|---|---|---|
| Binario | Genérico, se baja del store | **Propio**: tu nombre, ícono y package |
| Librerías nativas | Solo las incluidas en Expo Go | **Cualquiera** |
| `app.json` (plugins, permisos) | Se ignora | **Se aplica** ✅ |
| Hot reload de JS | ✅ | ✅ |
| Librería clave | — | `expo-dev-client` |

> 💡 Es la opción recomendada por Expo para desarrollar una app que se va a publicar.

### [[Development Build#Crear un Development Build en local|Crear un Development Build en local]]

```bash
# 1. Instalar el cliente de desarrollo
npx expo install expo-dev-client

# 2. Compilar e instalar en el emulador
npx expo run:android

# En un dispositivo físico (USB + depuración)
npx expo run:android --device
```

| Requisitos | Detalle |
|---|---|
| Android Studio + emulador | Los mismos de Android nativo |
| Cuenta de Expo | ❌ No hace falta |

**¿Qué hace `npx expo run:android`?**

1. **Prebuild:** genera la carpeta `android/`
2. **Gradle:** compila el proyecto
3. **Instala** la app en el emulador
4. **Levanta** el servidor de desarrollo

### [[Development Build#¿Cuándo hay que recompilar?|¿Cuándo hay que recompilar?]]

| ¿Qué cambiaste? | Qué hacer |
|---|---|
| ✅ **Solo JS / TS** (pantallas, estilos, lógica, Axios) | `npx expo start` — hot reload |
| ⚠️ **Algo nativo:** librería con código nativo, `app.json`, SDK de Expo | `npx expo prebuild --clean` + `npx expo run:android` |

> **Próximo paso en clase:** instalamos `expo-image-picker` y configuramos su plugin → **hay que recompilar**.

---

## Bloque 05 · [[expo-image-picker|Select Pick Image]]

### [[expo-image-picker#Activity Result API vs expo-image-picker|Activity Result API vs expo-image-picker]]

```java
// Android nativo — Activity Result API
ActivityResultLauncher<PickVisualMediaRequest> pickMedia =
    registerForActivityResult(new PickVisualMedia(), uri -> {
        if (uri != null) {
            imageView.setImageURI(uri);
        } else {
            // el usuario canceló
        }
    });

pickMedia.launch(new PickVisualMediaRequest.Builder()
    .setMediaType(PickVisualMedia.ImageOnly.INSTANCE)
    .build());
```

```javascript
// React Native — expo-image-picker
const result = await ImagePicker.launchImageLibraryAsync({
    mediaTypes: ['images'],
});

if (!result.canceled) {
    setImage(result.assets[0].uri);
} else {
    // el usuario canceló
}
```

| Android (Java) | Expo |
|---|---|
| `registerForActivityResult(...)` | No hace falta: devuelve una `Promise` |
| `pickMedia.launch(...)` | `launchImageLibraryAsync()` |
| Callback `uri -> { ... }` | `await result` |
| `uri == null` (canceló) | `result.canceled === true` |
| `uri` | `result.assets[0].uri` |

> Repaso del patrón nativo: [[ActivityResultLauncher]]

### [[expo-image-picker#Instalación y configuración del plugin|Instalación y configuración del plugin]]

```bash
npx expo install expo-image-picker
```

```json
{
  "expo": {
    "plugins": [
      [
        "expo-image-picker",
        {
          "photosPermission": "Accedemos a tus fotos para publicar",
          "cameraPermission": false,
          "microphonePermission": false
        }
      ]
    ]
  }
}
```

| Opción | Qué hace |
|---|---|
| `photosPermission` | Texto que ve el usuario cuando iOS pide acceso a las fotos |
| `cameraPermission: false` | Bloquea el permiso `CAMERA` en Android |
| `microphonePermission: false` | Quita `RECORD_AUDIO`, que el plugin agrega por defecto |

> ⚠️ El módulo funciona en Expo Go, pero **esta configuración solo se aplica en un Development Build**.

### [[expo-image-picker#Abrir la galería y mostrar la imagen|Abrir la galería y mostrar la imagen]]

```jsx
import { useState } from 'react';
import { Button, Image, View } from 'react-native';
import * as ImagePicker from 'expo-image-picker';

export function PhotoPicker() {
    const [image, setImage] = useState(null);

    const pickImage = async () => {
        const result = await ImagePicker.launchImageLibraryAsync({
            mediaTypes: ['images'],
            allowsEditing: true,
            aspect: [4, 3],
            quality: 0.7,
        });

        if (!result.canceled) {
            setImage(result.assets[0].uri);
        }
    };

    return (
        <View>
            <Button title="Elegir foto" onPress={pickImage} />
            {image && (
                <Image source={{ uri: image }}
                       style={{ width: 200, height: 150 }} />
            )}
        </View>
    );
}
```

| Idea clave | Detalle |
|---|---|
| **Sin pedir permiso** | Abrir la galería no requiere solicitar permisos (según la doc de Expo) |
| **Chequear `canceled`** | Si cierra la galería, `canceled` es `true` y `assets` es `null` |
| **`assets[0].uri`** | URI local, se usa directo como `source` de `<Image>` |

### [[expo-image-picker#Opciones de launchImageLibraryAsync({ ... })|Opciones de la galería y el objeto asset]]

| Opción | Qué hace | Default |
|---|---|---|
| `allowsEditing` | Muestra un recorte después de elegir | `false` |
| `aspect` | Proporción del recorte `[x, y]` (solo Android) | — |
| `quality` | Compresión de 0 a 1 | `1` |
| `allowsMultipleSelection` | Permite elegir varias imágenes | `false` |
| `selectionLimit` | Máximo de imágenes (`0` = límite del sistema) | `0` |

| Campo de `assets[i]` | Contenido |
|---|---|
| `uri` | Ruta local del archivo |
| `width` / `height` | Dimensiones en px |
| `fileSize` | Tamaño en bytes |
| `mimeType` | Ej.: `image/jpeg` |
| `fileName` | Nombre del archivo |

> ⚠️ `allowsMultipleSelection` y `allowsEditing` son **excluyentes**: con selección múltiple no hay recorte, y `allowsEditing` se ignora.

---

## Relacionado

- [[Clase 09 - Gestión de Sesión en React Native]]
- [[Development Build]]
- [[app.json y Config Plugins]]
- [[expo-image-picker]]
- [[Expo]]
- [[ActivityResultLauncher]]
- [[AndroidManifest]]
- [[Gradle]]
- [[Context API (useContext)]]
- [[Storage en React Native]]
- [[Biometría en React Native]]
