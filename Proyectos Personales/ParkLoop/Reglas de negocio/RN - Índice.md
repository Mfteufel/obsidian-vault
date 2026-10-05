---
tags: [parkloop, reglas-de-negocio]
---

# RN - Índice

Volver a [[Resumen]]

> [!info] Para Claude Code
> Cada regla tiene un ID estable (`RN-xx`). Usalo en código, tests y commits.
> Las reglas viven en un **módulo de dominio separado** y se testean solas.
> Ningún valor va hardcodeado: tarifas, umbrales y tiempos son **datos configurables**.

## Todas las reglas

| ID | Regla | Nota |
|---|---|---|
| RN-01 | Prohibición de sobreventa | [[RN - Reservas, no-show y cancelación]] |
| RN-02 | Frescura del dato de disponibilidad | [[RN - Reservas, no-show y cancelación]] |
| RN-03 | Fraccionamiento de 15 minutos | [[RN - Tarifación 15 minutos]] |
| RN-04 | Tolerancia de cortesía (10 min) | [[RN - Tarifación 15 minutos]] |
| RN-05 | Consumo del banco de horas | [[RN - Banco de horas y planes]] |
| RN-06 | Tarifa de overage | [[RN - Banco de horas y planes]] |
| RN-07 | Tarifa según perfil | [[RN - Banco de horas y planes]] |
| RN-08 | No-show | [[RN - Reservas, no-show y cancelación]] |
| RN-09 | Cancelación | [[RN - Reservas, no-show y cancelación]] |
| RN-10 | Prorrateo por cambio de plan | [[RN - Banco de horas y planes]] |
| RN-11 | Compatibilidad de vehículo | [[RN - Compatibilidad de vehículo]] |
| RN-12 | Congelamiento por falla | [[RN - Fallas de torre]] |
| RN-13 | Tiempo estimado de maniobra | [[RN - Fallas de torre]] |

**En alcance del MVP (prototipo):** RN-01, RN-03, RN-04, RN-05, RN-06, RN-11 y UC-07.

## Tests obligatorios

1. **Concurrencia:** N reservas sobre 1 plaza → 1 sola confirmada.
2. **Banco:** saldo = suma de movimientos. Siempre.
3. **Cada RN** con los ejemplos numéricos del doc técnico (sección 16).

## Supuestos sin cerrar

Valores marcados `[SUPUESTO — VALIDAR]`: cortesía (10 min), frescura (30 s), no-show (20 min), cancelación, overage por plan. **Preguntar antes de decidir.**
