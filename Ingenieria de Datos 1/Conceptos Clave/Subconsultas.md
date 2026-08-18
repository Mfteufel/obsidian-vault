---
tags: [base-de-datos, sql, subconsultas, sql-server]
aliases: [Subconsultas, Consultas Anidadas, Subquery]
---

# CC_Subconsultas

> Una **subconsulta** es un `SELECT` dentro de otro `SELECT`. Devuelve datos que la consulta externa usa para comparar o filtrar.

## ¿Dónde se puede poner?

- `WHERE` — para filtrar filas
- `HAVING` — para filtrar grupos
- `FROM` — como tabla temporal
- `SELECT` — como columna calculada

---

## Tipos de subconsultas

### Escalar — devuelve un solo valor
```sql
-- Empleado con el salario más alto
SELECT nombre
FROM empleados
WHERE salario = (SELECT MAX(salario) FROM empleados);
```

### Filas múltiples — devuelve varias filas
```sql
-- Empleados con salario igual al de alguien de Ventas
SELECT nombre
FROM empleados
WHERE salario > ANY (SELECT salario FROM empleados WHERE departamento = 'Ventas');
```

### Múltiples columnas — devuelve como tabla (en FROM)
```sql
SELECT nombre, salario
FROM (
    SELECT nombre, salario FROM empleados WHERE departamento = 'Ventas'
) AS empleados_ventas;
```

### Correlacionada — referencia a la consulta externa
```sql
-- Empleados que ganan más que el promedio de su propio departamento
SELECT nombre, salario
FROM empleados e
WHERE salario > (SELECT AVG(salario) FROM empleados WHERE departamento = e.departamento);
```

> ⚠️ Las correlacionadas se ejecutan **una vez por cada fila** de la consulta externa. Pueden ser lentas en tablas grandes.

---

## Operadores de conjunto

| Operador | Significado |
|---|---|
| `IN` | El valor está en el conjunto |
| `NOT IN` | El valor NO está en el conjunto |
| `ANY` | TRUE si al menos uno cumple la condición |
| `ALL` | TRUE si todos cumplen la condición |
| `EXISTS` | TRUE si la subconsulta devuelve al menos una fila |
| `NOT EXISTS` | TRUE si la subconsulta no devuelve ninguna fila |

```sql
-- IN
SELECT nombre FROM empleados
WHERE salario IN (SELECT salario FROM empleados WHERE departamento = 'Ventas');

-- NOT IN
SELECT nombre FROM empleados
WHERE salario NOT IN (SELECT salario FROM empleados WHERE departamento = 'Ventas');

-- ANY
SELECT nombre FROM empleados
WHERE salario > ANY (SELECT salario FROM empleados WHERE departamento = 'Ventas');

-- ALL
SELECT nombre FROM empleados
WHERE salario > ALL (SELECT salario FROM empleados WHERE departamento = 'Ventas');

-- EXISTS
SELECT nombre FROM empleados e
WHERE EXISTS (
    SELECT 1 FROM empleados WHERE departamento = e.departamento HAVING COUNT(*) > 10
);

-- NOT EXISTS
SELECT nombre FROM empleados e
WHERE NOT EXISTS (
    SELECT 1 FROM empleados WHERE departamento = e.departamento HAVING COUNT(*) > 10
);
```

---

## Otros operadores combinables

```sql
-- BETWEEN con subconsultas
SELECT nombre FROM empleados
WHERE salario BETWEEN
    (SELECT MIN(salario) FROM empleados WHERE departamento = 'Ventas')
    AND
    (SELECT MAX(salario) FROM empleados WHERE departamento = 'Ventas');
```

---

## Subconsultas en diferentes cláusulas

```sql
-- En WHERE
SELECT nombre FROM empleados
WHERE salario > (SELECT AVG(salario) FROM empleados);

-- En FROM
SELECT nombre, salario
FROM (
    SELECT nombre, salario FROM empleados
    WHERE salario > (SELECT AVG(salario) FROM empleados)
) AS empleados_filtrados;

-- En SELECT (como columna calculada)
SELECT nombre, salario,
    (SELECT AVG(salario) FROM empleados WHERE departamento = e.departamento)
    AS salario_promedio_departamento
FROM empleados e;
```

---

## Ventajas y limitaciones

| | Descripción |
|---|---|
| ✅ **Modularidad** | Divide consultas complejas en partes manejables |
| ✅ **Flexibilidad** | Se puede usar en múltiples cláusulas |
| ✅ **Reutilización** | Evita duplicar lógica |
| ⚠️ **Rendimiento** | Las correlacionadas pueden ser lentas |
| ⚠️ **Complejidad** | Muchos niveles de anidado son difíciles de leer |

---

## Relacionado

- [[Anti_Join]] — `NOT EXISTS` es la forma recomendada del Anti Join
- [[Vistas]] — las vistas pueden encapsular subconsultas complejas
- [[Clase 7 - Vistas, DCL y Subconsultas]]
