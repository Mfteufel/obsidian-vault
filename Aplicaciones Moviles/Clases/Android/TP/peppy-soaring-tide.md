# Plan: cubrir los hallazgos Crítico + Alto de la auditoría de manejo de errores

## Context

La auditoría (`docs/auditoria-manejo-errores.md`) encontró 10 problemas de manejo de errores en todo el proyecto. El usuario decidió el alcance de este plan: **los 5 hallazgos 🔴 Crítico + 🟠 Alto**, sin importar de qué "punto" del TPO sea el archivo (el riesgo real —crashes, 500 sin control, datos manipulables— no respeta la propiedad de un punto), más **un test automatizado por cada arreglo**. Los 5 restantes (limpieza de estilo, validadores Pydantic, tests adicionales de cobertura general) quedan fuera, documentados en la auditoría para más adelante.

Los 5 hallazgos a cerrar:

1. 🔴 `publicaciones.py`: `crear`/`editar`/`cambiar_estado` sin autenticación → permite un `vendedorId` arbitrario que después crashea `operaciones.py:126` sin control.
2. 🔴 Backend sin exception handler global (`main.py`) ni try/except en escrituras (`database.py`) → 500 en texto plano ante cualquier error no previsto.
3. 🔴 Android sin `UncaughtExceptionHandler` global + operaciones de Room en `Executor` sin try/catch → único camino real a un crash duro.
4. 🟠 `seguridad.py`: `int(carga["sub"])` fuera del try/except del JWT → 500 en vez de 401 ante un token malformado.
5. 🟠 Android sin logout automático en 401 → el usuario queda "atascado" viendo "Tu sesión venció" tras 8hs sin que la app lo saque de la pantalla.

Cada fix fue diseñado leyendo el código actual completo (no se adivina nada); abajo está el diff conceptual exacto por archivo.

---

## Backend

### Hallazgo 1 — Auth y dueño en `publicaciones.py`

**Por qué es seguro sin tocar Android:** todo llamado autenticado desde la app ya manda el header `Authorization: Bearer` (interceptor único en `NetworkModule.java`). El backend simplemente no lo estaba pidiendo. Cero cambios necesarios del lado de Android para este fix.

En `backend/routers/publicaciones.py`:

- Agregar un helper local, mismo patrón que `operacion_propia` en `operaciones.py:64-70`:
  ```python
  def publicacion_propia(publicacion_id, usuario):
      fila = database.buscar_publicacion(publicacion_id)
      if fila is None:
          raise error(404, "La publicacion no existe")
      if str(usuario["id"]) != fila["vendedor_id"]:
          raise error(403, "Esta publicacion no es tuya")
      return fila
  ```
- `crear()` (`:116-152`): agregar `usuario: dict = Depends(seguridad.usuario_actual)`. Dejar el parámetro `vendedorId: str = Form(...)` para no romper el contrato de multipart que ya manda Android, pero **ignorarlo** y usar `str(usuario["id"])` al llamar `database.crear_publicacion(...)`. El dato del cliente deja de ser confiable; la identidad sale del JWT.
- `editar()` (`:170-183`): agregar `usuario: dict = Depends(seguridad.usuario_actual)`, reemplazar el `fila = database.buscar_publicacion(publicacion_id); if fila is None: raise error(404, ...)` inicial por `fila = publicacion_propia(publicacion_id, usuario)`.
- `cambiar_estado()` (`:186-192`): agregar `usuario: dict = Depends(seguridad.usuario_actual)`, llamar `publicacion_propia(publicacion_id, usuario)` antes de `database.cambiar_estado_publicacion(...)`.

> Nota fuera de alcance: `GET /publicaciones/mias` (`:156-158`) tiene el mismo problema (toma `vendedorId` del query sin auth) pero no estaba nombrado en el hallazgo #1 de la auditoría de errores — se deja para la próxima pasada junto con el resto de los hallazgos bajos.

### Hallazgo 2 — Exception handler global

En `backend/main.py`, después de `app = FastAPI(...)`, agregar dos handlers (patrón estándar documentado por FastAPI, no interfiere con los `HTTPException` existentes porque Starlette resuelve por la clase más específica del MRO):

```python
import sqlite3
import traceback
from fastapi import Request
from fastapi.responses import JSONResponse

@app.exception_handler(sqlite3.IntegrityError)
async def manejar_conflicto(request: Request, exc: sqlite3.IntegrityError):
    print("[ERROR] conflicto de datos en " + str(request.url) + ": " + repr(exc), flush=True)
    return JSONResponse(status_code=409, content={"detail": "El dato ya existe o entra en conflicto con otro registro"})

@app.exception_handler(Exception)
async def manejar_error_no_previsto(request: Request, exc: Exception):
    print("[ERROR] " + request.method + " " + str(request.url) + ": " + repr(exc), flush=True)
    traceback.print_exc()
    return JSONResponse(status_code=500, content={"detail": "Ocurrió un error inesperado. Intentá de nuevo."})
```

Esto resuelve de una sola vez: el 500 en texto plano (ahora JSON, consistente con el resto de la API), y da red de contención para los casos sin try/except que la auditoría encontró en `database.py` y la carrera de `auth.py:142-146` — sin necesidad de sembrar try/except en las ~40 funciones de escritura de `database.py` (mucho más riesgo de introducir regresiones para el mismo beneficio).

### Hallazgo 4 — JWT `sub` sin proteger

En `backend/seguridad.py`, mover `int(carga["sub"])` **adentro** del `try` en ambas funciones:

```python
def usuario_actual(credenciales: HTTPAuthorizationCredentials = Depends(esquema_bearer)):
    sin_permiso = HTTPException(status_code=401, detail="Necesitas iniciar sesion", headers={"WWW-Authenticate": "Bearer"})
    if credenciales is None:
        raise sin_permiso
    try:
        carga = jwt.decode(credenciales.credentials, CLAVE_SECRETA, algorithms=[ALGORITMO])
        usuario_id = int(carga["sub"])
    except jwt.ExpiredSignatureError:
        raise HTTPException(status_code=401, detail="La sesion vencio, volve a iniciar sesion", headers={"WWW-Authenticate": "Bearer"})
    except (jwt.InvalidTokenError, KeyError, ValueError, TypeError):
        raise sin_permiso

    usuario = database.buscar_usuario_por_id(usuario_id)
    if usuario is None:
        raise sin_permiso
    return dict(usuario)
```

Mismo cambio en `usuario_actual_opcional` (`:78-91`): mover `int(carga["sub"])` dentro del try, agregar `KeyError, ValueError, TypeError` al `except`.

### Tests backend (pytest, mismo estilo que `test_operaciones.py`)

Reusar el scaffolding existente: `TestClient(app)`, env vars de DB temporal antes de importar `database`/`main` (`test_operaciones.py:1-25`), helper `nuevo_usuario`/`sesion`.

**`backend/tests/test_publicaciones.py`** (nuevo):
- `test_crear_publicacion_requiere_sesion` — `POST /publicaciones` sin header → 401.
- `test_cambiar_estado_requiere_ser_dueno` — usuario A crea (vía `database.crear_publicacion` directo, como hace `test_operaciones.py:62-63`), usuario B intenta `PATCH /publicaciones/{id}/estado` → 403.
- `test_cambiar_estado_dueno_puede` — mismo caso, con la sesión de A → 204.
- `test_error_no_previsto_da_json_no_texto_plano` — con `unittest.mock.patch("database.buscar_publicacion", side_effect=RuntimeError("boom"))`, `GET /publicaciones/1` → 500, `content-type` JSON, `"detail"` presente. Verifica el hallazgo 2 sin depender de gatillar una race condition real.
- `test_conflicto_de_datos_da_409` — con `patch("database.crear_usuario", side_effect=sqlite3.IntegrityError(...))`, `POST /auth/registro` con un email nuevo → 409 en vez de 500.

**`backend/tests/test_seguridad.py`** (nuevo):
- `test_token_con_sub_no_numerico_da_401_no_500` — armar un JWT a mano con `jwt.encode({"sub": "no-es-un-numero", ...}, seguridad.CLAVE_SECRETA, algorithm="HS256")` y pegarle a `GET /usuarios/yo` con ese token → 401, no 500. Verifica el hallazgo 4 sin depender de adivinar el secreto de producción.

---

## Android

### Hallazgo 5 — Logout automático en 401

Nuevo archivo `app/src/main/java/com/example/tpo/data/SesionExpirada.java` — singleton chico, mismo estilo que `MapaUtils`/`ConectividadUtils` (clase de utilidad estática) pero con estado mínimo para el listener:

```java
public final class SesionExpirada {
    private static Runnable escucha;

    private SesionExpirada() {}

    public static void registrarEscucha(Runnable alExpirar) {
        escucha = alExpirar;
    }

    public static void notificar() {
        if (escucha != null) {
            escucha.run();
        }
    }

    /** true si la respuesta 401 corresponde a un pedido que sí llevaba token
     * (sesión vencida) y no a un login/registro/OTP, que rechazan sin token. */
    public static boolean esRespuestaDeSesionVencida(Response respuesta) {
        return respuesta.code() == 401 && respuesta.request().header("Authorization") != null;
    }
}
```

En `login/NetworkModule.java`, agregar un `Authenticator` al `OkHttpClient.Builder` (además del interceptor existente):

```java
.authenticator((route, response) -> {
    if (SesionExpirada.esRespuestaDeSesionVencida(response)) {
        tokenManager.clearToken();
        SesionExpirada.notificar();
    }
    return null; // no reintenta: no hay refresh token
})
```

El chequeo de `Authorization` en el request original es la clave: `/auth/login` y `/auth/otp/verificar` devuelven 401 por credenciales incorrectas y **nunca** llevan ese header (son las llamadas previas a tener token), así que no disparan el logout global — siguen manejándose local en `LoginFragment`/`CodigoFragment` como hoy.

En `MainActivity.java` (`onCreate`, después de configurar el `NavController`), registrar el listener reusando exactamente la navegación que ya usa `PerfilFragment.cerrarSesion()` (`PerfilFragment.java:325-332`):

```java
SesionExpirada.registrarEscucha(() -> runOnUiThread(() -> {
    NavController nav = navHostFragment.getNavController();
    boolean yaEnLogin = nav.getCurrentDestination() != null
            && nav.getCurrentDestination().getId() == R.id.loginFragment;
    if (!yaEnLogin) {
        SesionUsuario.getInstancia().limpiar();
        nav.navigate(R.id.loginFragment, null,
                new NavOptions.Builder().setPopUpTo(R.id.nav_graph, true).build());
    }
}));
```

(El token ya se limpió en el Authenticator, que corre en el hilo de OkHttp — no hace falta repetirlo acá.)

### Hallazgo 3 — Room sin try/catch + crash handler global

Nuevo archivo `app/src/main/java/com/example/tpo/data/local/EjecutorSeguro.java` — envuelve el patrón repetido `executor.execute(() -> {...})` que hoy usan `BorradorRepositoryLocal`, `MisPublicacionesRepositoryLocal` y `PublicacionesVistas` sin protección:

```java
public final class EjecutorSeguro {
    private static final String TAG = "EjecutorSeguro";

    private EjecutorSeguro() {}

    /** Para operaciones fire-and-forget (sin callback a la UI): loguea y no propaga. */
    public static void ejecutar(ExecutorService executor, Runnable accion) {
        executor.execute(() -> {
            try {
                accion.run();
            } catch (RuntimeException excepcion) {
                Log.e(TAG, "Fallo una operación local", excepcion);
            }
        });
    }

    /** Para operaciones con resultado: si falla, el error llega a la UI en vez de crashear. */
    public static <T> void ejecutar(ExecutorService executor, Handler handlerPrincipal,
                                    Supplier<T> accion, RepositorioCallback<T> callback) {
        executor.execute(() -> {
            T resultado;
            try {
                resultado = accion.get();
            } catch (RuntimeException excepcion) {
                Log.e(TAG, "Fallo una operación local", excepcion);
                handlerPrincipal.post(() -> callback.onError("No pudimos completar la operación"));
                return;
            }
            handlerPrincipal.post(() -> callback.onExito(resultado));
        });
    }
}
```

Migrar los tres repositorios a usarlo, reemplazando cada bloque `executor.execute(() -> { dao.xxx(...); ... })` por una llamada a `EjecutorSeguro.ejecutar(...)`:
- `BorradorRepositoryLocal.java:49` (`guardar`, fire-and-forget), `:55-59` (`obtener`, con callback), `:64` (`borrar`, fire-and-forget).
- `MisPublicacionesRepositoryLocal.java:60-78` (`publicar`), `:83-91` (`listar`), `:106-109` (`cambiarEstado`) — todas con callback.
- `PublicacionesVistas.java:48-49` (`registrarVista`, fire-and-forget), `:57-63` (`listar`, con callback), `:67-75` (`obtenerVista`, con callback).

Esto previene el crash en el origen (que es lo que de verdad importa), en vez de solo loguearlo después de que ya mató el proceso.

**Handler global como red de contención adicional**, en `login/RondaApp.java`:

```java
@Override
public void onCreate() {
    super.onCreate();
    instancia = this;
    Thread.UncaughtExceptionHandler previo = Thread.getDefaultUncaughtExceptionHandler();
    Thread.setDefaultUncaughtExceptionHandler((hilo, excepcion) -> {
        Log.e("RondaApp", "Excepción no capturada en " + hilo.getName(), excepcion);
        if (previo != null) {
            previo.uncaughtException(hilo, excepcion);
        }
    });
}
```

Delega al handler anterior (el de Android) después de loguear, para no cambiar el comportamiento de crash existente — solo agrega visibilidad. No se integra ninguna librería de reporte de crashes (Crashlytics ni similar): no está en las dependencias del proyecto y sería agregar una lib "no vista en clase", fuera del criterio que ya sigue el resto del código (ver `MapaUtils`, `ImagenRemota`).

### Tests Android (JUnit puro, sin nuevas dependencias)

**`app/src/test/java/com/example/tpo/data/SesionExpiradaTest.java`** (nuevo):
- `esRespuestaDeSesionVencida` con una `Response` construida con `Request.Builder().header("Authorization", "Bearer x")` y código 401 → `true`.
- Mismo caso sin el header `Authorization` (simulando `/auth/login`) → `false`.
- Código distinto de 401 con header → `false`.
- `registrarEscucha` + `notificar()` → el `Runnable` se ejecuta.

Se construyen `okhttp3.Request`/`okhttp3.Response`/`okhttp3.Protocol` directamente con sus builders — `okhttp3` ya es dependencia del proyecto, no hace falta agregar `mockwebserver` ni Robolectric.

**`app/src/test/java/com/example/tpo/data/local/EjecutorSeguroTest.java`** (nuevo):
- Con un `ExecutorService` síncrono de prueba (`Executors.newSingleThreadExecutor()` + `awaitTermination` corto, o un executor que ejecuta inline) y un `Handler` — dado que `Handler(Looper.getMainLooper())` no corre en JVM plano sin Robolectric, testear directamente la sobrecarga con `Runnable` (fire-and-forget) usando un executor real y esperando su finalización, y para la variante con callback, extraer la lógica de captura de excepción a un método que no dependa de `Handler` (o inyectar un `Executor` que ejecute el post inline en el test). Concretamente: que una `accion` que lanza `RuntimeException` no propague la excepción fuera de `ejecutar(...)`, y que si no lanza, el resultado llegue.

> Nota de alcance: el handler global de `RondaApp` (parte de este mismo hallazgo) no lleva test automatizado — instalar y probar un `UncaughtExceptionHandler` de forma determinística requiere instrumentación o Robolectric, desproporcionado para esta pasada. Se verifica a mano (ver Verificación). El trabajo real de prevención lo hace `EjecutorSeguro`, que sí queda cubierto.

---

## Archivos a tocar — resumen

| Archivo | Cambio |
|---|---|
| `backend/routers/publicaciones.py` | auth + `publicacion_propia()` en `crear`/`editar`/`cambiar_estado` |
| `backend/main.py` | 2 exception handlers globales |
| `backend/seguridad.py` | `int(carga["sub"])` dentro del try, en ambas funciones |
| `backend/tests/test_publicaciones.py` | nuevo — 5 tests |
| `backend/tests/test_seguridad.py` | nuevo — 1 test |
| `app/.../data/SesionExpirada.java` | nuevo |
| `app/.../login/NetworkModule.java` | `.authenticator(...)` en el `OkHttpClient.Builder` |
| `app/.../MainActivity.java` | registrar el listener de `SesionExpirada` |
| `app/.../data/local/EjecutorSeguro.java` | nuevo |
| `app/.../data/BorradorRepositoryLocal.java`, `MisPublicacionesRepositoryLocal.java`, `PublicacionesVistas.java` | migrar a `EjecutorSeguro` |
| `app/.../login/RondaApp.java` | `setDefaultUncaughtExceptionHandler` |
| `app/src/test/.../SesionExpiradaTest.java` | nuevo |
| `app/src/test/.../local/EjecutorSeguroTest.java` | nuevo |

---

## Verificación

**Backend:**
1. `cd backend && pip install -r requirements.txt`
2. `python -m pytest tests/test_publicaciones.py tests/test_seguridad.py -v` — los 6 tests nuevos en verde.
3. `python -m pytest tests/test_operaciones.py -v` — regresión: los tests existentes siguen pasando (confirma que `usuario_id = int(carga["sub"])` movido de lugar no rompe el flujo normal, y que el exception handler global no interfiere con los `HTTPException` 401/403/404/409 que esos tests ya verifican).
   > Nota: correr cada archivo de test por separado (como ya hace el proyecto hoy) — no hay `conftest.py` compartido y cada archivo fija su propia DB temporal por variable de entorno al importar `database`, así que correrlos juntos en una sola invocación de pytest puede pisarse entre archivos (limitación preexistente, no introducida por este plan).
4. Manual: `uvicorn main:app --reload`, con el token de un usuario, `PATCH /publicaciones/{id-de-otro-usuario}/estado` → 403 (antes daba 204).

**Android:**
1. `./gradlew testDebugUnitTest --tests "*SesionExpirada*" --tests "*EjecutorSeguro*"` — verde.
2. `./gradlew testDebugUnitTest` — regresión completa de `app/src/test/`.
3. Manual: loguearse, esperar a que venza el token (o cambiar `HORAS_DE_VALIDEZ` a un valor chico en un backend de prueba), tocar cualquier acción autenticada → la app navega sola al login en vez de quedar mostrando "Tu sesión venció" en loop.
4. Manual: `LoginFragment` con contraseña incorrecta sigue mostrando el mensaje de credenciales inválidas (no dispara el logout global — confirma que el filtro por header `Authorization` funciona).
5. `./gradlew assembleDebug` — compila sin errores tras los cambios en los 3 repositorios locales migrados a `EjecutorSeguro`.
