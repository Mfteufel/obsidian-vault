---
tags: [parkloop, reglas-de-negocio, reservas]
---

# RN - Reservas, no-show y cancelación

Volver a [[RN - Índice]] · [[Resumen]]

## RN-01 — Prohibición de sobreventa

**Nunca** puede haber más reservas + sesiones que bays disponibles.

**Por qué es crítico:** una torre tiene solo 16 plazas. Vender una de más = alguien con reserva llega y no tiene lugar.

**Cómo:**
- La confirmación toma un **bloqueo transaccional** sobre el inventario del sitio.
- Dos confirmaciones simultáneas → **solo una gana**.

**Test obligatorio:** N solicitudes sobre 1 plaza → exactamente 1 confirmación.

Implementación en [[Back - Reintentos e idempotencia]].

---

## RN-02 — Frescura del dato

La disponibilidad tiene que ser **reciente** (propuesta: menos de **30 segundos**).

Si el dato es viejo:
- Se muestra **con la hora** ("actualizado hace 4 minutos").
- **Se deshabilita** el botón de reservar.

**Nunca mostrar un dato viejo como si fuera actual.**

---

## RN-08 — No-show

Si el usuario **no llega** dentro de la tolerancia:
- La reserva pasa a `NO_SHOW`.
- **La plaza se libera** al instante.

> [!warning] Supuesto
> Tolerancia propuesta: **20 minutos**. Cargo: **1 hora** de tarifa.

Por qué tan estricto: 1 plaza de 16 bloqueada = 6% de la capacidad perdida.

---

## RN-09 — Cancelación

| Cuándo cancela | Cargo |
|---|---|
| Más de 2 hs antes | Sin cargo |
| Entre 2 hs y el inicio | 50% de 1 hora |
| Después del inicio | Igual que no-show |

> [!warning] Supuesto
> Valores pendientes de definición comercial.

---

## Otras reglas de reserva

- **Modificar:** si el nuevo horario no tiene lugar, **la reserva original queda intacta**.
- **Saldo bajo:** se permite reservar igual, avisando que el excedente será overage.
- **Pago rechazado:** la reserva queda `RECHAZADA` y la plaza se libera de inmediato.

Relacionado: [[Front - Reservas y mapa]] · [[Back - Endpoints]]
