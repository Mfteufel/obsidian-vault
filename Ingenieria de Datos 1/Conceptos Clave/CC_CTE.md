---
tags: [sql, sql-server, cte, consultas, funciones]
aliases: [CTE, Common Table Expression, expresión de tabla común]
---

# CTE — Common Table Expression

## ¿Qué es?

Un **CTE** es una consulta temporal con nombre.

- Existe **solo durante la ejecución de la sentencia que le sigue**
- No se guarda en disco ni en memoria persistente
- Es como una "tabla temporal" que vive un instante

---

## Sintaxis básica

```sql
WITH nombre_cte AS (
    SELECT columnas
    FROM tabla
    WHERE condicion
)
SELECT *
FROM nombre_cte;
```

---

## Ejemplo del curso

```sql
CREATE FUNCTION fn_Top5Clientes(@Año INT)
RETURNS @Resultado TABLE (ID_Cliente INT, Nombre NVARCHAR(100), Total MONEY)
AS
BEGIN
    -- Paso 1: calcular totales con CTE
    WITH VentasPorCliente AS (
        SELECT v.ID_Cliente, c.Nombre, SUM(v.Monto) AS Total
        FROM Ventas v
        INNER JOIN Clientes c ON v.ID_Cliente = c.ID_Cliente
        WHERE YEAR(v.Fecha) = @Año
        GROUP BY v.ID_Cliente, c.Nombre
    )
    -- Paso 2: filtrar top 5
    INSERT INTO @Resultado
    SELECT TOP 5 ID_Cliente, Nombre, Total
    FROM VentasPorCliente
    WHERE Total > 0
    ORDER BY Total DESC;

    RETURN;
END;
```

---

## ¿Cuándo usar CTE?

| Situación | ¿Usar CTE? |
|-----------|-----------|
| Subconsulta repetida más de una vez | ✅ Sí |
| Necesito legibilidad / paso a paso | ✅ Sí |
| TOP N por grupo | ✅ Sí |
| Consulta recursiva (jerarquías) | ✅ Sí |
| Cálculo de una sola vez simple | ❌ Mejor subquery directa |

---

## CTE vs Subquery

| | CTE | Subquery |
|---|-----|---------|
| Legibilidad | Alta | Baja (anidada) |
| Reutilización en la misma query | ✅ | ❌ |
| Rendimiento | Similar | Similar |
| Recursividad | ✅ | ❌ |

---

## Relacionado

[[CC_Cursores]] · [[CC_SQL_Dinamico]] · [[Clase 10 - SQL Dinamico]]
