---
tags: [base-de-datos, algebra-relacional, sql-server, joins]
aliases: [Semi Join, Semijoin]
---

# CC_Semi Join

> Retorna las tuplas de R que **tienen al menos una tupla coincidente en S**, pero solo incluye las columnas de R.

## Concepto
El Semi Join es como un Inner Join, pero el resultado solo contiene los atributos de la tabla izquierda. No duplica filas aunque haya múltiples coincidencias en S.

**Notación formal:**
$$R \ltimes S$$


## Semi Join — ⋉

**Tabla R**

| id  | nombre |
| --- | ------ |
| 1   | Ana    |
| 2   | Luis   |
| 3   | María  |

**Tabla S**

| id  | ciudad  |
| --- | ------- |
| 1   | Córdoba |
| 3   | Rosario |

**Resultado: R ⋉ S** _(filas de R que tienen match en S, solo columnas de R)_

|id|nombre|
|---|---|
|1|Ana|
|3|María|


## Equivalencia en SQL Server
No existe `SEMI JOIN` como keyword. Se implementa con:

### Con EXISTS (recomendado)
```sql
SELECT *
FROM Clientes c
WHERE EXISTS (
    SELECT 1 FROM Pedidos p
    WHERE p.id_cliente = c.id_cliente
);
```

### Con IN
```sql
SELECT *
FROM Clientes
WHERE id_cliente IN (SELECT id_cliente FROM Pedidos);
```

## Ejemplo práctico
Clientes que realizaron al menos un pedido:

```sql
SELECT c.id_cliente, c.nombre
FROM Clientes c
WHERE EXISTS (
    SELECT 1 FROM Pedidos p
    WHERE p.id_cliente = c.id_cliente
);
```

## Propiedades
- Solo retorna columnas de **una** de las relaciones.
- No duplica filas (a diferencia del Inner Join).
- Útil en optimización de consultas distribuidas.

## Relacionado
- [[Anti_Join]] — el opuesto: filas de R sin coincidencia en S
- [[Outer_Join]] — retorna también los no coincidentes con NULL
