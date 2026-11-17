---
tags: [react-native, expo, development-build, expo-dev-client, gradle, desarrollo-aplicaciones-1]
aliases: [Development Build, Dev Build, expo-dev-client, expo run:android, Recompilar]
---

# CC_Development Build

> Es **tu propia versión de Expo Go**: incluye las librerías nativas y la configuración de tu proyecto. Es la opción **recomendada por Expo** para desarrollar una app que se va a publicar.

## Expo Go vs Development Build

| | **Expo Go** | **Development Build** |
|---|---|---|
| **Binario** | Genérico, se baja del store | **Propio**: tu nombre, ícono y package |
| **Librerías nativas** | Solo las incluidas en Expo Go | **Cualquiera** |
| **`app.json` (plugins, permisos)** | Se ignora | **Se aplica** ✅ |
| **Hot reload de JS** | ✅ | ✅ |
| **Librería clave** | — | `expo-dev-client` |

> Por qué Expo Go no alcanza (y qué cambios ignora): [[app.json y Config Plugins#¿Por qué Expo Go no aplica esta configuración?]]. Comparación general entre ambos: [[Expo#Expo Go vs Development Build]].

---

## Crear un Development Build en local

```bash
# 1. Instalar el cliente de desarrollo
npx expo install expo-dev-client

# 2. Compilar e instalar en el emulador
npx expo run:android

# En un dispositivo físico (USB + depuración)
npx expo run:android --device
```

### Requisitos

| Requisito | Detalle |
|---|---|
| **Android Studio + emulador** | Los mismos que se usan en Android nativo — ver [[Android Studio]] |
| **Cuenta de Expo** | ❌ **No hace falta** para compilar en local |
| **Dispositivo físico** | Cable USB + depuración USB activada (ver [[scrcpy]] para verlo en la compu) |

### ¿Qué hace `npx expo run:android`?

```
1. Prebuild   → genera la carpeta android/   (ver CNG)
2. Gradle     → compila el proyecto
3. Instala    → la app en el emulador / dispositivo
4. Servidor   → levanta el servidor de desarrollo (Metro)
```

> El paso 1 es [[app.json y Config Plugins#Config plugins — app.json modifica el código nativo|el prebuild]] y el paso 2 es el mismo [[Gradle]] de Android nativo.

---

## ¿Cuándo hay que recompilar?

### ✅ Solo cambiaste código JS / TS

Pantallas, estilos, lógica, llamadas con Axios… **No hace falta recompilar.**

```bash
npx expo start
```

La app instalada se conecta al servidor y los cambios se ven con **hot reload**.

### ⚠️ Cambiaste algo nativo

| Qué cambió | Por qué |
|---|---|
| Instalaste o actualizaste una **librería con código nativo** | El código nativo va dentro del binario |
| Cambiaste **`app.json`** | Nombre, ícono, permisos y plugins se aplican en el prebuild |
| Actualizaste el **SDK de Expo** | Cambia el código nativo base |

```bash
npx expo prebuild --clean
npx expo run:android
```

Regenera las carpetas nativas y vuelve a compilar e instalar la app.

### Resumen rápido

| Cambié... | Comando |
|---|---|
| Solo JS / TS | `npx expo start` (hot reload) |
| Una librería nativa, `app.json` o el SDK | `npx expo prebuild --clean` + `npx expo run:android` |

---

## Flujo de trabajo recomendado

```
1. Desarrollo diario  →  Dev Build instalado + npx expo start (hot reload)
2. Agrego librería nativa / toco app.json  →  recompilo (prebuild --clean + run:android)
3. Vuelvo al punto 1
```

> Ejemplo de la clase: al instalar [[expo-image-picker]] y configurar su plugin → **hay que recompilar**.

---

## Errores comunes

| ❌ Síntoma | Causa | ✅ Solución |
|---|---|---|
| Instalé una librería y crashea / "native module not found" | Falta recompilar: el binario viejo no tiene el código nativo | `npx expo run:android` |
| Cambié permisos en `app.json` y no se reflejan | No se regeneró la carpeta nativa | `npx expo prebuild --clean` y recompilar |
| Recompilo por cada cambio de pantalla | Confundir cambios JS con cambios nativos | Para JS alcanza `npx expo start` |
| `run:android` no encuentra dispositivo | Emulador apagado o depuración USB desactivada | Abrir el emulador / activar depuración |

---

## Relacionado

- [[Clase 10 - Development Build y expo-image-picker]]
- [[app.json y Config Plugins]]
- [[expo-image-picker]]
- [[Expo]]
- [[Gradle]]
- [[Android Studio]]
- [[scrcpy]]
