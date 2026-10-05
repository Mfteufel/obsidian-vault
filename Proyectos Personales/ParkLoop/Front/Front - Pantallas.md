---
tags: [parkloop, front, pantallas]
---

# Front - Pantallas

Volver a [[Resumen]] · Stack en [[Front - Stack]]

## Estructura general

```
Onboarding (primera vez)
   ↓
App con 5 tabs abajo:
[ Perfil ] [ Reservar ] [ Acceso ] [ Mi auto ] [ Banco ]
```

**Referencia visual:** el prototipo HTML (`parkloop-prototipo.html`) y los mockups de `Layouts_ParkLoop.pdf`.

---

## Onboarding

**Objetivo:** registrar al usuario y su auto.

Pasos:
1. Registro con **email y teléfono** (`HU-01`).
2. Datos personales → **escaneo de DNI** para autocompletar. Ver [[Front - Escaneo DNI y licencia]].
3. Carga del vehículo con **dimensiones** (`HU-02`).
4. Validación de compatibilidad. Ver [[RN - Compatibilidad de vehículo]].

---

## Tab 1 — Perfil

Pedido de las Notas: **centralizar** datos personales y documentos.

Muestra:
- **Foto** del usuario.
- Nombre, fecha de nacimiento, domicilio, DNI.
- **Plan actual** (tier) y botón **cambiar plan**.
- **Banco de horas** (saldo).
- Datos del auto.
- Estado de validación de DNI y licencia.

Casos de uso: `UC-10` (cambiar plan), `UC-18` (vehículo).

---

## Tab 2 — Reservar

**La pantalla más importante.** Ver [[Front - Reservas y mapa]].

- Mapa interactivo con los sitios.
- Disponibilidad por sitio (plazas libres).
- Hora de entrada y duración con selectores tipo scroll.
- **Costo estimado** antes de confirmar (`HU-07`).

Casos de uso: `UC-01`, `UC-02`.

---

## Tab 3 — Acceso

Lo que el usuario usa **en la torre**.

- **Código de acceso** de la reserva (alternativa si falla la lectura de patente).
- Estado de la sesión: `INICIANDO`, `EN_CURSO`, etc.
- Botón **"Voy en camino"** → pre-aviso de retiro (`UC-07`). La torre baja el auto antes de que llegue.
- **Posición en la cola** y tiempo estimado (`HU-25`).

> [!tip] Por qué importa el pre-aviso
> Una maniobra puede tardar 175 s. Con pre-aviso, el usuario casi no espera. Ver [[RN - Fallas de torre]] (RN-13).

---

## Tab 4 — Mi auto

> [!warning] Diseño final pendiente
> Usar una versión simple por ahora.

- Patente, marca, modelo, cédula.
- Dimensiones: alto, largo, ancho (mm) y peso (kg).
- En qué sitios es compatible.
- Marca y modelo con **selector tipo scroll**.

Caso de uso: `UC-18`.

---

## Tab 5 — Banco

> [!warning] Diseño final pendiente
> Usar una versión simple por ahora.

- **Saldo** de horas del ciclo.
- Fecha de vencimiento del ciclo.
- **Lista de movimientos**: acreditaciones, consumos, ajustes.
- Cada consumo linkea a su sesión con el **desglose** del cobro.

Caso de uso: `UC-11`. Reglas en [[RN - Banco de horas y planes]].

---

## Panel demo (solo prototipo)

El prototipo HTML tiene un panel lateral para simular:
- Velocidad de simulación.
- Plan del usuario.
- Saldo del banco.

Útil para mostrar la app sin backend real.
