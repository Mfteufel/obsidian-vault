---
tags: [base-de-datos, algebra-relacional, sql-server]
aliases: [Selección, SELECT, Filtrado de filas]
---

# CC_Selección

> Operación del álgebra relacional que **filtra filas** de una relación según una condición.

## Concepto
La selección (σ) devuelve un subconjunto de tuplas de una tabla que cumplen un predicado dado. **No modifica las columnas**, solo filtra filas.

**Notación formal:**
$$\sigma_{\text{condición}}(R)$$


## Selección — σ

**Tabla R**

|id|nombre|edad|
|---|---|---|
|1|Ana|30|
|2|Luis|22|
|3|María|30|

**Resultado: σ_edad=30(R)**

|id|nombre|edad|
|---|---|---|
|1|Ana|30|
|3|María|30|





## Equivalencia en SQL Server
```sql
SELECT *
FROM tabla
WHERE condición;
```

## Ejemplo práctico
Obtener los pedidos cuyo monto supera los $1000:

```sql
SELECT *
FROM Pedidos
WHERE monto > 1000;
```

## Propiedades
- El resultado tiene el **mismo esquema** que la relación original.
- La condición puede combinar predicados con `AND`, `OR`, `NOT`.
- Es **conmutativa** con otra selección: σ_A(σ_B(R)) = σ_B(σ_A(R)).
- Se puede combinar: σ_{A ∧ B}(R) = σ_A(σ_B(R)).

## Relacionado
- [[Proyección]] — filtra columnas en vez de filas
- [[CC_Theta Join]] — combina selección con producto cartesiano
