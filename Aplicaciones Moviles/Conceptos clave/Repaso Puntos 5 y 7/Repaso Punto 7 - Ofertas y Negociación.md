---
tags: [repaso, tpo, ronda, punto-7, android, java, desarrollo-aplicaciones-1]
aliases: [Repaso Punto 7, Ofertas y Negociación, Mis ofertas]
---

# Repaso Punto 7 — Ofertas y Negociación

> Volver al [[00 - Índice Repaso 5 y 7]]

**Código leído:** rama `main` (`feature/ofertas-negociacion` y `feature/conectar-backend-publicar-ofertas` ya están mergeadas).
**Contrato real:** `docs/ofertas-api.md` (en el repo). Es la fuente de verdad.

---

## 1. Qué hace la funcionalidad

- El interesado **ofrece un precio** (con mensaje opcional de hasta 200 caracteres).
- Quien tiene el turno puede **aceptar, rechazar o contraofertar**.
- Cada oferta tiene **estado** y **vence sola a las 6 horas**.
- **"Mis ofertas"** tiene dos pestañas: **Enviadas** y **Recibidas**.
- La **dirección de entrega** solo aparece si la oferta está **ACEPTADA**.

| Estado | Significado |
|---|---|
| `PENDIENTE` | Espera respuesta. Única donde se puede actuar |
| `ACEPTADA` | Se cerró la venta. Se ve la dirección |
| `RECHAZADA` | Terminal |
| `VENCIDA` | Pasó `venceEn` sin respuesta |

| Turno | Quién responde |
|---|---|
| `VENDEDOR` | Arranca así (el comprador acaba de ofertar) |
| `COMPRADOR` | Después de una contraoferta del vendedor |

---

## 2. Mapa del código

Ruta base: `app/src/main/java/com/example/tpo/`

### App Android

| Archivo | Rol |
|---|---|
| `data/OfertasRepository` | **Interfaz**: `crear`, `enviadas`, `recibidas`, `obtener`, `aceptar`, `rechazar`, `contraofertar` |
| `data/OfertasRepositoryRemoto` | Implementación con Retrofit. **La única** |
| `data/remote/OfertasApi` | Interfaz Retrofit con los 6 endpoints |
| `data/remote/dto/OfertaNuevaRequest` | Body del `POST` (publicacionId, precio, mensaje) |
| `data/remote/dto/CambioEstadoOfertaRequest` | Body del `PATCH` de estado |
| `data/remote/dto/CambioPrecioOfertaRequest` | Body del `PATCH` de precio |
| `data/remote/dto/OfertaResponse` | Lo que devuelve la API (todo `String`/números) |
| `model/OfertaNegociacion` | Modelo **inmutable** de dominio |
| `model/EstadoOferta`, `model/TurnoOferta` | Enums |
| `ui/misofertas/MisOfertasFragment` | Pantalla con las 2 pestañas |
| `ui/misofertas/OfertaAdapter` | Fila de la lista |
| `ui/misofertas/DetalleOfertaBottomSheet` | Hoja inferior con detalle y botones |
| `di/RepositoryModule` | Hilt: provee `OfertasApi` y `OfertasRepository` |
| `ui/detalle/DetalleFragment` | **Ofertar** (diálogo). Es del Punto 4, no tuyo |
| `res/layout/dialogo_oferta.xml`, `dialogo_contraoferta.xml` | Diálogos |

### Backend (solo para validar las reglas)

| Archivo | Rol |
|---|---|
| `backend/routers/ofertas.py` | Endpoints y reglas |
| `backend/models/oferta.py` | Modelos de entrada/salida |
| `backend/database.py` | `vencer_ofertas()`, `aceptar_oferta(...)`, `contraofertar(...)` |

### Endpoints

| Acción | Método y ruta |
|---|---|
| Crear | `POST /ofertas` |
| Enviadas | `GET /ofertas/enviadas` |
| Recibidas | `GET /ofertas/recibidas` |
| Ver una | `GET /ofertas/{id}` |
| Aceptar / Rechazar | `PATCH /ofertas/{id}/estado` con `{"estado": "ACEPTADA"}` o `"RECHAZADA"` |
| Contraofertar | `PATCH /ofertas/{id}/precio` con `{"precio": 40000}` |

---

## 3. Flujo paso a paso

### A) Ofertar (desde el Detalle)

1. En el Detalle, tocás **Ofertar**.
2. Si no hay conexión → Snackbar y se corta.
3. Se abre el diálogo (`dialogo_oferta`): precio + mensaje opcional.
4. Validaciones **en la app**:
   - No vacío.
   - Número válido y mayor a 0.
   - **Mínimo 50 %** del precio publicado (`PROPORCION_MINIMA_OFERTA = 0.5`).
   - **No más** que el precio publicado.
5. Se llama a `ofertasRepository.crear(...)` (vía `registrarOferta`; **no leí esa parte completa**).
6. `OfertasApi.crear` hace `POST /ofertas`.
7. El backend revisa:
   - Precio > 0.
   - Publicación existe.
   - **No es tu propia publicación** (403).
   - **Está ACTIVA** (409).
   - **No tenés otra PENDIENTE** en esa publicación (409).
8. Se crea con `turno = VENDEDOR` y `venceEn = ahora + 6 h`.

### B) Ver "Mis ofertas"

1. Home → `action_home_to_misOfertas`.
2. `MisOfertasFragment` arranca en la pestaña **Enviadas**.
3. `cargarTabActual()`:
   - Incrementa `generacionConsulta`.
   - Muestra el progreso.
   - Llama a `repositorio.enviadas(...)` o `recibidas(...)`.
4. El backend corre `vencer_ofertas()` y devuelve la lista.
5. El callback **ignora** el resultado si la vista murió o si la generación cambió.
6. Se muestra la lista o el estado vacío.

**Cuándo se recarga ("siempre actualizadas"):**

| Disparador | Dónde |
|---|---|
| Cambiar de pestaña | `onTabSelected` |
| Volver a la pantalla | `onResume` |
| Botón "Recargar" del menú | `menuRecargarOfertas` |
| Después de aceptar / rechazar / contraofertar | `setFragmentResultListener` |

### C) Abrir el detalle de una oferta

1. Tocás una fila → `onOfertaClick`.
2. Se abre `DetalleOfertaBottomSheet.nuevaInstancia(id)`.
3. El id viaja en un **`Bundle`** (`setArguments`) y se lee en `onCreate`.
4. `cargarOferta()` → `repositorio.obtener(id)` → `GET /ofertas/{id}`.
5. `mostrarOferta` pinta título, monto, estado, mensaje y vencimiento.
6. Llama a `configurarBotonesSegunRolYEstado` y a `mostrarPuntoEntrega`.

### D) Aceptar, rechazar o contraofertar

1. `meTocaResponder(usuarioId)` decide si se ven los botones:

```java
public boolean meTocaResponder(String usuarioId) {
    if (estado != EstadoOferta.PENDIENTE) return false;   // solo PENDIENTE
    boolean soyComprador = compradorId.equals(usuarioId);
    boolean soyVendedor  = vendedorId.equals(usuarioId);
    return (soyComprador && turno == TurnoOferta.COMPRADOR)
        || (soyVendedor  && turno == TurnoOferta.VENDEDOR);
}
```

2. **Aceptar / Rechazar** → `PATCH /ofertas/{id}/estado`.
3. **Contraofertar** → diálogo con un precio → `PATCH /ofertas/{id}/precio`.
4. El backend valida **otra vez** (`oferta_para_responder`):
   - No sos parte → **403** "Esta oferta no es tuya".
   - No está PENDIENTE → **409**.
   - No es tu turno → **403**.
5. Efectos en el backend:

| Acción | Resultado |
|---|---|
| Aceptar | Oferta `ACEPTADA` · la publicación pasa a `VENDIDA` · las demás PENDIENTES de esa publicación pasan a `RECHAZADA` |
| Rechazar | Oferta `RECHAZADA` |
| Contraofertar | Cambia el precio · **pasa el turno al otro** · **renueva `venceEn`** (otras 6 h) |

6. Si sale bien → `notificarResultadoYCerrar(...)`: manda un resultado con `setFragmentResult` y hace `dismiss()`.
7. `MisOfertasFragment` recibe el resultado, muestra Snackbar y recarga.

### E) Vencimiento automático

- **No hay cron ni proceso en segundo plano.**
- Cada vez que se **lee o responde** una oferta, el backend ejecuta:

```sql
UPDATE ofertas SET estado = 'VENCIDA'
WHERE estado = 'PENDIENTE' AND vence_en < ahora
```

- La app solo **muestra** el tiempo restante (`FormatoUtils.tiempoRestante`).

### F) Dirección de entrega (solo si es ACEPTADA)

- **Backend:** devuelve `direccionEntrega` solo si `estado == "ACEPTADA"`. Si no, `null`.
- **App:** `mostrarPuntoEntrega` muestra el bloque solo si `estado == ACEPTADA`.
  - Con dirección → texto + botón **"Cómo llegar"** (`MapaUtils.abrirComoLlegar`).
  - Sin dirección → aviso "sin dirección".
- **Doble protección:** el dato ni siquiera llega a la app si no corresponde.

---

## 4. Conceptos de Java/Android usados

> **⚠️ No hay nota tuya** = el concepto no está en tus apuntes. Lo expliqué acá y no inventé links.

### Patrón Repository con interfaz
- **Idea:** la UI depende de `OfertasRepository` (interfaz), no de Retrofit.
- **Dónde:** `OfertasRepository` → `OfertasRepositoryRemoto`.
- **Nota:** [[Inyección de Dependencias (DI)]] (depender de abstracciones).
- ⚠️ **El patrón Repository como tal no está en tus notas.**

### Retrofit: @GET, @POST, @PATCH, @Path, @Body, enqueue
- **Qué es:** declarás los endpoints en una interfaz y Retrofit arma el cliente.
- **Dónde:** `OfertasApi`.

```java
@PATCH("ofertas/{id}/estado")
Call<OfertaResponse> cambiarEstado(@Path("id") String id,
                                   @Body CambioEstadoOfertaRequest request);
```

- **`enqueue`:** corre en otro hilo y responde en el Main Thread.
- **Nota:** [[Retrofit - Consumo de API REST]]
- ⚠️ **`@PATCH`** no aparece en tu nota (sí `@Path` y `@Body`). Funciona igual que `@POST`.

### Callback de Retrofit: onResponse / onFailure
- **`onResponse`** = llegó una respuesta (puede ser un error HTTP: 403, 409...).
- **`onFailure`** = no hubo respuesta (sin red, timeout).
- **Acá:** `onFailure` → mensaje "Necesitás conexión para continuar". HTTP error → `ErrorApi.mensaje(...)` (lee el `detail` del servidor).
- **Dónde:** `OfertasRepositoryRemoto.callbackDeUna` y `callbackDeVarias`.
- **Nota:** [[Retrofit - Consumo de API REST]]

### DTO vs modelo de dominio
- **DTO** (`OfertaResponse`): la forma del JSON. Strings, sin lógica.
- **Modelo** (`OfertaNegociacion`): la forma que usa la app. Enums, inmutable, con lógica (`meTocaResponder`).
- **Mapeo:** `haciaModelo(...)` convierte uno en otro.
- **Gson** hace JSON ↔ clase por nombre de campo.
- **Nota:** [[Retrofit - Consumo de API REST]] (sección "Modelos: cómo Gson parsea el JSON").

### Enum con recurso asociado
- **`EstadoOferta`** lleva un `@StringRes` (el texto a mostrar) y su constructor.
- **`EstadoOferta.valueOf("PENDIENTE")`** convierte el String del servidor en enum.
- **Dónde:** `model/EstadoOferta`, `OfertasRepositoryRemoto.haciaModelo`.
- ⚠️ **No hay nota tuya de enums.**

### Clase inmutable
- **Campos `final`**, sin setters, todo por constructor.
- **Por qué:** una oferta recibida es un "hecho"; para cambiarla se pide otra al servidor.
- **Dónde:** `OfertaNegociacion`.
- ⚠️ **No hay nota tuya.**

### Hilt: @AndroidEntryPoint, @Inject, @Module, @Provides, @Singleton
- **Acá:**
  - `MisOfertasFragment` y `DetalleOfertaBottomSheet` tienen `@AndroidEntryPoint`.
  - Reciben el repositorio con `@Inject OfertasRepository repositorio;`.
  - `RepositoryModule` dice cómo crearlo.
- **Dónde:** `RepositoryModule.provideOfertasRepository` y `provideOfertasApi`.

```java
@Provides @Singleton
public OfertasRepository provideOfertasRepository(OfertasApi api) {
    return new OfertasRepositoryRemoto(api);   // un solo lugar para cambiar la fuente
}
```

- **Nota:** [[Hilt]] · [[Inyección de Dependencias (DI)]] · [[Clase 04 - Inyección de Dependencias y Storage]]

### Fragment y ciclo de vida (`onResume`, `onDestroyView`)
- **`onResume`:** cada vez que la pantalla vuelve a primer plano. Acá recarga las ofertas.
- **`onDestroyView`:** se ponen las vistas en `null`.
- **Chequeo `if (listaMisOfertas == null) return;`** = "la vista ya no existe".
- **Dónde:** `MisOfertasFragment`.
- **Notas:** [[Fragment]] · [[Actividad]] (ciclo de vida) · [[Clase 03 - Fragments y Navigation Component]]

### Protección contra respuestas viejas (`generacionConsulta`)
- **Problema:** cambiás de pestaña rápido y llega primero la respuesta vieja.
- **Solución:** un contador. Cada consulta guarda su número. Si al llegar el número cambió → se descarta.
- **Dónde:** `MisOfertasFragment.cargarTabActual`.
- ⚠️ **Es una idea propia del proyecto, no está en tus notas.**

### TabLayout
- **Qué es:** pestañas de Material.
- **Acá:** 2 pestañas sobre **el mismo** `RecyclerView`. No usa `ViewPager2`.
- **`OnTabSelectedListener`** → al seleccionar, recarga.
- **Dónde:** `MisOfertasFragment`.
- ⚠️ **No hay nota tuya de TabLayout.**

### RecyclerView + Adapter + interfaz de click
- **Un solo Adapter** para las 2 pestañas. Cambia solo el texto "A" / "De" (`soyComprador`).
- **Click:** el Adapter expone `OnOfertaClickListener`; el Fragment lo implementa.
- **Dónde:** `OfertaAdapter`, `MisOfertasFragment implements OfertaAdapter.OnOfertaClickListener`.
- **Nota:** [[RecyclerView]]

### BottomSheetDialogFragment
- **Qué es:** un `DialogFragment` que sale desde abajo.
- **Patrón de creación:** `nuevaInstancia(id)` con un `Bundle` en `setArguments` (no un constructor con parámetros).
- **Por qué `Bundle`:** Android recrea el Fragment con el constructor vacío y le devuelve los argumentos.
- **Dónde:** `DetalleOfertaBottomSheet`.
- **Relacionado:** [[Fragment]] (argumentos con `Bundle`) · [[Navigation Component]] (Paso 4: pasar datos con `Bundle`).
- ⚠️ **No hay nota tuya de `BottomSheetDialogFragment` ni de `DialogFragment`.**

### Fragment Result API (`setFragmentResult`)
- **Qué es:** forma de que un Fragment le **devuelva un resultado** a otro.
- **Acá:** la hoja manda `RESULTADO_OFERTA` (con el id del mensaje). `MisOfertasFragment` lo escucha, muestra Snackbar y recarga.
- **Dónde:** `DetalleOfertaBottomSheet.notificarResultadoYCerrar`, `MisOfertasFragment.onViewCreated`.
- ⚠️ **No hay nota tuya.**

### Diálogos de Material (`MaterialAlertDialogBuilder`)
- **Acá:** diálogo con vista propia (`setView`) para el precio.
- **Truco:** el botón positivo se configura en `setOnShowListener` para **validar sin cerrar** el diálogo si hay error.
- **Dónde:** `DetalleFragment.armarYMostrarDialogoOferta`, `DetalleOfertaBottomSheet.mostrarDialogoContraoferta`.
- ⚠️ **No hay nota tuya.**

### Singleton `SesionUsuario`
- **Qué es:** guarda el id del usuario logueado en memoria. `getInstancia()`.
- **Acá:** compara contra `compradorId` / `vendedorId` para saber tu rol.
- **Dónde:** `OfertaAdapter`, `DetalleOfertaBottomSheet`.
- **Nota:** [[Clase 04 - Inyección de Dependencias y Storage]] (singleton manual) · [[JWT]] (el backend identifica por token).

### JWT / Bearer token
- **Qué es:** el backend sabe quién sos por el token del header `Authorization`.
- **Acá:** todos los endpoints piden Bearer. **El usuario nunca viaja en el body.**
- **Dónde:** `docs/ofertas-api.md`; `backend/routers/ofertas.py` (`Depends(seguridad.usuario_actual)`).
- **Nota:** [[JWT]] · [[Clase 05 - JWT y LocalStorage]]
- ⚠️ **No leí el `Interceptor` que agrega el header en la app.** Está en otro punto (autenticación).

### Intent implícito (Cómo llegar)
- **Qué es:** pedirle al sistema que abra otra app para una acción.
- **Dónde:** `MapaUtils.abrirComoLlegar` (prueba varias URIs y devuelve `false` si ninguna funciona).
- **Nota:** [[Intents]]
- ⚠️ **Lo leí solo de pasada.** Repasá el código antes de explicarlo.

### Lambdas y generics
- `v -> aceptar()`, `d -> dialogo.getButton(...)`.
- `RepositorioCallback<List<OfertaNegociacion>>`.
- ⚠️ **No hay nota tuya.**

---

## 5. Preguntas probables de defensa

**¿Quién tiene el turno y cómo lo sabe la app?**
El servidor manda `turno`. La app lo compara con tu rol en `meTocaResponder`. El backend valida igual (403 si no es tu turno).

**¿Por qué validar en la app y en el backend?**
La app evita errores de uso y ahorra requests. El backend es la autoridad: no se puede confiar en el cliente.

**¿Cómo vence una oferta sin un cron?**
En cada lectura o respuesta, el backend marca como `VENCIDA` las PENDIENTES con `vence_en` vencido. Es vencimiento "perezoso".

**¿Cuánto dura una oferta?**
6 horas. Se renueva en cada contraoferta.

**¿Qué pasa al aceptar?**
La oferta queda ACEPTADA, la publicación pasa a VENDIDA y las otras ofertas PENDIENTES de esa publicación quedan RECHAZADAS.

**¿Por qué la dirección solo se ve si está aceptada?**
Es dato sensible. El backend solo la devuelve si `estado == ACEPTADA`. La app además oculta el bloque en cualquier otro estado.

**¿Cómo se mantienen "siempre actualizadas"?**
Se recarga al cambiar de pestaña, en `onResume`, con el botón Recargar y después de cada acción. **No es tiempo real** (no hay push ni polling).

**¿Por qué una sola pestaña con un solo RecyclerView?**
Las dos listas tienen la misma fila. Cambia solo la llamada (`enviadas` / `recibidas`) y el texto "A" / "De".

**¿Qué es `generacionConsulta`?**
Un contador para descartar respuestas viejas si el usuario cambió de pestaña antes de que llegaran.

**¿Cómo cambiarías a otra fuente de datos?**
Se cambia el `@Provides` de `RepositoryModule`. Los Fragments solo conocen la interfaz.

**¿Por qué `PATCH` y no `POST /aceptar`?**
La URL nombra el recurso (la oferta) y el verbo HTTP dice qué hacés. No hay verbos en la URL. (Lo dice el comentario de `OfertasApi`.)

**¿Diferencia entre `onResponse` con error y `onFailure`?**
`onResponse` con código de error = el servidor contestó (403, 409). `onFailure` = no hubo respuesta (sin conexión).

**¿Qué pasa si hay dos ofertas a la vez sobre la misma publicación?**
Cada comprador puede tener **una sola PENDIENTE** por publicación (409). Si el vendedor acepta una, las otras pasan a RECHAZADA.

**¿Por qué una hoja inferior y no un Fragment nuevo?**
Es un detalle corto con acciones. No necesita su propia entrada en el nav graph.

---

## 6. Dudas o cosas raras del código

> Lo **vi leyendo**. No lo probé corriendo la app.

### Contradicciones entre comentario y código

- **Botones cuando no te toca.**
  - El Javadoc de `DetalleOfertaBottomSheet` dice: si sos parte pero no te toca, ves **solo "Contraofertar"**.
  - El código oculta los **tres** botones si `meTocaResponder` es falso.
  - **Hoy no podés insistir con otro precio mientras esperás.**
- **Tu nota "Punto 7 posible coneccion" no coincide con lo implementado.**

| Punto | Nota | Código real |
|---|---|---|
| Vigencia | 48 h | **6 h** |
| Aceptar / rechazar | `POST .../aceptar` | `PATCH .../estado` |
| Contraoferta | `POST .../contraoferta` | `PATCH .../precio` |
| Mock | `OfertasRepositoryMock` | **No existe**, solo `Remoto` |
| Pull-to-refresh | Pedido | **No está** |

### Posibles faltantes frente al enunciado

- **Sin pull-to-refresh.** El Javadoc dice que `SwipeRefreshLayout` "no se vio en la materia". Se resolvió con `onResume` + menú "Recargar".
- **Sin mensaje en contraoferta.** El contrato lo permite solo en la oferta original. Es coherente, pero el enunciado habla de "mensaje breve" solo para el interesado.

### Validaciones que viven solo en la app

- **Regla del 50 % / 100 %.** Solo en `DetalleFragment`. El backend solo exige precio > 0 (`ofertar` y `contraofertar`).
  - Con otro cliente, se puede ofertar por cualquier monto.
- **La contraoferta no tiene tope.** El diálogo solo pide número mayor a 0 (podrías contraofertar por más que el precio publicado).

### Posibles bugs / fragilidades

- **`EstadoOferta.valueOf(...)` y `TurnoOferta.valueOf(...)`** en `haciaModelo`.
  - Si el backend manda un valor nuevo, lanza `IllegalArgumentException`.
  - No hay try/catch. Compará con `BorradorRepositoryLocal`, que sí lo maneja.
- **Aceptar / rechazar / contraofertar no chequean conexión antes.** Solo "Ofertar" usa `ConectividadUtils.hayConexion`. En la hoja, la falta de red cae en `onFailure` con el mensaje "Necesitás conexión...". Funciona, pero es otro camino.
- **Doble toque.** No vi que los botones se deshabiliten mientras la request está en vuelo. Un doble toque podría mandar dos requests (el backend respondería 409). **No lo probé.**
- **Solo `crear` usa `registrarOferta`** en `DetalleFragment` y **no la leí completa**.

### Cosas a confirmar con tu equipo

- **¿Aceptar una oferta debe marcar la publicación VENDIDA?** Ya lo hace el backend. Tu nota "posible coneccion" decía "preguntame antes de decidir". Te conviene tener clara la decisión.
- **Dirección sin cargar.** Si el vendedor no cargó dirección, la oferta aceptada muestra "sin dirección". En el Punto 5 la dirección es obligatoria para publicar, así que debería ser raro.
