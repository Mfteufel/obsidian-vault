---
tags: [parkloop, back, api, endpoints]
---

# Back - Endpoints

Volver a [[Resumen]] · Arquitectura en [[Back - Arquitectura]]

## Convenciones

- Importes siempre en **centavos** (enteros).
- Fechas en **ISO 8601 con zona horaria** (ej: `2026-08-20T18:00:00-03:00`).
- Endpoints que crean o cobran aceptan el header **`Idempotency-Key`**.
- Errores de negocio devuelven un **código estable** en el body, no solo el HTTP status.

---

## Fase 1 (MVP inicial)

| Método | Ruta | Para qué |
|---|---|---|
| GET | `/health` | Ver que la API está viva |
| GET | `/sitios` | Sitios con disponibilidad |
| GET | `/vehiculos` | Vehículos del usuario |
| POST | `/vehiculos` | Cargar vehículo (valida compatibilidad) |
| POST | `/reservas` | Crear reserva · **idempotente** |

---

## Disponibilidad y reserva

**`GET /sitios?lat=&lng=&desde=&hasta=`**
Devuelve por sitio: plazas libres, estado, tarifa, `disponibilidad_actualizada_en`, `reservable`.
Si el dato es viejo → `reservable: false`.

**`POST /reservas`** · idempotente
```json
{
  "sitio_id": "sit_001",
  "vehiculo_id": "veh_042",
  "inicio_previsto": "2026-08-20T18:00:00-03:00",
  "fin_previsto": "2026-08-20T21:00:00-03:00"
}
```
Responde con `codigo_acceso`, `tolerancia_hasta` y la **estimación** (minutos de banco, overage, importe).

**`PATCH /reservas/{id}`** — modificar. Si falla, **la reserva original queda intacta**.
**`DELETE /reservas/{id}`** — cancelar. Devuelve el cargo aplicado.

---

## Ciclo de estacionamiento

| Método | Ruta | Para qué |
|---|---|---|
| POST | `/sitios/{id}/ingresos` | Ingreso tras leer patente · idempotente |
| POST | `/sesiones/{id}/retiro` | Retiro inmediato o **pre-aviso** · idempotente |
| GET | `/sesiones/{id}` | Estado en vivo |
| GET | `/sesiones/{id}/desglose` | Detalle del cobro |

Las maniobras son **asíncronas** → responden `202` con posición en cola y tiempo estimado.

---

## Membresía

| Método | Ruta | Para qué |
|---|---|---|
| GET | `/membresia` | Plan actual, saldo, vencimiento |
| GET | `/membresia/movimientos` | Libro de movimientos del banco |
| POST | `/membresia` | Contratar o cambiar plan · idempotente |

**Invariante:** la suma de los movimientos = el saldo mostrado. Siempre.

---

## Errores de negocio

| Código | Situación | HTTP |
|---|---|---|
| `SIN_DISPONIBILIDAD` | No hay plaza | 409 |
| `VEHICULO_INCOMPATIBLE` | No entra en el bay | 422 |
| `SITIO_NO_RESERVABLE` | Fuera de servicio o dato viejo | 409 |
| `MEDIO_PAGO_RECHAZADO` | Pago rechazado | 402 |
| `CONFLICTO_CONCURRENCIA` | Perdió la carrera por la última plaza | 409 |

Detalle completo: sección 17 del doc técnico.
