---
tags: [repaso, tpo, ronda, indice, desarrollo-aplicaciones-1]
aliases: [Índice Repaso 5 y 7]
---

# 00 — Índice Repaso Puntos 5 y 7

**Objetivo:** entender mi propio código del TPO "Ronda" para poder defenderlo.
**Repo:** `Uade---Android` (rama `main`; mis ramas ya están mergeadas).

---

## Notas de repaso

| Nota | Para qué |
|---|---|
| [[Repaso Punto 5 - Publicar Artículo]] | Wizard de 5 pasos, borrador en Room, Mis publicaciones |
| [[Repaso Punto 7 - Ofertas y Negociación]] | Ofertar, contraofertar, estados, Mis ofertas, dirección |
| [[01 - Plan de estudio hasta el oral]] | Qué estudiar cada día hasta el martes 1/12 (sin fines de semana) |

Cada una tiene 6 secciones:
1. Qué hace
2. Mapa del código
3. Flujo paso a paso
4. Conceptos Java/Android
5. Preguntas de defensa
6. Dudas o cosas raras

---

## Orden de estudio sugerido

1. **Punto 5, sección 3** (flujo). Entendé el recorrido.
2. **Punto 5, sección 4**: ViewModel, LiveData y Room.
3. **Punto 7, sección 3** (flujo).
4. **Punto 7, sección 4**: Repository, Retrofit y Hilt.
5. **Las dos secciones 5** (preguntas), en voz alta.
6. **Las dos secciones 6**: saber qué está raro **antes** de que te pregunten.

---

## Los 3 hilos conductores

| Idea | Dónde se ve |
|---|---|
| **UI → interfaz Repository → implementación** | `MisPublicacionesRepository`, `OfertasRepository`, `BorradorRepository` |
| **Nada de red/BD en el Main Thread** | `Executor` + `Handler` (Room), `enqueue` (Retrofit) |
| **Proteger la UI de vistas muertas** | `if (vista == null) return;`, `onDestroyView`, `generacionConsulta` |

---

## Conceptos que tus notas SÍ cubren

| Concepto | Nota |
|---|---|
| Fragment, ciclo de vida | [[Fragment]] · [[Clase 03 - Fragments y Navigation Component]] |
| Navigation Component | [[Navigation Component]] |
| RecyclerView | [[RecyclerView]] |
| Room | [[Room]] |
| Retrofit | [[Retrofit - Consumo de API REST]] |
| Hilt / DI | [[Hilt]] · [[Inyección de Dependencias (DI)]] · [[Clase 04 - Inyección de Dependencias y Storage]] |
| ActivityResultLauncher y permisos | [[ActivityResultLauncher]] · [[Clase 06 - Biometría y Seguridad en el Dispositivo]] |
| JWT | [[JWT]] · [[Clase 05 - JWT y LocalStorage]] |
| Intents | [[Intents]] |

---

## ⚠️ Conceptos que el código usa y tus notas NO cubren

> Los expliqué dentro de cada nota de repaso. Conviene sumarles notas propias más adelante.

| Concepto | Dónde se usa |
|---|---|
| **ViewModel** | Punto 5 |
| **LiveData / MutableLiveData / observe** | Punto 5 |
| **`@EntryPoint` / `@HiltViewModel`** | Punto 5 |
| **Patrón Repository y callbacks propios** | Puntos 5 y 7 |
| **`Uri` / `ContentResolver` / permiso persistente** | Punto 5 |
| **`@Multipart` / `@Part`**, `@PATCH` | Puntos 5 y 7 |
| **`Executor` + `Handler`** (solo de pasada en Room) | Punto 5 |
| **TabLayout** | Punto 7 |
| **BottomSheetDialogFragment** | Punto 7 |
| **Fragment Result API** | Punto 7 |
| **Diálogos y componentes Material** (Snackbar, TextInputLayout) | Puntos 5 y 7 |
| **Enums, clases inmutables, lambdas, generics** (Java básico) | Puntos 5 y 7 |
| **Sub-grafos de Navigation** (`<include>`, `getBackStackEntry`) | Punto 5 |

---

## Las 5 cosas raras más importantes (para no sorprenderte)

1. **Punto 5:** la dirección es **texto libre**, no coordenadas reales ni mapa.
2. **Punto 5:** las fotos y los cambios solo se guardan al tocar **Siguiente**.
3. **Punto 5:** posible `setValue` fuera del Main Thread si falla la lectura de fotos.
4. **Punto 7:** el Javadoc dice que sin turno podés contraofertar, pero el código oculta los 3 botones.
5. **Punto 7:** la regla del 50 %–100 % del precio vive solo en la app.

> Detalle completo en la sección 6 de cada nota.

---

## Qué NO verifiqué

- No corrí la app ni los tests. Todo es **lectura de código**.
- No leí completos: `registrarOferta` (en `DetalleFragment`), `MapaUtils.abrirComoLlegar` ni el interceptor del token.
- No revisé el backend de **publicaciones** (pausar/reactivar).
- Los Fragments de Título/Descripción y Categoría/Estado los leí solo por sus llamadas a `guardarPaso`.
