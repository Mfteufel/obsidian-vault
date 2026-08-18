---
tags: [clase, sql, joins, sql-server]
---
![[IDD I_Clase08_AFB.pdf]]

# Clase 8 — JOINs en SQL

**Tema:** Metodologías de relacionamiento entre tablas
**Objetivo:** Dominar los tipos de JOIN para combinar datos de múltiples tablas.

---

## ¿Qué es un JOIN?

Un JOIN **combina filas de dos o más tablas** usando una condición de relación.

---

## Tipos de JOIN

| JOIN | ¿Qué devuelve? |
|---|---|
| **CROSS JOIN** | Todas las combinaciones posibles |
| **INNER JOIN** | Solo filas con coincidencia en ambas tablas |
| **LEFT JOIN** | Todas las filas de la tabla izquierda + coincidencias |
| **RIGHT JOIN** | Todas las filas de la tabla derecha + coincidencias |
| **FULL OUTER JOIN** | Todas las filas de ambas tablas |
| **SELF JOIN** | Una tabla unida consigo misma |

Los que devuelven filas **sin coincidencia** muestran `NULL` en los campos faltantes.

---

## Variantes ANTI JOIN

Se construyen agregando `WHERE ... IS NULL` sobre un OUTER JOIN.
Devuelven **solo las filas sin coincidencia**.

| Variante | ¿Qué devuelve? |
|---|---|
| **LEFT ANTI JOIN** | Filas de la izquierda **sin match** a la derecha |
| **RIGHT ANTI JOIN** | Filas de la derecha **sin match** a la izquierda |
| **FULL ANTI JOIN** | Filas sin match en ninguno de los dos lados |

---

## Cláusula ON — tipos de condición

La condición del JOIN va en el `ON`. Puede ser:

- **Igualdad** → `ON e.ID_Dpto = d.ID_Dpto`
- **Comparación** → `ON p.Precio > s.PrecioReferencia`
- **Múltiple** → `ON e.ID_Dpto = d.ID_Dpto AND e.Ciudad = d.Ciudad`
- **Función** → `ON UPPER(e.Nombre) = UPPER(d.NombreJefe)`
- **Subconsulta** → `ON e.ID_Dpto = (SELECT MAX(ID_Dpto) FROM Departamentos)`
- **Nulos** → `ON e.ID_Dpto = d.ID_Dpto AND d.ID_Dpto IS NOT NULL`

---

## HINTS — Plan de ejecución

Los **HINTS** le dicen a SQL Server qué algoritmo usar para ejecutar el JOIN.
Reemplazan la decisión automática del optimizador.

```sql
SELECT * FROM Productos p
LEFT OUTER HASH JOIN Stock s ON p.ID_Producto = s.ID_Producto;
```

| HINT | Algoritmo |
|---|---|
| `LOOP` | Nested Loops |
| `HASH` | Hash Join |
| `MERGE` | Sort Merge Join |

---

## Conceptos Clave relacionados

- [[Outer_Join]] — LEFT, RIGHT y FULL JOIN con detalle y ejemplos
- [[Anti_Join]] — LEFT/RIGHT/FULL ANTI JOIN
- [[Producto_Cartesiano]] — CROSS JOIN
- [[Equi_Join]] — INNER JOIN por igualdad
- [[Theta_Join]] — JOIN con cualquier condición
- [[Self_Join]] — tabla unida consigo misma (jerarquías)
