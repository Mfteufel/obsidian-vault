---
tags: [base-de-datos, algebra-relacional, sql-server, joins]
aliases: [Equi Join, Equijoin, Inner Join por igualdad]
---

# CC_Equi Join

> Caso especial del Theta Join donde la condición θ es **exclusivamente de igualdad** (`=`).

## Concepto
El Equi Join combina tuplas de R y S donde un atributo de R es igual a un atributo de S.

**Notación formal:**
$$R \bowtie_{R.a = S.b} S$$

## Equi Join — ⋈=

**Tabla R**

| id  | nombre |
| --- | ------ |
| 1   | Ana    |
| 2   | Luis   |
| 3   | María  |

**Tabla S**

| id_r | ciudad  |
| ---- | ------- |
| 1    | Córdoba |
| 3    | Rosario |

**Resultado: R ⋈ R.id = S.id_r S**

|id|nombre|id_r|ciudad|
|---|---|---|---|
|1|Ana|1|Córdoba|
|3|María|3|Rosario|



A diferencia del Natural Join, **mantiene ambas columnas** aunque tengan el mismo valor.

## Equivalencia en SQL Server
```sql
SELECT *
FROM TablaA a
INNER JOIN TablaB b ON a.id = b.id_a;
```

## Ejemplo práctico
Pedidos con su cliente correspondiente:

```sql
SELECT p.id_pedido, c.nombre, p.monto
FROM Pedidos p
INNER JOIN Clientes c ON p.id_cliente = c.id_cliente;
```

## Diferencia con Natural Join
| Aspecto | Equi Join | Natural Join |
|---|---|---|
| Columnas iguales | Se mantienen ambas | Se fusionan en una |
| Explícito | Sí (`ON`) | No (automático) |
| Control | Mayor | Menor |

## Relacionado
- [[Theta_Join]] — generalización con cualquier condición
- [[Natural_Join]] — caso más restrictivo del Equi Join
