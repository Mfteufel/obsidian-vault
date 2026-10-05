---
tags: [parkloop, front, reservas, mapa]
---

# Front - Reservas y mapa

Volver a [[Resumen]] · Pantalla en [[Front - Pantallas]]

## Cambios pedidos en las Notas

| Antes (prototipo) | Ahora |
|---|---|
| Mapa como **imagen fija** | Mapa **interactivo** |
| Botones fijos **1, 2, 4, 8 hs** | Selector **tipo scroll** (como temporizador de iPhone) |
| Sin hora de entrada | **Hora de entrada exacta** |
| Duración en horas | Duración con **precisión de minutos** |

---

## Mapa interactivo

- Librería: **`react-native-maps`**.
- Un **marcador por sitio** (Palermo, Recoleta, Belgrano en los datos de prueba).
- Al tocar un sitio se ve:
  - Plazas libres / totales.
  - Estado: `OPERATIVO`, `CON_DEMORAS`, `FUERA_DE_SERVICIO`.
  - Tarifa por hora.
  - **Hace cuánto se actualizó** el dato.

> [!warning] Web
> `react-native-maps` no anda en web. Buscar alternativa (Leaflet) para la versión web.

---

## Flujo de reserva (`UC-02`)

1. Elegir **sitio** en el mapa.
2. Elegir **vehículo**.
3. Elegir **hora de entrada** (picker scroll).
4. Elegir **duración** (picker scroll, horas + minutos).
5. La app pide al backend el **costo estimado**.
6. Se muestra el desglose:
   - Cuánto sale del **banco de horas**.
   - Cuánto es **overage** o tarifa walk-in.
7. El usuario **confirma**.
8. Se muestra el **código de acceso** y la tolerancia de llegada.

---

## Qué muestra el costo estimado

Ejemplo (`HU-07`): suscriptor Plus con 1 hora de saldo, reserva de 3 horas.
- 1 hora del banco.
- 2 horas de overage a $4.500/h.

**El cálculo lo hace el backend.** Ver [[RN - Tarifación 15 minutos]] y [[RN - Banco de horas y planes]].

---

## Errores que la app tiene que manejar

| Código | Qué mostrar |
|---|---|
| `SIN_DISPONIBILIDAD` | "No hay lugar" + ventanas cercanas |
| `VEHICULO_INCOMPATIBLE` | "Tu auto no entra en esta torre" |
| `SITIO_NO_RESERVABLE` | "Sitio sin datos actualizados" |
| `CONFLICTO_CONCURRENCIA` | "Alguien reservó el último lugar" |

Ver [[Back - Endpoints]].

---

## Reglas relacionadas

- Si el dato de disponibilidad es viejo → **botón reservar deshabilitado** (RN-02).
- Si el auto no es compatible → **no dejar reservar** (RN-11).
- Ver [[RN - Reservas, no-show y cancelación]] y [[RN - Compatibilidad de vehículo]].
