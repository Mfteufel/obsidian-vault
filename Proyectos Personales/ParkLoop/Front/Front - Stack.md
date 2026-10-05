---
tags: [parkloop, front, stack]
---

# Front - Stack

Volver a [[Resumen]]

## Resumen rápido

| Tecnología | Para qué |
|---|---|
| **React Native** | Hacer la app móvil con un solo código |
| **Expo** | Herramientas para correr, probar y compilar la app |
| **TypeScript** | JavaScript con tipos (avisa errores antes de ejecutar) |
| **React Navigation** | Navegación entre pantallas y tabs |

**Requisito clave:** la app tiene que probarse en **iOS, Android y web**.

---

## React Native

**Qué es:** un framework de Meta para crear apps móviles con JavaScript/TypeScript y componentes tipo React.

**Cómo funciona:** escribís componentes (`<View>`, `<Text>`, `<Pressable>`). React Native los convierte en componentes **nativos** de cada sistema.

**Por qué lo elegimos:**
- Un solo código para iOS y Android.
- Marco ya lo usó en otro proyecto (Bidly).
- Mismo lenguaje que el backend.

**Alternativa descartada:** Flutter. Obliga a aprender Dart.

---

## Expo

**Qué es:** una capa sobre React Native que simplifica todo.

**Qué nos da:**
- **Expo Go**: probar la app en el celular escaneando un QR.
- **Web**: `npx expo start --web` corre la app en el navegador.
- **Librerías listas**: cámara, ubicación, notificaciones.
- **Expo Router** o React Navigation para las pantallas.

**Comandos base:**
```bash
npx create-expo-app@latest mobile --template
npx expo start          # arranca el servidor de desarrollo
npx expo start --web    # abre la versión web
```

---

## TypeScript

**Qué es:** JavaScript + tipos.

**Por qué importa acá:**
- Los tipos (`Usuario`, `Vehiculo`, `Reserva`) se escriben **una sola vez** en `packages/shared`.
- La app y el backend usan **los mismos tipos**.
- Si el backend cambia un campo, la app avisa el error al compilar.

Ver [[Back - Arquitectura]] para la estructura del monorepo.

---

## Librerías previstas

| Necesidad | Librería sugerida | Nota |
|---|---|---|
| Cámara + lectura de código | `expo-camera` | Soporta PDF417. Ver [[Front - Escaneo DNI y licencia]] |
| Mapa interactivo | `react-native-maps` | Ver [[Front - Reservas y mapa]] |
| Selectores tipo scroll | `@react-native-picker/picker` o `react-native-wheel-picker` | Para marca, modelo, hora y duración |
| Llamadas a la API | `fetch` o `axios` + React Query | Ver [[Back - Reintentos e idempotencia]] |
| Navegación | React Navigation (bottom tabs) | 5 tabs. Ver [[Front - Pantallas]] |

> [!warning] Mapa en web
> `react-native-maps` **no funciona en web**. Para la versión web hace falta una alternativa (ej: Leaflet) o mostrar un aviso. Decidir al implementar.

---

## Reglas para el front

- **Nunca calcular dinero en la app.** El costo estimado viene del backend. La app solo lo muestra.
- **Plata en centavos** (enteros). Se formatea a pesos solo para mostrar.
- **Mostrar siempre la antigüedad del dato** de disponibilidad (ver [[RN - Reservas, no-show y cancelación]]).
- En esta etapa, los botones tienen que ser **interactivos aunque no procesen datos reales**.
