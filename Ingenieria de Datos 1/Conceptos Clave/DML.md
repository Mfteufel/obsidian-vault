---
tags: [base-de-datos, sql, dml, sql-server]
aliases: [DML, Data Manipulation Language, INSERT, UPDATE, DELETE, SELECT]
---

# DML — Data Manipulation Language

> Opera directamente sobre los **datos** de las tablas.

## Comandos

| Comando | Para qué sirve |
|---------|---------------|
| `SELECT` | Consultar y recuperar datos |
| `INSERT` | Agregar nuevas filas |
| `UPDATE` | Modificar filas existentes |
| `DELETE` | Eliminar filas |

## INSERT

```sql
-- Especificando columnas (el resto toma DEFAULT o NULL)
INSERT INTO Empleados (IdEmpleado, Nombre) VALUES (1, 'Carlos López');

-- Todas las columnas en orden
INSERT INTO Empleados VALUES (2, 'Ana Gómez', NULL, 1, 3, NULL);
```

> El tipo de dato debe coincidir con la columna, si no → **ERROR**

## DELETE

```sql
DELETE FROM Empleados WHERE IdEmpleado = 3;
-- ⚠️ Sin WHERE elimina TODOS los registros
DELETE FROM Empleados;
```

## UPDATE

```sql
UPDATE Empleados SET Salario = Salario + 500 WHERE IdEmpleado = 3;
-- ⚠️ Sin WHERE actualiza TODAS las filas
UPDATE Empleados SET Salario = 3000;
```

## SELECT — orden de ejecución

| Orden escritura | Orden ejecución | Cláusula | Rol |
|:-:|:-:|----------|-----|
| 5 | 1 | `FROM` | Origen de datos |
| 6 | 2 | `WHERE` | Filtro de filas |
| 3 | 3 | `GROUP BY` | Agrupamiento |
| 4 | 4 | `HAVING` | Filtro de grupos |
| 1 | 5 | `SELECT` | Columnas resultado |
| 2 | 6 | `ORDER BY` | Orden |

```sql
SELECT DISTINCT TOP 5 Nombre, Salario * 12 AS SalarioAnual
FROM Empleados
WHERE Salario > 3000
ORDER BY Salario DESC;
```

## Operadores WHERE

| Operador | Ejemplo |
|----------|---------|
| Comparación | `Salario > 3000` |
| Rango | `Salario BETWEEN 3000 AND 5000` |
| Patrón | `Nombre LIKE 'A%'` |
| Nulo | `Telefono IS NULL` |
| Lista | `IdDepto IN (1, 2, 3)` |
| Subconsulta | `Salario > (SELECT AVG(Salario) FROM Empleados)` |

## Relacionado
- [[DDL]] — crea las estructuras donde DML opera
- [[SELECT]] — detalle de la cláusula SELECT
- [[GROUP_BY]] — agrupamiento y agregación
