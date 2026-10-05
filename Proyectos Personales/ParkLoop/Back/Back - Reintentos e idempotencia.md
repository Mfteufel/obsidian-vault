---
tags: [parkloop, back, idempotencia, errores]
---

# Back - Reintentos e idempotencia

Volver a [[Resumen]] · Endpoints en [[Back - Endpoints]]

## El problema

Una red móvil falla. La app no recibe respuesta y **reintenta**.
Si el request era "crear reserva" o "cobrar", se puede **duplicar**.

> [!danger] Antecedente real
> En Bidly, una lógica de reintentos hizo que los **POST se ejecutaran varias veces**. No repetir ese error.

---

## Regla 1 — Reintentar solo lecturas

| Tipo | ¿Reintento automático? |
|---|---|
| `GET` (leer) | ✅ Sí |
| `POST`, `PATCH`, `DELETE` (crear, cambiar, cobrar) | ❌ No, salvo con clave de idempotencia |

---

## Regla 2 — Clave de idempotencia

**Qué es idempotencia:** hacer la misma operación dos veces produce **un solo efecto**.

**Cómo funciona:**
1. La app genera un ID único (UUID) antes de mandar el POST.
2. Lo manda en el header `Idempotency-Key`.
3. El backend guarda la clave y la respuesta.
4. Si llega de nuevo la misma clave → devuelve la **misma respuesta**, sin volver a ejecutar.

```typescript
// app
const key = crypto.randomUUID();
await api.post('/reservas', body, { headers: { 'Idempotency-Key': key } });
// si reintenta, usa la MISMA key
```

**Dónde es obligatoria:**
- Crear reserva.
- Cobros.
- Contratar plan.
- Ingreso y retiro de vehículo.
- Comandos a la torre (dos `SolicitarRetiro` = una sola maniobra).

---

## Regla 3 — Concurrencia en reservas

Dos personas reservan **la última plaza al mismo tiempo**.
**Solo una puede ganar.** (RN-01)

**Cómo:** bloqueo transaccional en PostgreSQL (`SELECT ... FOR UPDATE` o transacción serializable con Prisma).

**Nunca:** leer disponibilidad y después escribir sin protección.

**Test obligatorio:** N reservas simultáneas sobre 1 plaza → exactamente 1 confirmada.

Ver [[RN - Reservas, no-show y cancelación]].

---

## Cold start (servidores gratuitos)

Si en el futuro la API se sube a un servicio con "cold start" (ej: Railway, Render gratis), la primera llamada tarda. Poner **timeouts generosos** en la app y un reintento **solo en GET**.
