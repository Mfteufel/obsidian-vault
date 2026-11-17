---
tags: [parkloop, finanzas, pendientes]
actualizado: 2026-11-06
---

# Problemas del Sheet "ParkLoop Modelo Costos v3"

Volver a [[Finanzas - Resumen]]

> Las sumas del Sheet cierran. Los problemas son de datos y de criterio.

## Inconsistencias

- [x] **Planes distintos:** el Sheet usa Ocasional/Frecuente/Intensivo/Nocturno/Fundador. → Decidido: **usar los de la app**.
- [ ] **Boca de entrada:** figura "SÍ" pero está pendiente. Sin boca propia, el throughput baja a 30,8 mov/h y no cubre el pico (37,9).
- [ ] **Planes sobre el techo:** Frecuente, Intensivo y Fundador cuestan más que una cochera ($150.000).
- [ ] **Plan Fundador:** 19% del ingreso total, pero la propia hoja dice que destruye valor a ocupación madura.
- [ ] **Ocupación por franja:** el reparto 30/55/15% implica 97% de día, pero la columna "esperada" dice 60%. Con la esperada, la ocupación sería ~41% y no 51%.
- [ ] **Seguridad:** 1 persona cubre 48 de 168 h. Para 24/7 hacen falta 4–5.
- [x] **Tarifa walk-in:** Sheet $10.634 vs. doc técnica $7.500. → Decidido: **$7.500**.
- [ ] **Facturación:** el Sheet usa mínimo de 30 min; la app cobra bloques de 15 min con 10 min de tolerancia.

## Errores de cálculo

- [ ] **Transporte puerto-predio:** cargado en USD y en ARS. Da $5,6 M, debería dar $2,8 M.
- [ ] **App:** aparece dos veces (USD 5.000 y otra fila en $0).
- [ ] **Sensibilidad "walk-in −20%":** resta 20% de todos los ingresos. Debería restar ~$10,6 M, no $15,8 M.
- [ ] **"USD si el dólar sube":** muestra menos inversión, pero los costos de importación están en USD y subirían en pesos.
- [ ] **Incobrables:** calculados sobre todo el ingreso; la nota dice que son solo sobre suscripciones.
- [ ] **Tarifa SUV:** $17.000 en una hoja y $16.500 en otra.

## Datos faltantes

- [ ] Inversión en $0: obra, montaje, fundación, electricidad, grupo electrógeno, habilitaciones, lanzamiento.
- [ ] 8 gastos en cero y 18 estimados.
- [ ] Impuesto a las ganancias, amortización y retorno a inversores.
- [ ] Rampa de ocupación de los primeros meses.
