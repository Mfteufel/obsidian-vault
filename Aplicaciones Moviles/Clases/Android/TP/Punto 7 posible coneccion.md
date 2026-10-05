## Fase 2 — Punto 7: definir el contrato de la API (sin código)

**No existe nada de ofertas en la API.** Primero definimos el contrato. Después la app lo consume (Fase 3) y el backend lo implementa (Fase 4).

Crear el archivo **`docs/ofertas-api.md`** con:

- Cada endpoint: método, ruta, body de request y **JSON exacto** de respuesta.
- Nombres de campos en el mismo estilo que el resto de la API (camelCase, ids como string, fechas como timestamp int).
- Códigos de error: `400`, `401`, `403`, `404`, `409`.

**Este archivo es la única fuente de verdad.** App y backend lo siguen al pie de la letra. **Mostrámelo y esperá mi OK antes de seguir.**

Base para el contrato (ajustala al estilo del backend existente):

### Modelo `Oferta` (propuesta, ajustala al estilo del backend)

|Campo|Tipo|Notas|
|---|---|---|
|`id`|int||
|`publicacionId`|int||
|`compradorId`|int|quien oferta|
|`vendedorId`|int|dueño de la publicación|
|`precio`|number|precio propuesto actual|
|`mensaje`|string, opcional|mensaje breve|
|`estado`|string|`PENDIENTE`, `ACEPTADA`, `RECHAZADA`, `VENCIDA`|
|`turno`|string|`VENDEDOR` o `COMPRADOR`: quién tiene que responder|
|`fechaCreacion`|int (timestamp)|mismo formato que `fechaPublicacion`|
|`venceEn`|int (timestamp)|plazo de vigencia (ej: 48 h)|

### Reglas de negocio

- El **comprador** crea la oferta → `turno = VENDEDOR`.
- Quien tiene el turno puede: **aceptar**, **rechazar** o **contraofertar** (nuevo precio).
- Contraofertar: cambia el precio, pasa el turno al otro y **renueva `venceEn`**.
- **Vencimiento automático:** al leer ofertas, si `venceEn` ya pasó y sigue `PENDIENTE`, marcarla `VENCIDA`. (No hace falta un cron.)
- No se puede ofertar sobre una publicación **propia** ni que no esté **activa**.
- Solo comprador y vendedor pueden ver o tocar su oferta. Si no → `403`.
- **Dirección de entrega:** solo se devuelve si la oferta está `ACEPTADA`.

### Endpoints propuestos (todos con Bearer token)

|Acción|Endpoint|
|---|---|
|Crear oferta|`POST /ofertas` → `{ publicacionId, precio, mensaje? }`|
|Ofertas enviadas|`GET /ofertas/enviadas`|
|Ofertas recibidas|`GET /ofertas/recibidas`|
|Ver una oferta|`GET /ofertas/{id}` (incluye dirección solo si `ACEPTADA`)|
|Aceptar|`POST /ofertas/{id}/aceptar`|
|Rechazar|`POST /ofertas/{id}/rechazar`|
|Contraofertar|`POST /ofertas/{id}/contraoferta` → `{ precio }`|

Cada oferta en las respuestas debe traer también: título y foto principal de la publicación, y nombre de la contraparte. Así la lista no necesita más llamadas.

**Preguntame antes de decidir:** ¿aceptar una oferta cambia el estado de la publicación? (No lo definas vos.)

---

## Fase 3 — Punto 7: conectar la app (contra el contrato)

1. Crear `OfertasApi` (Retrofit) siguiendo **exactamente** `docs/ofertas-api.md`.
    - Crear los DTOs (request/response) con los mismos nombres de campo.
2. **Repository con dos implementaciones** detrás de la misma interfaz:
    - `OfertasRepositoryMock`: devuelve datos falsos con la forma del contrato. Sirve para probar las pantallas **mientras no existe el backend**.
    - `OfertasRepositoryRemoto`: usa `OfertasApi`.
    - Elegir cuál se usa en **un solo lugar** (el módulo de Hilt), así el cambio es de una línea.
3. **Ofertar desde el detalle:** diálogo con precio + mensaje opcional → `POST /ofertas`.
    - El botón está en la pantalla de Detalle (de Damian). **Avisame antes de tocarla.**
4. **"Mis ofertas":** reemplazar Mock por datos reales.
    - Dos pestañas: **Enviadas** y **Recibidas**.
    - Mostrar precio, estado y vencimiento.
    - "Siempre actualizadas": recargar en `onResume` + pull-to-refresh.
5. **Acciones según turno:** mostrar aceptar / rechazar / contraofertar solo a quien le toca.
6. **Oferta aceptada:** mostrar la dirección de entrega al comprador.
7. **Sin conexión:** deshabilitar ofertar/responder y mostrar mensaje claro de que se necesita internet.

---

## Fase 4 — Punto 7: crear tablas y endpoints en el backend

Rama: **`testzone-backend`**. Seguí el estilo del backend existente (modelos, rutas, auth Bearer).

1. **Tablas:** crear la tabla `ofertas` en la base (SQLite) con los campos del contrato.
    - Cómo se crean las tablas hoy (migración, `create_all`, script): **seguí el mismo mecanismo**.
2. **Endpoints:** implementar todos los de `docs/ofertas-api.md`, con las reglas de negocio de la Fase 2.
    - Las respuestas deben coincidir **campo por campo** con el contrato.
3. **Registros de prueba (seed):** algunas ofertas en distintos estados (pendiente, aceptada, rechazada, vencida), entre usuarios y publicaciones que ya existan.
    - Que el seed **no duplique** datos si se corre dos veces.
    - Que **no borre** datos existentes.
4. Pushear a `testzone-backend`, esperar el deploy y **probar cada endpoint en `/docs`**. Pasame los resultados.
5. En la app: cambiar en Hilt de `OfertasRepositoryMock` a `OfertasRepositoryRemoto`.
6. Probar el flujo completo en el emulador con **dos usuarios** (comprador y vendedor): ofertar → contraofertar → aceptar → el comprador ve la dirección.