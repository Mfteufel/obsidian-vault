---
tags: [react-native, expo, expo-go, eas, tooling, desarrollo-aplicaciones-1]
aliases: [Expo, Expo Go, Expo SDK, EAS, Development Build, Metro]
---

# CC_Expo

> Framework **full-stack** para [[React Native]] con servicios en la nube que ayudan a moverse más rápido en cada etapa del ciclo de vida de la app. Recomendado oficialmente por Meta.

## Las 4 piezas de Expo

| Pieza | Qué ofrece |
|---|---|
| **Expo SDK** | +100 librerías listas para producción: cámara, GPS, notificaciones push, biometría, deep links y más, sin configuración extra |
| **Developer Experience** | Expo Go para probar en el celular al instante, Expo Orbit para lanzar emuladores sin abrir Android Studio, hot reload incluido |
| **EAS** (Expo App Services) | Build, Submit, Update — compila en la nube, manda a los stores y envía actualizaciones OTA sin pasar por revisión |
| **Respaldo de Meta** | El único framework nativo oficial recomendado por Meta para React Native. 10 años de desarrollo, 3M+ usuarios, 40K+ stars en GitHub |

---

## Expo Go — el playground

**Expo Go** es una app nativa **pre-compilada** que actúa como un playground: se instala en el celular **una sola vez**, y el código JS se sube desde la máquina de desarrollo y corre dentro de esa app — sin compilar nada.

### Toda app Expo tiene 2 partes

| Parte | Qué es |
|---|---|
| **La app nativa** (el contenedor) | El binario compilado que se instala en el dispositivo. En Expo Go, este contenedor es **fijo** — lo compila Expo y lo publica en los stores. No se puede cambiar |
| **El bundle JavaScript** (el código) | Ahí vive la lógica y la UI. Durante el desarrollo se sirve en vivo desde la máquina vía **Metro** (el bundler de RN). Expo Go lo recibe escaneando el QR y lo ejecuta dentro del contenedor nativo |

### Flujo de desarrollo con Expo Go

```
npx expo start  →  QR en terminal  →  Abrís Expo Go  →  Escaneás  →  app live
```

---

## Expo Go vs Development Build

Dos herramientas distintas para **distintos momentos** del proyecto.

| | **Expo Go** | **Development Build** |
|---|---|---|
| Qué es | Playground — sin compilar | Tu propio "Expo Go" personalizado |
| Instalar Android Studio/Xcode | ❌ No hace falta | ✅ Sí, para compilar (una sola vez) |
| Escanear QR y correr al instante | ✅ | ✅ (misma experiencia de dev) |
| Hot reload | ✅ Instantáneo | ✅ |
| Librerías nativas | Solo las **incluidas en Expo Go** | **Cualquiera** |
| Agregar código nativo custom | ❌ No | ✅ Sí |
| Cambiar ícono, splash, nombre | ❌ No | ✅ Sí |

> **Regla práctica:** empezar con Expo Go mientras el proyecto solo usa librerías estándar. Migrar a Development Build en cuanto se necesite una librería nativa que Expo Go no trae, o personalizar ícono/nombre.

---

## Primer proyecto con Expo

```bash
npx create-expo-app Example
```

### Estructura de archivos generada

```
Example/
├── app/                    ← Expo Router (pantallas)
│   ├── (tabs)/
│   │   ├── index.tsx       ← tab Home
│   │   └── explore.tsx
│   └── _layout.tsx         ← layout raíz
├── components/             ← componentes reutilizables
├── assets/                 ← imágenes, fuentes
├── constants/               ← colores, config
└── package.json
```

> La carpeta `app/` usa **file-based routing** (Expo Router): cada archivo `.tsx` dentro es automáticamente una pantalla, sin configurar rutas a mano.

### Comandos clave

| Comando | Qué hace |
|---|---|
| `npx expo start` | Levanta Metro y muestra el QR para Expo Go |
| `npx expo start --android` | Abre directamente en un emulador Android |
| `npx expo install <pkg>` | Instala librerías **compatibles con la versión del SDK** del proyecto (mejor que `npm install` a secas) |
| `npx expo run:android` | Compila y corre como **Development Build** en Android |

> ⚠️ Usar `npx expo install` en vez de `npm install` para librerías del ecosistema Expo — evita incompatibilidades de versión entre el SDK y la librería.

---

## Relación con el TPO

La **segunda entrega** del TPO ("Ronda") es en **React Native con Expo (Android)**. El flujo típico:

1. `npx create-expo-app` para arrancar el proyecto
2. Desarrollar y probar con **Expo Go** mientras alcance
3. Si se necesita algo nativo específico (ej: notificaciones push avanzadas), migrar a **Development Build**
4. Usar **EAS Build** para generar el `.apk`/`.aab` final de la entrega

---

## Relacionado

- [[Clase 07 - React Native y Expo]]
- [[React Native]]
- [[React]]
- [[React Hooks]]
