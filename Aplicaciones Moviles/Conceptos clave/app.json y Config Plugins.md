---
tags: [react-native, expo, app-json, config-plugins, cng, prebuild, desarrollo-aplicaciones-1]
aliases: [app.json, app.config.js, Config Plugins, CNG, Continuous Native Generation, prebuild, expo prebuild]
---

# CC_app.json y Config Plugins

> `app.json` es la **configuración de la app Expo en un solo archivo**: identidad, íconos, permisos y plugins nativos. Expo lo lee cada vez que genera o compila el proyecto nativo.

## Equivalencia con Android nativo

| Concepto | Android nativo (Java) | Expo — `app.json` |
|---|---|---|
| Identificador de la app | `applicationId` (build.gradle) | `android.package` |
| Versión interna / visible | `versionCode` / `versionName` | `android.versionCode` / `version` |
| Permisos | `<uses-permission>` (Manifest) | `android.permissions` / `plugins` |
| Nombre e ícono | `android:label` / `android:icon` | `name` / `icon` |
| Orientación | `android:screenOrientation` | `orientation` |

> 💡 Lo que en Android se reparte entre [[Gradle|build.gradle]] y [[AndroidManifest]], en Expo vive **todo en `app.json`**.

---

## Anatomía de un app.json

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

| Grupo | Campos | Detalle |
|---|---|---|
| **Identidad** | `name`, `slug`, `version` | `name`: nombre visible · `slug`: identificador del proyecto en Expo · `version`: la que ve el usuario |
| **Visual** | `icon`, `orientation` | `orientation`: `portrait`, `landscape` o `default` |
| **Plataformas** | `android.package`, `ios.bundleIdentifier` | ID único en cada store. **Una vez publicado, no se cambia** |
| **Extensiones nativas** | `plugins` | Librerías que necesitan modificar el proyecto nativo (permisos, Manifest, Info.plist) |

> ⚠️ ¿Necesitás valores **dinámicos** (por ejemplo, variables de entorno)? Usá **`app.config.js`** en lugar de `app.json`.

---

## Config plugins — app.json modifica el código nativo

Los plugins se ejecutan **al generar el proyecto nativo** (*prebuild*).

```
app.json            npx expo prebuild        android/ · ios/         Manifest · Info.plist
plugins: [ ... ] →  ejecuta cada plugin  →   carpetas nativas    →   permisos y textos
                                             generadas               aplicados
```

### CNG — Continuous Native Generation

| Idea | Detalle |
|---|---|
| **Qué es** | Las carpetas `android/` e `ios/` se **generan** a partir de `app.json` y `package.json` |
| **No se editan a mano** | Si cambia la configuración, se vuelven a generar |
| **Equivalente en Android nativo** | Lo que ahí editás directamente en `AndroidManifest.xml` |

```bash
npx expo prebuild          # genera android/ e ios/
npx expo prebuild --clean  # las borra y las regenera desde cero
```

### Ejemplo: `expo-image-picker`

Su plugin **escribe por nosotros** en el proyecto nativo:

- Configura los **textos de permiso en iOS** (`Info.plist`)
- Permite **bloquear en Android** los permisos que la app no usa

```json
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
```

> Desarrollo completo de cada opción: [[expo-image-picker#Instalación y configuración del plugin]]

---

## ¿Por qué Expo Go no aplica esta configuración?

Expo Go es **un binario precompilado, el mismo para todos los proyectos**. No se recompila con tu app: solo descarga y ejecuta tu código JavaScript. Por eso **no puede tomar tu nombre, tu ícono, tus permisos ni tus plugins**.

| Cambio | En Expo Go | ¿Rebuild? |
|---|---|---|
| Código JS / TS | ✅ Hot reload | No |
| `name` / `icon` | ❌ Se ve "Expo Go" | Sí |
| Permisos / plugins | ❌ Se ignoran | Sí |
| Librería con código nativo no incluida en Expo Go | ❌ No funciona | Sí |

> Para ver estos cambios hace falta un binario propio → [[Development Build]].

---

## Errores comunes

| ❌ Síntoma | Causa | ✅ Solución |
|---|---|---|
| Cambié el ícono/nombre en `app.json` y la app sigue igual | Estás usando Expo Go, que ignora la config nativa | Usar un [[Development Build]] |
| Cambié un plugin y no pasa nada | Los plugins solo corren en el *prebuild* | `npx expo prebuild --clean` + `npx expo run:android` |
| Edité a mano `android/` y se perdió el cambio | Con CNG esas carpetas se regeneran | Hacer el cambio en `app.json` / plugin, no en la carpeta |
| Quiero cambiar el `android.package` ya publicado | El ID es único en el store y no se puede cambiar | Definirlo bien desde el principio |

---

## Relacionado

- [[Clase 10 - Development Build y expo-image-picker]]
- [[Development Build]]
- [[expo-image-picker]]
- [[Expo]]
- [[AndroidManifest]]
- [[Gradle]]
