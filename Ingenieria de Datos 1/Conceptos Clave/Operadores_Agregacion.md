---
tags: [base-de-datos, algebra-relacional, sql-server, agregacion]
aliases: [Totales, Agregación, γ, COUNT, SUM, AVG, MIN, MAX]
---

# Operadores de Agregación (Totales) — γ

> Realizan **cálculos sobre conjuntos de tuplas** y producen resultados resumidos.

**Notación:** $\gamma_{A,\ CALC(B)}(R)$

donde:
- **γ** (gamma) = operador de agregación
- **A** = atributo(s) por los que se agrupa
- **CALC(B)** = función aplicada sobre el atributo B
- **R** = relación

## Tabla base: VENTAS

| sale_ID | item | cantidad | precio |
|---------|------|----------|--------|
| 1       | 101  | 2        | 10     |
| 2       | 102  | 1        | 20     |
| 3       | 101  | 3        | 10     |
| 4       | 103  | 5        | 15     |

## SUM — Suma de cantidades por item

**Álgebra:** $\gamma_{item,\ SUM(cantidad)}(VENTAS)$

| item | SUM(cantidad) |
|------|---------------|
| 101  | 5             |
| 102  | 1             |
| 103  | 5             |

```sql
SELECT item, SUM(cantidad) AS SumCantidad
FROM VENTAS
GROUP BY item;
```

## COUNT — Cantidad de ventas por item

**Álgebra:** $\gamma_{item,\ COUNT(sale\_ID)}(VENTAS)$

| item | COUNT(sale_ID) |
|------|----------------|
| 101  | 2              |
| 102  | 1              |
| 103  | 1              |

```sql
SELECT item, COUNT(sale_ID) AS CantVentas
FROM VENTAS
GROUP BY item;
```

## AVG — Promedio de precio por item

**Álgebra:** $\gamma_{item,\ AVG(precio)}(VENTAS)$

| item | AVG(precio) |
|------|-------------|
| 101  | 10          |
| 102  | 20          |
| 103  | 15          |

```sql
SELECT item, AVG(precio) AS PrecioPromedio
FROM VENTAS
GROUP BY item;
```

## MIN y MAX

```sql
SELECT MIN(precio) AS PrecioMin, MAX(precio) AS PrecioMax
FROM VENTAS;
```

| PrecioMin | PrecioMax |
|-----------|-----------|
| 10        | 20        |

## Resumen de funciones

| Función | Descripción | SQL Server |
|---------|-------------|------------|
| `SUM(col)` | Suma los valores | `SUM(cantidad)` |
| `COUNT(col)` | Cuenta filas no NULL | `COUNT(sale_ID)` |
| `COUNT(*)` | Cuenta todas las filas | `COUNT(*)` |
| `AVG(col)` | Promedio | `AVG(precio)` |
| `MIN(col)` | Valor mínimo | `MIN(precio)` |
| `MAX(col)` | Valor máximo | `MAX(precio)` |

## Relacionado
- [[GROUP_BY]] — uso con agrupamiento
- [[Proyección]] — γ proyecta los atributos resultantes
- [[Funciones_Sistema]] — estas funciones en SQL Server con más categorías
- [[Clase 9 - Procedimientos y Funciones]]
