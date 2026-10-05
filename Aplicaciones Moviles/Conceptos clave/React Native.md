---
tags: [react-native, javascript, mobile, cross-platform, desarrollo-aplicaciones-1]
aliases: [React Native, RN, Learn once write anywhere]
---

# CC_React Native

> **"Learn once, write anywhere"** — framework de Meta para crear apps **nativas** (no híbridas) para Android, iOS y más, usando [[React]].

## La idea central

React Native está escrito en JavaScript, pero se **renderiza con código nativo real**. Los primitivos de React se mapean directamente a los componentes nativos de cada plataforma — **no es una WebView**.

| Componente React Native | Se mapea a (Android) | Se mapea a (iOS) |
|---|---|---|
| `<View>` | `android.view` | `UIView` |
| `<Text>` | `TextView` | `UILabel` |
| `<Image>` | `ImageView` | `UIImageView` |

```jsx
function Pantalla() {
    return (
        <View>
            <Text>Hola mundo 👋</Text>
        </View>
    );
}
```

> La app usa las **mismas APIs nativas** que cualquier otra app de la plataforma — el rendimiento y el look & feel son nativos, no una capa web disfrazada.

---

## El problema que resuelve

| | **Android nativo** | **Web híbrida** (Cordova, Ionic) | **React Native** |
|---|---|---|---|
| Categoría | Nativo | Híbrido | Cross-platform |
| ✓ Ventajas | Máximo rendimiento, acceso total a APIs | Un solo código, HTML + CSS + JS | Un código, nativo real, JS + React (conocido) |
| ✗ Desventajas | Solo Android, hay que saber Java/Kotlin | WebView = no nativo, rendimiento limitado | Algo más complejo que web, algunas APIs aún requieren código nativo |

> React Native ocupa el punto intermedio: **un código compartido, pero renderizado nativamente** — a diferencia de una WebView que solo simula ser nativa.

---

## RN no prescribe todo — por eso existe Expo

React Native por sí solo **no trae**:
- Sistema de routing/navegación
- Acceso simplificado a APIs de plataforma (cámara, GPS, notificaciones)
- Herramientas de build para los stores

La propia documentación oficial de React Native **recomienda [[Expo]]** para empezar cualquier app nueva — Expo agrega todo eso encima de RN.

```bash
# Recomendado por reactnative.dev:
npx create-expo-app MiApp
npx expo start
```

---

## Casos de uso reales

| Empresa | Uso de React Native |
|---|---|
| **Facebook** | App original de Meta — uno de los primeros casos de RN en producción masiva |
| **Instagram** | Pantallas de notificaciones y vistas secundarias migradas a RN |
| **Shopify** | App de merchants reescrita en RN — redujo drásticamente el tiempo de desarrollo |
| **Discord** | Usado en partes de la app móvil para compartir código entre plataformas |
| **Microsoft** | Outlook Mobile y partes de Teams usan React Native |
| **Wix** | Apostó fuerte a RN y contribuye activamente al ecosistema open source |

### ¿Cuándo NO conviene React Native?

- Apps con **gráficos 3D intensivos**
- Acceso muy **bajo nivel al hardware**
- **Rendimiento crítico extremo** (videojuegos)

En esos casos, nativo puro (Android/Kotlin o iOS/Swift) sigue siendo la mejor opción.

---

## Relación con lo visto en Android nativo

| Concepto en Android nativo (Java) | Equivalente conceptual en React Native |
|---|---|
| [[Actividad|Activity]] / [[Fragment]] | Componente funcional de React |
| [[Intents]] / [[Navigation Component]] | React Navigation / Expo Router |
| [[Retrofit - Consumo de API REST]] | `fetch()` / Axios dentro de `useEffect` |
| [[Room]] | AsyncStorage / SQLite vía librerías de RN |
| [[Hilt]] (DI) | Context API / librerías como `react-native-di` (menos común en RN) |

> El TPO tiene su segunda entrega justamente en **React Native con Expo** — este mapeo ayuda a trasladar lo aprendido en Android nativo.

---

## Relacionado

- [[Clase 07 - React Native y Expo]]
- [[React]]
- [[React Hooks]]
- [[Expo]]
- [[Aplicaciones Nativas]]
- [[Aplicaciones Hibridas]]
