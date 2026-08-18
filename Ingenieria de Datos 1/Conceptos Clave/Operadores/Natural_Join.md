---
tags: [base-de-datos, algebra-relacional, sql-server, joins]
aliases: [Natural Join, Join Natural]
---

# CC_Natural Join

> Join que combina relaciones igualando automáticamente **todos los atributos con el mismo nombre**, eliminando columnas duplicadas.

## Concepto
El Natural Join detecta los atributos comunes entre R y S, aplica igualdad en todos ellos, y retorna una sola copia de esas columnas en el resultado.

**Notación formal:**
$$R \bowtie S$$


## Natural Join — ⋈

**Tabla R**

|id|nombre|
|---|---|
|1|Ana|
|2|Luis|

**Tabla S**

|id|ciudad|
|---|---|
|1|Córdoba|
|3|Rosario|

**Resultado: R ⋈ S** _(une por `id`, columna común)_

|id|nombre|ciudad|
|---|---|---|
|1|Ana|Córdoba|


## Equivalencia en SQL Server
SQL Server no tiene `NATURAL JOIN` nativo. Se simula con:
```sql
SELECT a.id, a.col1, b.col2  -- evitar SELECT * para no duplicar
FROM TablaA a
INNER JOIN TablaB b ON a.atributo_comun = b.atributo_comun;
```

En estándar SQL:
```sql
SELECT * FROM TablaA NATURAL JOIN TablaB;  -- No soportado en SQL Server
```

## Ejemplo práctico
Simular Natural Join entre `Pedidos` y `Clientes` por `id_cliente`:

```sql
SELECT c.id_cliente, c.nombre, p.id_pedido, p.monto
FROM Clientes c
INNER JOIN Pedidos p ON c.id_cliente = p.id_cliente;
```

## Precauciones ⚠️
- Si hay columnas con **mismo nombre pero semántica diferente**, el Natural Join las une incorrectamente.
- Por eso en SQL Server se prefiere el `INNER JOIN` explícito.

## Relacionado
- [[Equi_Join]] — más explícito y controlado
- [[Theta_Join]] — generalización con cualquier condición
