---
tags: [parkloop, front, dni, escaneo]
---

# Front - Escaneo DNI y licencia

Volver a [[Resumen]] · Pantalla en [[Front - Pantallas]]

## Objetivo

Pedido de las Notas: **validar DNI y licencia** con escaneo o foto.
Beneficio: el usuario no tipea sus datos → menos errores.

---

## DNI argentino

**Tiene un código PDF417** en el dorso.

**Qué es PDF417:** un código de barras **2D apilado** (varias filas de barras). No es un QR.

**Qué datos trae:**
- Nombre y apellido
- Número de DNI
- Fecha de nacimiento
- Sexo
- Número de trámite

**Qué hacemos:** leerlo con la cámara → autocompletar el formulario.

### Cómo implementarlo

- Librería: **`expo-camera`** con `barcodeScannerSettings: { barcodeTypes: ['pdf417'] }`.
- El texto leído viene separado por `@`. Se parsea en campos.
- El usuario **revisa y confirma** antes de guardar.

> [!warning] Holograma
> El DNI tiene un holograma que puede dificultar la lectura. Mostrar instrucciones claras (buena luz, sin reflejos).

> [!note] Formato
> El orden exacto de los campos cambia entre versiones del DNI. Validar con DNIs reales antes de dar por cerrado el parser.

---

## Licencia de conducir

| Tipo | Código | ¿Se guarda? |
|---|---|---|
| **Física** | PDF417 | ✅ Sí, se lee igual que el DNI |
| **Digital** (app Mi Argentina) | QR | ❌ No. El QR **vence a las 24 hs** |

**Regla:** la licencia digital **no se guarda como dato fijo**. Se escanea cuando haga falta validarla.

---

## Privacidad

DNI y licencia son **datos personales** (Ley 25.326).

- Guardar **solo los campos necesarios**, no la imagen completa.
- No mandar la imagen a servicios externos.

Ver [[BD - Privacidad]].
