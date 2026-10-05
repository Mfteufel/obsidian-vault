# Auditoría de manejo de errores — Ronda

> Alcance: todo el proyecto — backend FastAPI (`backend/`) y app Android (`app/src/main/java/com/example/tpo/`). No incluye cambios de código; es un relevamiento para priorizar arreglos.
>
> Formato de referencias: `archivo:línea`.

## Resumen ejecutivo

El manejo de errores es **sólido y consistente en la mayoría de los flujos felices y de negocio** (403/404/409 bien pensados, mensajes en español coherentes, sin fugas de stack trace, sin `catch (Exception e)` genéricos en ningún lado del proyecto). El riesgo real está concentrado en un puñado de puntos:

1. 🔴 **Tres endpoints de publicaciones sin autenticación** (`crear`, `editar`, `cambiar_estado`) permiten inyectar un `vendedorId` arbitrario que después **crashea sin control** un endpoint distinto (`operaciones.py:126`) cuando alguien califica esa venta.
2. 🔴 **Sin exception handler global en FastAPI** ni try/except en la capa de acceso a datos (`database.py`): cualquier error no previsto explícitamente por un router (IntegrityError de una carrera, conexión a disco, JSON corrupto) se propaga como un 500 en **texto plano**, rompiendo el contrato JSON que el resto de la API respeta.
3. 🔴 **Sin manejador global de excepciones en Android** combinado con operaciones de Room en background sin try/catch: es el único camino real a un crash duro documentado en el código.
4. 🟠 **Sin logout automático en 401**: el token expira a las 8 horas y el usuario queda viendo "Tu sesión venció" en cada acción, sin redirección, hasta que entra manualmente a cerrar sesión.
5. 🟠 Inconsistencias de estilo que no rompen nada hoy pero son deuda: helper `error()` de FastAPI duplicado en 4 routers, validaciones de negocio (precio > 0, longitudes) escritas a mano en vez de en los modelos Pydantic, un repositorio Android (`MisPublicacionesRepositoryApi`) que no usa el mecanismo común de errores (`ErrorApi`).

El resto del documento detalla cada hallazgo con archivo y línea.

---

## Backend (FastAPI)

### 1. Patrón de errores HTTP — inconsistente pero semánticamente correcto

No hay una función de error compartida a nivel de proyecto. Se reinventa, idéntica, en 4 routers:

```python
def error(codigo, mensaje):
    return HTTPException(status_code=codigo, detail=mensaje)
```

- `backend/routers/ofertas.py:16-17`
- `backend/routers/publicaciones.py:32-33`
- `backend/routers/preguntas.py:17-18`
- `backend/routers/operaciones.py:18-19`

Los otros 4 routers (`auth.py`, `busquedas.py`, `favoritos.py`, `usuarios.py`) llaman `HTTPException(...)` directo, con dos estilos distintos entre ellos:
- Enteros literales: `usuarios.py:50,52,56,68,76`, `favoritos.py:28,31`, `busquedas.py:34,50`.
- Constantes `status.HTTP_*`: todo `auth.py`.

Conviven tres formas de escribir lo mismo en el mismo proyecto. No es un bug — los ~52 `raise` relevados usan códigos semánticamente correctos y consistentes (400 validación, 401 sin sesión, 403 no autorizado, 404 no existe, 409 conflicto de estado, 429 rate limit en `auth.py:46`) — pero es deuda de estilo fácil de resolver moviendo `error()` a un módulo común.

**Mensajes duplicados textualmente** en vez de centralizados:
- `"La publicacion no existe"` — 8 apariciones: `favoritos.py:28,31`, `ofertas.py:77,80,115`, `preguntas.py:37,50,71`, `publicaciones.py:166,174,191`.
- `"El precio tiene que ser mayor a cero"` — `ofertas.py:73,139`, `publicaciones.py:135,181`.

### 2. 🔴 Excepciones no capturadas — el hallazgo más serio del proyecto

**Cadena completa, verificada:**

1. `backend/routers/publicaciones.py:116-152` (`POST /publicaciones`, crear), `:170-183` (`PUT /{id}`, editar) y `:186-192` (`PATCH /{id}/estado`) **no tienen `Depends(seguridad.usuario_actual)`**. El único uso de auth en todo el archivo es el opcional en `ver_detalle` (`:163`).
2. `crear()` toma `vendedorId: str = Form(...)` (`publicaciones.py:118`) tal cual lo manda el cliente — sin validar que sea numérico, sin verificar que corresponda a nadie autenticado (no hay nadie autenticado en el request).
3. Ese string arbitrario se guarda como `vendedor_id` en la tabla `publicaciones` (`database.crear_publicacion`).
4. Cuando esa publicación se vende y el comprador califica la operación, `backend/routers/operaciones.py:126` hace:
   ```python
   calificado_id = int(fila["vendedor_id"])   # sin try/except
   ```
   Si `vendedor_id` no es numérico, esto es un `ValueError` no capturado → **500 no controlado**, en vez de haberse frenado mucho antes con un 401/400 en la creación.

Verificado línea por línea: `publicaciones.py:118` no valida el tipo de `vendedorId`, y `operaciones.py:126` efectivamente no tiene try/except a su alrededor (el único try/except del bloque cubre `database.crear_calificacion`, dos líneas después).

**Otros puntos sin try/except que pueden fallar:**

| Ubicación | Riesgo |
|---|---|
| `auth.py:124-126` | `usuario = buscar_usuario_por_email(email)` seguido de `usuario["id"]` sin chequear `None`. Ventana teórica (no hay endpoint de borrado de usuarios hoy), pero sin resguardo. |
| `auth.py:142-146` (registro) | TOCTOU clásico: chequea `buscar_usuario_por_email` y luego `crear_usuario` sin transacción ni catch. `usuarios.email` es `UNIQUE` (`database.py:12`) — dos registros simultáneos con el mismo email pueden disparar `sqlite3.IntegrityError` sin capturar → 500 en vez de 409. Contrastar con `operaciones.py:129-134`, que sí protege la misma clase de carrera para calificaciones. |
| `database.py` (todo el archivo) | Cero `try/except` alrededor de cualquier `INSERT`/`UPDATE`/`DELETE` en las ~40 funciones de escritura. Toda la protección contra `IntegrityError` del proyecto vive en un solo router (`operaciones.py`), no en la capa de datos. |
| `database.py:135-138` (`conectar`) | `sqlite3.connect` sin try/except — una ruta sin permisos de escritura tira la excepción cruda hasta el endpoint. |
| `database.py:127` (`desde_texto`) | `datetime.fromisoformat(texto)` sin try/except (usado en `auth.py:42,118`). Riesgo bajo (dato propio), sin defensa si cambiara el formato. |
| `busquedas.py` (`a_json`) | `json.loads(fila["filtro"])` sin try/except — un campo `filtro` corrupto en DB tira `JSONDecodeError` no capturada. |
| `busquedas.py:36-38` (`guardar`) y `preguntas.py:56-59` (`preguntar`) | Buscan el registro recién creado en un loop y hacen `return a_json(fila)` adentro; si no se encontrara, la función devuelve `None` implícito y rompe la validación de `response_model` con un 500 de FastAPI en vez de un error de negocio. |
| `publicaciones.py:46` (`imagen.save(...)`) | Ver sección de archivos, punto 7. |

**Bien resuelto, para contraste:** `ofertas.py:76,78` y `favoritos.py:27,29` sí protegen `int(datos.publicacionId)` con un chequeo previo `.isdigit()` — es exactamente el patrón que falta en `operaciones.py:126`.

### 3. `seguridad.py` — JWT

`usuario_actual` (`seguridad.py:53-75`) y `usuario_actual_opcional` (`seguridad.py:78-91`) manejan bien el token: sin credenciales → 401; `jwt.ExpiredSignatureError` → 401 con mensaje específico; `jwt.InvalidTokenError` → 401 genérico; usuario no encontrado → mismo 401 genérico (no filtra si el problema fue el token o la cuenta).

**Punto débil verificado:** en ambas funciones, `int(carga["sub"])` queda **fuera** del bloque `try`:

```python
# seguridad.py:61-72 (usuario_actual)
try:
    carga = jwt.decode(...)
except jwt.ExpiredSignatureError: ...
except jwt.InvalidTokenError: ...

usuario = database.buscar_usuario_por_id(int(carga["sub"]))   # <- fuera del try
```

Mismo patrón en `usuario_actual_opcional:90`. Si `RONDA_JWT_SECRET` no se configura en producción, queda el default `"clave-solo-para-desarrollo"` (`seguridad.py:13`) — cualquiera puede forjar un token válido con un `sub` no numérico y provocar un 500 en lugar del 401 esperado. En `usuario_actual_opcional` es más grave todavía: ese 500 tumbaría un endpoint que se supone debe degradar con gracia a "usuario anónimo" (`ver_detalle` en `publicaciones.py:161-167`).

### 4. `main.py` — sin red de contención global

Archivo completo (37 líneas), verificado: no hay `@app.exception_handler(...)` de ningún tipo, ni `CORSMiddleware`, ni `debug=True`. Consecuencia: ante cualquier excepción no capturada por un router, Starlette responde con su `ServerErrorMiddleware` por defecto — **500 en texto plano** ("Internal Server Error"), no el JSON `{"detail": ...}` que respeta el resto de la API. Rompe el contrato de error para quien consume la API (la app Android, vía `ErrorApi`, espera JSON).

### 5. Validación de entrada — todo a mano, nada en Pydantic

Cero usos de `@validator`/`@field_validator` en `backend/models/*.py`. Toda la validación de negocio está repetida en cada router en vez de en el modelo:

- **Precio > 0**: repetido a mano en `ofertas.py:73,139` y `publicaciones.py:135,181`, en vez de `Field(gt=0)` en `models/oferta.py` / `models/publicacion.py` (ambos `float` sin restricción).
- **Categoría/estado como `str` libre**: `models/publicacion.py:11-12` en vez de `Enum`/`Literal`; la pertenencia a un conjunto válido se valida a mano contra sets (`CATEGORIAS`, `ESTADOS_ARTICULO`, `ESTADOS_PUBLICACION`, `publicaciones.py:27-29`). Además, `editar()` (PUT) ni siquiera revalida categoría/estado porque `EdicionPublicacion` no incluye esos campos — create y edit quedan inconsistentes entre sí.
- **Longitudes de texto**: cada endpoint tiene su propia constante local (`LARGO_MAXIMO_DE_MENSAJE=200` en `ofertas.py:13`, `LARGO_MAXIMO_DE_COMENTARIO=280` en `operaciones.py:14`, `LARGO_MINIMO_PREGUNTA=10` en `preguntas.py:14`, `LARGO_MINIMO_DE_CLAVE=6` en `auth.py:25`) en vez de `Field(min_length=..., max_length=...)`.
- **Buen contraejemplo, ya en el código**: `es_email_valido` está centralizada una sola vez en `models/usuario.py:6-13` y reutilizada en `auth.py` y `usuarios.py`. Es el patrón correcto — solo falta aplicarlo al resto.

### 6. Manejo de archivos (fotos) — `publicaciones.py`

`guardar_foto` (`publicaciones.py:38-47`) sí maneja bien el caso esperable:
```python
try:
    imagen = Image.open(foto.file)
    imagen = ImageOps.exif_transpose(imagen).convert("RGB")
except (UnidentifiedImageError, OSError):
    raise error(400, "Una de las fotos no es una imagen valida")
```

Pero:
- **Sin límite de tamaño en bytes** antes de leer con PIL — solo se limita la cantidad de fotos (`MAXIMO_DE_FOTOS = 6`), no el peso de cada una.
- `imagen.save(CARPETA_FOTOS / archivo, "JPEG", quality=80)` (`:46`) **no tiene try/except**: una falla de escritura a disco (permisos, disco lleno) se propaga como 500 sin capturar, y además el loop `archivos = [guardar_foto(foto) for foto in fotos]` (`:141`) no hace rollback de las fotos que ya se guardaron en disco si una posterior falla.
- Al no requerir auth (ver punto 2), cualquiera sin sesión puede subir fotos arbitrarias al servidor.

### 7. `correo.py` — decisión deliberada y bien ejecutada

`enviar_codigo` (`correo.py:30-51`) envuelve todo el envío en un único `try/except Exception`, y si falla **solo loguea por `print` y sigue** — nunca relanza. Como se invoca vía `BackgroundTasks` después de responder (`auth.py:61`), la request de "pedir código" **nunca falla aunque el mail no salga**. Es correcto como diseño (documentado en el propio código) pero implica que, si el correo está mal configurado en producción, el usuario recibe "Te enviamos un código" sin que le llegue nada y sin ninguna señal de error — solo queda en logs de `print`, sin structured logging ni alerta.

### 8. Tests — cobertura acotada a un solo router

`backend/tests/test_operaciones.py` es el **único** archivo de test del backend. Cubre bien sus propios casos de error (401 sin sesión, 404 inexistente, 403 tercero, 409 doble confirmación/doble calificación/plazo vencido, 400 rangos inválidos). **No hay ningún test** para `auth.py` (rate limit, credenciales incorrectas, cuenta sin confirmar, email duplicado, token inválido/expirado), `publicaciones.py` (incluida la falta de auth del punto 2, que un test la habría detectado), `favoritos.py`, `busquedas.py`, `preguntas.py`, ni para la carrera de `IntegrityError` bajo concurrencia real (solo se ejerce el camino secuencial).

### 9. Mensajes de error — consistentes y sin fuga de información

Todos los `detail` están en español, tono informal coherente ("vos"/"tenés"), sin mezclar idiomas. No se encontró ningún `HTTPException` que exponga stack trace o ruta de archivo. El riesgo de fuga real no está en los errores manejados sino en los **no** convertidos a `HTTPException` (puntos 2-4): con `debug=False` por defecto el 500 no expone traza, pero si algún despliegue activara modo debug, cualquiera de los casos sin capturar documentados arriba pasaría a mostrar trazas con rutas del servidor.

Buena práctica explícita, para destacar: login (`auth.py:170-174`) y verificación de OTP (`auth.py:112-121`) devuelven el **mismo mensaje** para "no existe" y "credencial incorrecta", con comentario aclarando que es a propósito — no revela qué emails tienen cuenta.

---

## App Android

### 1. `ErrorApi.java` — bien diseñado, con test dedicado

Archivo completo (69 líneas). `mensaje()`/`detalle()`/`leerDetalle()` cubren los 7 códigos relevantes sin crashear:
- 401 → siempre "Tu sesión venció..." sin mirar el body (`ErrorApi.java:33-35`).
- Body null, no-JSON (`JsonSyntaxException`, que es `RuntimeException`), o JSON que no es objeto → capturado, cae al mensaje por defecto de cada pantalla.
- **422 de FastAPI** (donde `detail` es una lista de objetos, no un string): `detalle.isJsonPrimitive()` da `false` para un array → cae al default. Es intencional, está documentado en el javadoc de la clase y **testeado explícitamente** en `ContratoJsonTest.unErrorDeValidacionUsaElMensajePorDefecto` (`app/src/test/java/com/example/tpo/data/remote/ContratoJsonTest.java:334-339`).

### 2. Callbacks de Retrofit — patrón dominante correcto, con dos excepciones puntuales

En **`PublicacionRepositoryApi`, `FavoritoRepositoryApi`, `BusquedaGuardadaRepositoryApi`, `PreguntaRepositoryApi`, `OfertasRepositoryRemoto`, `OperacionRepositoryApi`, `PerfilRepositoryApi`** el patrón es consistente: siempre `!response.isSuccessful() || body == null` antes de usar el body, siempre `ErrorApi.mensaje(response, ...)` para el mensaje, `onFailure` nunca queda vacío, y el mapeo DTO→modelo está envuelto en `try/catch(RuntimeException)` para no crashear ante un campo faltante o un enum desconocido. No se encontró ningún uso de `response.body()` sin chequeo previo en ningún repositorio.

**Excepción 1 — `MisPublicacionesRepositoryApi.java`:** verificado por grep, **nunca usa `ErrorApi`**. Sus tres métodos (`publicar` `:82`, `listar` `:113`, `cambiarEstado` `:145`) devuelven siempre un string hardcodeado sin leer `response.errorBody()`. Un rechazo del backend con `detail` específico (409 de conflicto, 400/422 de validación, o el 401 de sesión vencida) se pierde — el usuario nunca ve el motivo real ni el mensaje de "sesión vencida" que sí ven el resto de las pantallas.

**Excepción 2 — pantallas de login** (`LoginFragment.java`, `CodigoFragment.java`, `RegistroFragment.java`): sí chequean `isSuccessful()`/body null y sí implementan `onFailure`, pero **no usan `ErrorApi`** — reimplementan el manejo de códigos con `if/else` sobre `response.code()` (`LoginFragment.java:183-191,229-237`, etc.) y nunca leen `detail` del body. Cualquier `detail` específico para un código no listado se descarta a favor del genérico `login_error_servidor`. Segunda implementación paralela de lo mismo que ya resuelve `ErrorApi`.

**Mensajes de "sin conexión" con texto distinto según el repositorio** — no rompe nada, pero es inconsistente de cara al usuario:
- `ErrorApi.SIN_CONEXION` = *"No pudimos conectarnos. Revisá tu conexión e intentá de nuevo."*
- `OfertasRepositoryRemoto.SIN_CONEXION` (`OfertasRepositoryRemoto.java:31`) = *"Necesitás conexión para continuar"*
- `MisPublicacionesRepositoryApi` usa un texto ad-hoc por método (`:99,122,153`).

### 3. Excepciones locales — bien manejadas, sin catches genéricos

No se encontró **ningún** `catch (Exception e)` en todo `app/src/main/java`. Todos los try/catch son específicos: `RuntimeException` para parseo Gson, `IllegalArgumentException` para `Enum.valueOf`, `NumberFormatException` para input numérico, `SecurityException` para permisos de URI, `ActivityNotFoundException` en `MapaUtils`.

**Input numérico de usuario** — los 4 usos de `Double.parseDouble` sobre texto de un campo (ofertar, contraofertar, filtro de precio, precio de publicación) están **todos** protegidos con try/catch: `PublicarPrecioZonaFragment.java:152-153`, `FiltrosBottomSheet.java:212-213`, `DetalleFragment.java:877-878`, `DetalleOfertaBottomSheet.java:335-336`.

**Enum.valueOf() sobre datos del servidor — inconsistente entre repositorios.** Verificado por grep de todos los usos de `.valueOf(` en `data/`:

| Archivo | ¿Protegido? |
|---|---|
| `PublicacionResumenResponse.java:112` (`Enum.valueOf` genérico) | ✅ — usado dentro de `aModelo()`, que descarta la publicación entera si el enum no matchea, en vez de crashear (patrón defensivo documentado). |
| `UsuarioResponse.java:60`, `FiltroMapper.java:100,113,124` | ✅ envueltos en `try/catch(IllegalArgumentException)`. |
| `SesionLocal.java:33-37` (`Zona.valueOf`) | ✅ envuelto en try/catch. |
| **`OfertasRepositoryRemoto.java:131-132`** (`EstadoOferta.valueOf(...)`, `TurnoOferta.valueOf(...)` dentro de `haciaModelo()`) | ❌ **sin try/catch**. Si el backend devolviera un valor de `estado`/`turno` que la app todavía no conoce, `IllegalArgumentException` se propaga sin capturar dentro de `onResponse` (que corre en el hilo principal) → **crash**, a diferencia del patrón defensivo que sí se usa para publicaciones. |

Es un hallazgo puntual y de bajo riesgo práctico hoy (los valores del enum están fijos y controlados por el mismo equipo), pero rompe la consistencia del resto del código y sería el primer lugar que falla si se agrega un estado de oferta nuevo sin actualizar la app antes que el backend.

### 4. 🔴 Sin manejador de crashes global

Se buscó `Thread.setDefaultUncaughtExceptionHandler`/`UncaughtExceptionHandler` en todo `app/src/main/java` — **cero resultados**. Ni `RondaApp.java` (32 líneas, solo guarda un `Context` estático) ni `MainActivity.java` instalan un handler global. No hay Crashlytics ni herramienta equivalente en el código fuente. Cualquier excepción no capturada tumba el proceso dependiendo 100% del comportamiento por defecto de Android/logcat, sin logging propio ni pantalla de recuperación.

### 5. 🔴 Room — el camino real a un crash

`AppDatabase.java:87-95` usa `.fallbackToDestructiveMigration()` sin ninguna `Migration` explícita. Es una **decisión documentada y consciente** (el javadoc de la clase explica el historial de bumps de versión, `:22-58`) — aceptable para un proyecto sin usuarios reales, con el costo de que cualquier cambio de esquema borra los datos locales sin aviso.

Más grave: en los repositorios que despachan a un `ExecutorService` (`BorradorRepositoryLocal.java` — verificado: `guardar()` línea 49, `obtener()` línea 55-59, `borrar()` línea 64; `MisPublicacionesRepositoryLocal.java:61-78,84-91,107-108`; `PublicacionesVistas.java:48-49,58,68`), las llamadas al DAO **no están envueltas en try/catch**. Si Room lanza una excepción dentro del `Runnable` (`SQLiteFullException` por disco lleno, `SQLiteDatabaseCorruptException`, violación de constraint), queda sin capturar en un hilo de background — y combinado con la ausencia de handler global del punto 4, esto tumba la app sin ningún feedback al usuario ni recuperación. **Es el único camino real a un crash duro documentado en todo el código auditado.**

`BorradorRepositoryLocal.guardar()` y `PublicacionesVistas.registrarVista()` son además fire-and-forget por diseño (sin callback de éxito/error, documentado como intencional) — un fallo ahí es silencioso incluso si no llegara a crashear.

### 6. Mensajes al usuario — mayormente consistentes, con fallas silenciosas puntuales

Patrón dominante: Snackbar (30 apariciones en 12 archivos de `ui/`), con algunas pantallas usando un `TextView` de error dedicado (login, `CalificarBottomSheet.java:171-172`).

Fallas silenciosas identificadas:
- **`BusquedasGuardadasBottomSheet.java:111-118`**: si falla `listar()`, el `onError` solo hace `dismiss()` sin mostrar ningún mensaje — la hoja se cierra sola y el usuario no sabe por qué. Es el único caso donde un error de red no se comunica en absoluto.
- `MainActivity.sondearNovedades()` (`:139-141,150-152`), `GestionPublicacionBottomSheet.java:297-300,426-428`, `HomeFragment.java:858-860`: `onError` vacíos, pero documentados y justificados (polling silencioso, sección secundaria que no debe cortar la pantalla, lectura local que no falla). Decisiones de UX razonables, no bugs.

No se encontró ningún `onFailure` de red vacío o que solo haga `Log.e` sin tocar la UI, salvo los casos ya señalados como intencionales.

### 7. Conectividad — implementación correcta, uso inconsistente

`ConectividadUtils.hayConexion()` (27 líneas) maneja bien el caso `ConnectivityManager` null. Pero **solo se usa en 8 lugares de 4 archivos** (`HomeFragment`, `FavoritosFragment`, `DetalleFragment`, `PerfilVendedorFragment`) de las ~15 pantallas que hacen llamadas de red. En el resto (`MisPublicacionesFragment`, `MisOfertasFragment`, `HistorialFragment`, `PerfilFragment`, `BusquedasGuardadasBottomSheet`, `GestionPublicacionBottomSheet`, `CalificarBottomSheet`, `PublicarResumenFragment`) no hay chequeo preventivo — funciona igual porque `onFailure` de Retrofit cubre el caso, pero esas pantallas no pueden mostrar proactivamente "estás sin conexión" ni ofrecer una vista cacheada.

### 8. 🟠 Autenticación — sin logout automático en 401

`NetworkModule.provideOkHttp` (`:29-45`) solo **agrega** el header `Authorization` si hay token vía interceptor; no hay `Authenticator` ni lógica de refresh (no existe endpoint de refresh en `AuthApi.java`).

Verificado con grep de `clearToken()`: se llama solo en dos lugares — `LoginFragment.recuperarSesion()` (`:133-134`, logout automático, pero solo corre una vez al abrir la pantalla de login con un token guardado) y `PerfilFragment.cerrarSesion()` (`:327`, acción manual del botón "Cerrar sesión"). **Ningún repositorio ni fragment reacciona a un 401 durante el uso normal de la app** haciendo logout o redirigiendo al login.

Consecuencia práctica: el token expira a las `HORAS_DE_VALIDEZ = 8` horas (`seguridad.py:15`). Si eso ocurre mientras el usuario navega, cada acción que requiera red muestra repetidamente "Tu sesión venció..." en un Snackbar, pero la app **no lo saca de la pantalla ni borra el token viejo** — queda en un estado roto hasta que entra manualmente a Perfil y cierra sesión.

### 9. Tests — cobertura sólida pero acotada

`ContratoJsonTest.java` (365 líneas) es la prueba más relevante para este audit: valida explícitamente que 403/409/404 devuelven su `detail`, que un 409 con conflicto real llega bien, que un **422 con `detail` como lista** cae al mensaje por defecto sin crashear (`:334-339`), y que un 401 devuelve un mensaje que menciona "sesión" (`:341-345`).

No hay tests de: `onFailure`/fallas de red (ningún test simula un `Call` fallido de Retrofit), `ConectividadUtils`, lógica de `TokenManager`/logout en 401, migraciones o fallback de Room, ni de los repositorios `data/*Api.java` completos con Retrofit mockeado (los tests existentes cubren DTOs y `ErrorApi` de forma aislada).

### 10. Carga de imágenes — `ImagenRemota.java`

Archivo completo (115 líneas), bien resuelto: `OkHttpClient` propio con timeout de 10s, `try (Response respuesta = ...)` que cubre no-2xx, body null y `catch (IOException | RuntimeException)` para timeout/host inalcanzable/URL rota. En todos los casos de fallo el placeholder queda puesto, sin excepción visible ni crash, y el guard de `vista.setTag(url)` evita pintar una foto en un ViewHolder ya reciclado. Única observación menor: una imagen corrupta que sí llega por HTTP (`BitmapFactory.decodeByteArray` devuelve `null` sin lanzar) termina en el mismo placeholder que un 404 — no se distingue de cara al usuario, aceptable para una grilla de fotos.

---

## Tabla priorizada de hallazgos

| # | Severidad | Dónde | Qué pasa |
|---|---|---|---|
| 1 | 🔴 Crítico | `backend/routers/publicaciones.py:116-192` | `crear`/`editar`/`cambiar_estado` sin autenticación → `vendedorId` arbitrario → crashea sin control `operaciones.py:126` al calificar |
| 2 | 🔴 Alto | `backend/main.py` + `backend/database.py` | Sin exception handler global, sin try/except en escrituras → 500 en texto plano ante cualquier `IntegrityError` no prevista (ej. registro concurrente, `auth.py:142-146`) |
| 3 | 🔴 Alto | Android, sin archivo único | Sin `UncaughtExceptionHandler` global + operaciones Room en `Executor` sin try/catch (`BorradorRepositoryLocal`, `MisPublicacionesRepositoryLocal`, `PublicacionesVistas`) → único camino real a un crash duro |
| 4 | 🟠 Medio | `backend/seguridad.py:72,90` | `int(carga["sub"])` fuera del try/except del JWT → 500 en vez de 401 si el secreto por defecto queda en producción |
| 5 | 🟠 Medio | Android, transversal | Sin logout/reintento automático en 401 fuera del login — usuario "atascado" tras 8h de sesión |
| 6 | 🟠 Medio | `app/.../data/MisPublicacionesRepositoryApi.java:82,113,145` | No usa `ErrorApi` → pierde el `detail` real del backend y el aviso de sesión vencida |
| 7 | 🟡 Bajo-medio | `backend/models/*.py` | Sin `field_validator`/`Enum`/`Field(gt=0)` → validación de negocio duplicada a mano en 4+ routers, inconsistente entre create/edit |
| 8 | 🟡 Bajo | `app/.../data/OfertasRepositoryRemoto.java:131-132` | `EstadoOferta.valueOf`/`TurnoOferta.valueOf` sin try/catch, a diferencia del patrón defensivo usado para publicaciones |
| 9 | 🟡 Bajo | `app/.../ui/home/BusquedasGuardadasBottomSheet.java:111-118` | Falla de red cierra la hoja en silencio, sin avisar al usuario |
| 10 | 🟢 Informativo | Tests, ambos lados | Backend: un solo router testeado (`operaciones`). Android: sin tests de fallas de red, Room o logout — la cobertura existente es sólida pero acotada al parseo de errores/DTOs |

---

## Lo que ya está bien hecho (para no tocar)

- **`ErrorApi.java`** (Android) y el patrón `error()` + códigos HTTP semánticos (backend) son un buen diseño base — el problema no es el mecanismo, es que no se usa en el 100% de los lugares.
- **`OfertasRepositoryRemoto`** (Android) y **`ofertas.py`** (backend) son el ejemplo más prolijo de cada lado: callbacks factorizados con chequeo de éxito + body null + `ErrorApi`, y router con auth + chequeos de rol/estado en cada paso.
- **`auth.py`**: mismo mensaje para "no existe" y "credencial incorrecta" (no revela qué emails tienen cuenta), rate limiting con `429` + `Retry-After`, comparación de OTP con `hmac.compare_digest` (tiempo constante).
- **`correo.py`**: falla de SMTP/API nunca tumba la request que pidió el código — decisión correcta y bien documentada.
- **Parseo de input numérico** en Android: los 4 `Double.parseDouble` sobre texto de usuario están protegidos sin excepción.
- **`ImagenRemota.java`**: descarga de fotos con timeout, guard de reciclado de vista y fallback a placeholder sin crashear en ningún camino.

---

## Metodología

Relevamiento hecho contra `main` (commit de referencia: rama `main` limpia al momento de esta auditoría), combinando exploración automatizada de todo `backend/` y `app/src/main/java/` con verificación manual línea por línea de los hallazgos de mayor severidad antes de volcarlos acá. No se modificó ningún archivo de código como parte de este trabajo.
