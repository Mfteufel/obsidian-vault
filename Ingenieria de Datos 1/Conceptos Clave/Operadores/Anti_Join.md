---
tags: [base-de-datos, algebra-relacional, sql-server, joins]
aliases: [Anti Join, Antijoin, Left Anti Join]
---

# CC_Anti Join

> Retorna las tuplas de R que **no tienen ninguna tupla coincidente en S**.

## Concepto
El Anti Join es el complemento del Semi Join. Devuelve solo las filas de R para las cuales no existe ninguna fila relacionada en S.

**Notación formal:**
$$R \bar{\ltimes} S$$

## Anti Join — ▷

**Tabla R**

| id  | nombre |
| --- | ------ |
| 1   | Ana    |
| 2   | Luis   |
| 3   | María  |

**Tabla S**

|id|ciudad|
|---|---|
|1|Córdoba|
|3|Rosario|

**Resultado: R ▷ S** _(filas de R sin match en S)_

|id|nombre|
|---|---|
|2|Luis|

Se puede expresar como: R − (R ⋉ S)

## Equivalencia en SQL Server

### Con NOT EXISTS (recomendado)
```sql
SELECT *
FROM Clientes c
WHERE NOT EXISTS (
    SELECT 1 FROM Pedidos p
    WHERE p.id_cliente = c.id_cliente
);
```

### Con NOT IN (cuidado con NULLs)
```sql
SELECT *
FROM Clientes
WHERE id_cliente NOT IN (
    SELECT id_cliente FROM Pedidos
    WHERE id_cliente IS NOT NULL  -- importante!
);
```

### Con LEFT JOIN + IS NULL
```sql
SELECT c.*
FROM Clientes c
LEFT JOIN Pedidos p ON c.id_cliente = p.id_cliente
WHERE p.id_cliente IS NULL;
```

## Ejemplo práctico
Clientes que **nunca** realizaron un pedido:

```sql
SELECT c.id_cliente, c.nombre
FROM Clientes c
WHERE NOT EXISTS (
    SELECT 1 FROM Pedidos p WHERE p.id_cliente = c.id_cliente
);
```

## Cuidado con NULL ⚠️
`NOT IN` falla silenciosamente si la subconsulta retorna algún `NULL`. Preferir `NOT EXISTS`.

---

## Variantes de ANTI JOIN en SQL (Clase 8)

La forma más directa en SQL es con **LEFT/RIGHT/FULL JOIN + IS NULL**.

### LEFT ANTI JOIN
```sql
-- Empleados sin departamento asignado
SELECT e.Nombre, d.departamento
FROM Empleados e
LEFT JOIN Departamentos d ON e.ID_Dpto = d.ID_Dpto
WHERE d.ID_Dpto IS NULL;
```

### RIGHT ANTI JOIN
```sql
-- Departamentos sin empleados
SELECT e.Nombre, d.departamento
FROM Empleados e
RIGHT JOIN Departamentos d ON e.ID_Dpto = d.ID_Dpto
WHERE e.ID_Dpto IS NULL;
```

### FULL ANTI JOIN
```sql
-- Todo lo que no coincide en ninguno de los dos lados
SELECT e.Nombre, d.departamento
FROM Empleados e
FULL JOIN Departamentos d ON e.ID_Dpto = d.ID_Dpto
WHERE e.ID_Dpto IS NULL OR d.ID_Dpto IS NULL;
```

---

## Relacionado
- [[Semi_Join]] — el opuesto: filas de R con coincidencia
- [[Diferencia]] — operación de conjuntos equivalente
- [[Outer_Join]] — LEFT/RIGHT/FULL JOIN (base de los ANTI JOINs)
- [[Clase 8 - JOINs en SQL]] — nota de clase con resumen general
