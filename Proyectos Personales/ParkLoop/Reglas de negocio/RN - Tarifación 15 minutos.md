---
tags: [parkloop, reglas-de-negocio, tarifacion]
---

# RN - Tarifación 15 minutos

Volver a [[RN - Índice]] · [[Resumen]]

## RN-03 — Fraccionamiento de 15 minutos

**El diferencial más visible del producto.**
El mercado cobra hora completa. ParkLoop cobra cada 15 minutos.

**Fórmula:**
```
bloques = ceil(duracion_segundos / 900)     // 900 s = 15 min
importe = bloques × (tarifa_hora / 4)
```

**Reglas:**
- La duración se mide **en segundos**.
- Se redondea **una sola vez**, al cerrar la sesión.
- Todo en **centavos** (enteros).
- Se aplica a **todos**, incluido el walk-in.

**Ejemplos (tarifa $7.500/h):**

| Duración real | Bloques | Se cobra | Importe |
|---|---|---|---|
| 12 min | 1 | 15 min | $1.875 |
| 15 min 0 s | 1 | 15 min | $1.875 |
| 15 min 1 s | 2 | 30 min | $3.750 |
| 1 h 5 min | 5 | 1 h 15 min | $9.375 |
| 2 h | 8 | 2 h | $15.000 |
| 8 h 40 min | 35 | 8 h 45 min | $65.625 |

**Comparación:** 1 h 5 min en ParkLoop = **$9.375**. Con redondeo a hora = $15.000. **37,5% menos.**

---

## RN-04 — Tolerancia de cortesía

Si la sesión dura **10 minutos o menos** → **no se cobra** y no se consume banco.
La sesión **igual se registra** (custodia, evidencia).

> [!warning] Supuesto
> 10 minutos es una propuesta. Tiene que ser configurable.

---

## El desglose se guarda

- El detalle del cálculo se **persiste** en `DESGLOSE_TARIFA` al cobrar.
- **No se recalcula** al mostrarlo. Si la tarifa cambia, el comprobante viejo sigue igual.
- El usuario tiene que ver: entrada, salida, bloques, tarifa, banco usado, overage (Ley 24.240).

Relacionado: [[RN - Banco de horas y planes]] · [[BD - Modelo de datos]]
