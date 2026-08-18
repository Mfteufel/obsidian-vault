---
tags: [base-de-datos, sql-server, agregacion, dml]
aliases: [GROUP BY, Agrupamiento, HAVING]
---

# GROUP BY / HAVING

> Agrupa filas con el mismo valor en una o más columnas para aplicar funciones de agregación.

## Sintaxis

```sql
SELECT columna, FUNCION_AGREGACION(col)
FROM tabla
GROUP BY columna
[HAVING condición_sobre_grupo];
```

## Reglas importantes

- Toda columna en `SELECT` que **no** sea una función de agregación **debe** estar en `GROUP BY`
- Se puede agrupar por más de una columna
- `HAVING` filtra **grupos** (equivalente al `WHERE` pero post-agrupamiento)

## Diferencia WHERE vs HAVING

|                         | WHERE              | HAVING               |
| ----------------------- | ------------------ | -------------------- |
| Filtra                  | Filas individuales | Grupos               |
| Se ejecuta              | Antes del GROUP BY | Después del GROUP BY |
| Puede usar agregaciones | ❌ No               | ✅ Sí                 |

## Ejemplos

```sql
-- Ventas por empleado
SELECT IdEmpleado, COUNT(IdVenta) AS TotalVentas, SUM(Cantidad) AS TotalCantidad
FROM Ventas
GROUP BY IdEmpleado;

-- Agrupamiento por dos columnas
SELECT IdEmpleado, IdProducto, COUNT(IdVenta) AS Total
FROM Ventas
GROUP BY IdEmpleado, IdProducto;

-- Con HAVING: solo empleados con más de 1 venta
SELECT IdEmpleado, COUNT(IdVenta) AS TotalVentas
FROM Ventas
GROUP BY IdEmpleado
HAVING COUNT(IdVenta) > 1;

-- Con ORDER BY
SELECT IdDepartamento, AVG(Salario) AS Promedio
FROM Empleados
GROUP BY IdDepartamento
ORDER BY Promedio DESC;
```

## Orden de ejecución completo

```
FROM → WHERE → GROUP BY → HAVING → SELECT → ORDER BY
```

## Relacionado
- [[Operadores_Agregacion]] — COUNT, SUM, AVG, MIN, MAX
- [[SELECT]]
- [[DML]]
