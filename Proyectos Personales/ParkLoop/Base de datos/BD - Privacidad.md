---
tags: [parkloop, base-de-datos, privacidad, legal]
---

# BD - Privacidad

Volver a [[Resumen]] · Modelo en [[BD - Modelo de datos]]

## Leyes que aplican

| Ley | Qué obliga |
|---|---|
| **Ley 25.326** (Datos Personales) | Proteger patentes, imágenes, ubicación, DNI y datos de pago |
| **Ley 24.240** (Defensa del Consumidor) | Informar tarifa y condiciones **antes** de cobrar |
| **CCyCN art. 1375** | El estacionamiento es un depósito: deber de custodia. Hay que poder probar qué auto entró y quién lo retiró |

---

## Datos sensibles en ParkLoop

| Dato | Dónde | Cuidado |
|---|---|---|
| DNI, fecha de nacimiento, domicilio | `USUARIO` | Guardar solo lo necesario |
| Licencia de conducir | `USUARIO` | La digital **no se guarda** (QR vence en 24 hs) |
| Patente | `VEHICULO`, `REGISTRO_CUSTODIA` | Dato personal |
| Fotos del vehículo | `REGISTRO_CUSTODIA` | Definir cuánto tiempo se guardan |
| Ubicación | App | Pedir permiso, no guardar historial sin motivo |
| Tarjeta | `MEDIO_PAGO` | **Solo token + últimos 4 dígitos** |

---

## Reglas

- ❌ **Nunca** guardar el número completo de la tarjeta.
- ❌ **No** guardar la imagen del DNI; solo los campos leídos.
- ✅ Cifrar en tránsito (HTTPS) y en reposo (en producción).
- ✅ Acceso por rol: el conductor ve lo suyo, el operador lo necesario.
- ✅ Registros de custodia **inalterables**.

> [!warning] Pendiente (supuesto S-14)
> Falta definir la **política de retención** de fotos y patentes. Hay que decidirla **antes** de empezar a acumular imágenes.

Ver también [[Front - Escaneo DNI y licencia]].
