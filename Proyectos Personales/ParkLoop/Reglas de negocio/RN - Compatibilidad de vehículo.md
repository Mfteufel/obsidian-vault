---
tags: [parkloop, reglas-de-negocio, vehiculo]
---

# RN - Compatibilidad de vehículo

Volver a [[RN - Índice]] · [[Resumen]]

## RN-11 — Regla

Un auto es compatible con una torre **solo si cumple las 4 condiciones**:

```
vehiculo.alto_mm  <= modelo.alto_max_mm
vehiculo.largo_mm <= modelo.largo_max_mm
vehiculo.ancho_mm <= modelo.ancho_max_mm
vehiculo.peso_kg  <= modelo.peso_max_kg
```

Los límites vienen de la tabla `MODELO_TORRE`. **Nunca hardcodeados.**

## Límites de referencia (config SUV)

| Medida | Máximo |
|---|---|
| Alto | 2.000 mm |
| Largo | 5.300 mm |
| Ancho | 1.900 mm |
| Peso | 2.350 kg |

La torre es **solo SUV**. Un bay de SUV acepta sedanes, al revés no.

---

## Cuándo se valida

- ✅ **Al cargar el auto** → se le dice en qué sitios puede operar.
- ✅ **Al reservar** → si no entra, no se deja reservar.
- ❌ **No** esperar a que llegue a la torre. Rechazarlo ahí es una falla de producto.

El PLC igual lo frena en la torre, pero eso es **la última barrera de seguridad**, no la validación de negocio.

## En la app (pedido de las Notas)

- El usuario carga marca y modelo con **selector tipo scroll**.
- Se vincula a una **base de medidas estándar** (tipo Excel/catálogo) para autocompletar dimensiones (`HU-03`).
- Si no es compatible → **alerta** clara.

**Ejemplo (`HU-02`):**
- Alto 1.850 mm, largo 4.700, ancho 1.850, 1.900 kg → ✅ compatible.
- Alto 2.150 mm → ❌ "Este vehículo no puede usar la red".

> [!warning] Supuesto S-12
> ¿Cómo verificar que el usuario no miente en las medidas? Pendiente. Idea: medir con sensores en el primer ingreso.

Relacionado: [[Front - Pantallas]] · [[BD - Modelo de datos]]
