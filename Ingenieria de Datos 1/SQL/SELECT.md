---
tags: [sql, dml, sql-server]
aliases: [SELECT, Consulta SQL]
---

# SELECT

> Recupera datos de una o más tablas y los devuelve como tabla resultado.

## Sintaxis completa

```sql
SELECT { ALL | DISTINCT } [TOP n] { * | columna | expresión [AS alias] }
FROM { tabla | vista }
[WHERE condición]
[GROUP BY columna]
[HAVING condición_grupo]
[ORDER BY columna { ASC | DESC }]
```

## Cláusulas

| Cláusula | Rol | Ejecución |
|----------|-----|:---------:|
| `FROM` | Origen de datos | 1 |
| `WHERE` | Filtro de filas | 2 |
| `GROUP BY` | Agrupamiento | 3 |
| `HAVING` | Filtro de grupos | 4 |
| `SELECT` | Columnas resultado | 5 |
| `ORDER BY` | Orden | 6 |

## Ejemplos

```sql
-- Todo
SELECT * FROM Empleados;

-- Sin duplicados
SELECT DISTINCT Departamento FROM Empleados;

-- Top 5 con alias y expresión
SELECT TOP 5 Nombre, Salario * 12 AS SalarioAnual
FROM Empleados ORDER BY Salario DESC;

-- WHERE con distintos operadores
SELECT Nombre FROM Empleados WHERE Salario BETWEEN 3000 AND 5000;
SELECT Nombre FROM Empleados WHERE Nombre LIKE 'A%';
SELECT Nombre FROM Empleados WHERE Telefono IS NULL;
SELECT Nombre FROM Empleados WHERE IdDepto IN (1, 2);

-- Subconsulta escalar
SELECT Nombre FROM Empleados
WHERE Salario > (SELECT AVG(Salario) FROM Empleados);

-- JOIN
SELECT e.Nombre, d.NombreDepartamento
FROM Empleados e
INNER JOIN Departamentos d ON e.IdDepartamento = d.IdDepartamento;

-- GROUP BY + HAVING
SELECT IdDepartamento, COUNT(*) AS Total
FROM Empleados
GROUP BY IdDepartamento
HAVING COUNT(*) > 1;
```

## Expresiones válidas en SELECT

| Tipo | Ejemplo |
|------|---------|
| Columna simple | `Nombre` |
| Alias | `Salario AS Sueldo` |
| Expresión aritmética | `Salario * 12` |
| Agregación | `COUNT(*), SUM(Salario)` |
| Lógica condicional | `CASE WHEN Salario > 3000 THEN 'Alto' ELSE 'Bajo' END` |
| Función de fila | `ROW_NUMBER() OVER (ORDER BY Salario DESC)` |
| Subconsulta | `(SELECT MAX(Salario) FROM Empleados)` |

## Relacionado
- [[DML]]
- [[GROUP_BY]]
- [[Vistas]]
