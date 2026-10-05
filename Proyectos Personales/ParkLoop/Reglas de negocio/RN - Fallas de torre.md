---
tags: [parkloop, reglas-de-negocio, torre, fallas]
---

# RN - Fallas de torre

Volver a [[RN - Índice]] · [[Resumen]]

## Contexto

- La torre tiene **un solo motor**. Si falla, queda **100% parada**, con los autos adentro.
- Solo hace **una maniobra a la vez**.
- Una maniobra tarda hasta **175 segundos** (peor caso, a verificar con el fabricante).

---

## RN-12 — Congelamiento por falla

Si la torre queda **fuera de servicio** con autos adentro:
- El tiempo de la falla **no se cobra**.
- Se acumula en `SESION.segundos_congelados`.
- Se resta antes de calcular los bloques de 15 min.

Por qué: el usuario no puede sacar su auto. Cobrarle sería injusto e ilegal (Ley 24.240).

---

## RN-13 — Tiempo estimado de maniobra

```
tiempo_estimado = tiempo de las maniobras en cola
                + tiempo_base del modelo
                + (distancia_en_posiciones × tiempo_por_posicion)
```

- La distancia es el camino **más corto** en el carrusel.
- Mostrar un valor **conservador**. Mejor sobrestimar que quedarse corto.

---

## Pre-aviso de retiro (`UC-07`)

El usuario avisa **"voy en camino"** → la torre baja el auto mientras viaja.
- Si no llega a tiempo, el auto **se vuelve a guardar** y la sesión sigue.
- **No es una función secundaria**: es lo que hace tolerable esperar 175 s.

---

## Qué hace el sistema ante una falla (`UC-13`)

1. Pone la torre en `FUERA_DE_SERVICIO`.
2. Deja de aceptar maniobras y reservas.
3. **Avisa a todos los usuarios con auto adentro** (no solo push).
4. Congela el cobro.
5. Al volver, **compara** su registro con lo que dice el PLC.
6. Si no coincide → **no vuelve sola**. Decide una persona.

## Reglas de seguridad (no negociables)

- **El PLC decide.** El software solo pide.
- Un rechazo por seguridad (ej: persona en el recinto) **nunca** se reintenta ni se fuerza.
- El registro de **qué auto está en qué bay** nunca puede perderse.

Relacionado: [[Back - Arquitectura]] · [[Front - Pantallas]] (tab Acceso)
