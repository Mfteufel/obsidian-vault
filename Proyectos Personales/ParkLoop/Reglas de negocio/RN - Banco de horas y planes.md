---
tags: [parkloop, reglas-de-negocio, banco-de-horas, planes]
---

# RN - Banco de horas y planes

Volver a [[RN - Índice]] · [[Resumen]]

## Qué es el banco de horas

El suscriptor paga un plan mensual y recibe un **saldo de horas**.
Lo usa **cuando quiera**, cualquier día, sin franja horaria.

## Planes (banda Standard)

| Plan | Banco mensual | Precio | Overage |
|---|---|---|---|
| Sin plan (walk-in) | — | — | Tarifa plena $7.500/h |
| Básico Deambulante | ~63 hs | $80.000 | $5.000/h |
| Plus | ~105 hs | $105.000 | $4.500/h |
| Full | 150 hs | $120.000 | $4.500/h |
| Ilimitado | Sin tope | $135.000 | — |

> [!warning] Datos, no constantes
> Los planes son **configurables en la BD**. Faltan definir las bandas Económica y Premium.

---

## RN-05 — Consumo del banco

Al cerrar la sesión:
1. Calcular bloques de 15 min ([[RN - Tarifación 15 minutos]]).
2. Saldo = `SUM(minutos)` de los movimientos del ciclo.
3. Consumir del banco `min(minutos_sesion, saldo)`, **redondeado hacia arriba al bloque de 15**.
4. El resto → **overage**.
5. Generar un movimiento `CONSUMO`.

**Ejemplo:** plan Plus, **40 min de saldo**, sesión de **2 h**.

| Paso | Resultado |
|---|---|
| Bloques | 8 (120 min) |
| Del banco | 40 → redondeado a **45 min** |
| Overage | 120 − 45 = **75 min** |
| Importe overage | 5 × ($4.500/4) = **$5.625** |
| Saldo final | **−5 min** (correcto y deliberado) |

---

## RN-06 — Overage

Tarifa por usar **más** que el banco.
Siempre **mayor** que el $/hora del plan y **menor** que el walk-in.
Así incentiva a suscribirse y a subir de plan.

## RN-07 — Tarifa según perfil

| Perfil | Tarifa |
|---|---|
| Walk-in (registrado o no) | Tarifa plena |
| Suscriptor con saldo | Banco |
| Suscriptor sin saldo | Overage de su plan |
| Ilimitado | Sin cargo por tiempo |
| Suscripción suspendida | Tarifa plena |

## RN-10 — Cambio de plan

- **Subir (upgrade):** inmediato. Se cobra la diferencia **proporcional** a los días que quedan y se acreditan horas proporcionales.
- **Bajar (downgrade):** toma efecto **el próximo ciclo**. Sin reintegro.

Por qué: evitar que alguien use 150 hs del Full y baje a Básico el día 25.

---

## Cómo se modela

- **Libro de movimientos**, no un contador.
- Cada acreditación, consumo, ajuste o vencimiento = **una fila nueva**.
- Nunca se edita ni se borra. Corregir = agregar un `AJUSTE`.
- El plan **Ilimitado no tiene banco**. Es otra política.

Ver [[BD - Modelo de datos]].
