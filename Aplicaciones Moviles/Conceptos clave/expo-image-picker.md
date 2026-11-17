---
tags: [react-native, expo, expo-image-picker, imagenes, galeria, permisos, desarrollo-aplicaciones-1]
aliases: [expo-image-picker, ImagePicker, launchImageLibraryAsync, Select Pick Image, Seleccionar imagen]
---

# CC_expo-image-picker

> Módulo del [[Expo#Las 4 piezas de Expo|Expo SDK]] para **elegir imágenes de la galería**. Es el equivalente en React Native de la Activity Result API en Android nativo.

## Activity Result API vs expo-image-picker

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

// al tocar el botón
pickMedia.launch(new PickVisualMediaRequest.Builder()
    .setMediaType(PickVisualMedia.ImageOnly.INSTANCE)
    .build());
```

```javascript
// React Native — expo-image-picker
// al tocar el botón
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
| `registerForActivityResult(...)` | **No hace falta**: la función devuelve una `Promise` |
| `pickMedia.launch(...)` | `launchImageLibraryAsync()` |
| Callback `uri -> { ... }` | `await result` |
| `uri == null` (canceló) | `result.canceled === true` |
| `uri` | `result.assets[0].uri` |

> Mismo problema, mucho menos código: en Android hay que **registrar el launcher antes** y resolver en un callback; en RN alcanza con `await`. Repasar el patrón nativo: [[ActivityResultLauncher]].

---

## Instalación y configuración del plugin

```bash
npx expo install expo-image-picker
```

```json
// app.json
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
| `photosPermission` | Texto que ve el usuario cuando **iOS** pide acceso a las fotos |
| `cameraPermission: false` | **Bloquea** el permiso `CAMERA` en Android (si no se usa la cámara) |
| `microphonePermission: false` | Quita `RECORD_AUDIO`, que el plugin agrega **por defecto** en Android |

> ⚠️ El módulo **funciona en Expo Go**, pero **esta configuración solo se aplica en un [[Development Build]]**. Después de tocar `app.json` hay que recompilar. Cómo funcionan los plugins: [[app.json y Config Plugins#Config plugins — app.json modifica el código nativo]].

> 💡 Pedir solo los permisos que la app realmente usa es lo mismo que declarar solo lo necesario en el [[AndroidManifest]].

---

## Abrir la galería y mostrar la imagen

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
| **Sin pedir permiso** | Según la documentación de Expo, **abrir la galería no requiere solicitar permisos** |
| **Chequear `canceled`** | Si el usuario cierra la galería, `canceled` es `true` y `assets` es `null` |
| **`assets[0].uri`** | URI **local** del archivo. Se usa directo como `source` de `<Image>` |
| **Estado con `useState`** | Guardar el `uri` en estado dispara el re-render y muestra la imagen — ver [[React Hooks#useState — estado y re-render]] |

---

## Opciones de `launchImageLibraryAsync({ ... })`

| Opción | Qué hace | Default |
|---|---|---|
| `mediaTypes` | Qué tipo de archivos mostrar (`['images']`) | — |
| `allowsEditing` | Muestra un recorte después de elegir | `false` |
| `aspect` | Proporción del recorte `[x, y]` (**solo Android**) | — |
| `quality` | Compresión de 0 a 1 | `1` |
| `allowsMultipleSelection` | Permite elegir varias imágenes | `false` |
| `selectionLimit` | Máximo de imágenes (`0` = límite del sistema) | `0` |

> ⚠️ **`allowsMultipleSelection` y `allowsEditing` son excluyentes:** con selección múltiple no hay recorte, y `allowsEditing` se ignora.

### Ejemplo — varias imágenes

```javascript
const result = await ImagePicker.launchImageLibraryAsync({
    mediaTypes: ['images'],
    allowsMultipleSelection: true,
    selectionLimit: 5,
    quality: 0.7,
});

if (!result.canceled) {
    const uris = result.assets.map(a => a.uri);
    setImages(uris);
}
```

---

## El objeto `result.assets[i]`

| Campo | Contenido |
|---|---|
| `uri` | Ruta local del archivo |
| `width` / `height` | Dimensiones en px |
| `fileSize` | Tamaño en bytes |
| `mimeType` | Ej.: `image/jpeg` |
| `fileName` | Nombre del archivo |

> Útil para **validar antes de subir**: por ejemplo, rechazar archivos pesados con `fileSize` o tipos no permitidos con `mimeType`. Para publicar la imagen en un backend se manda como `FormData` con [[Axios y fetch API|Axios]].

---

## Errores comunes

| ❌ Qué NO hacer | Por qué | ✅ Qué hacer |
|---|---|---|
| Leer `result.assets[0]` sin chequear `canceled` | Si el usuario cancela, `assets` es `null` → crash | Siempre `if (!result.canceled)` |
| Configurar el plugin y probar en Expo Go | Expo Go ignora la config nativa | Probar en un [[Development Build]] |
| Combinar `allowsMultipleSelection` con `allowsEditing` | Son excluyentes: el recorte se ignora | Elegir uno de los dos |
| Dejar permisos que no se usan (cámara, micrófono) | Aparecen en el Manifest y la app pide más de lo necesario | `cameraPermission: false`, `microphonePermission: false` |
| Usar `aspect` esperando que funcione en iOS | Solo aplica en Android | No depender de él en iOS |

---

## Relacionado

- [[Clase 10 - Development Build y expo-image-picker]]
- [[Development Build]]
- [[app.json y Config Plugins]]
- [[ActivityResultLauncher]]
- [[Expo]]
- [[React Hooks]]
- [[Axios y fetch API]]
