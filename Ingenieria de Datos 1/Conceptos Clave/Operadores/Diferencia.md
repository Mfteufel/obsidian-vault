---
tags: [base-de-datos, algebra-relacional, sql-server]
aliases: [Diferencia, EXCEPT, Minus, Set Difference]
---

# CC_Diferencia

> Operación de conjuntos que retorna las tuplas que están en R **pero no en S**.

## Concepto
La diferencia (−) devuelve las filas de la primera relación que no aparecen en la segunda.

**Notación formal:**
$$R - S$$

## Diferencia — −

**Tabla R**

|nombre|
|---|
|Ana|
|Luis|
|María|

**Tabla S**

|nombre|
|---|
|Luis|

**Resultado: R − S**

|nombre|
|---|
|Ana|
|María|

---

## Equivalencia en SQL Server
```sql
SELECT col1, col2 FROM TablaA
EXCEPT
SELECT col1, col2 FROM TablaB;
```

## Ejemplo práctico
Clientes que nunca realizaron un pedido:

```sql
SELECT id_cliente FROM Clientes
EXCEPT
SELECT id_cliente FROM Pedidos;
```

## Propiedades
- **No es conmutativa:** R − S ≠ S − R
- Requiere compatibilidad de esquemas (igual que Unión e Intersección).
- R − S ⊆ R siempre.

## Relacionado
- [[Unión]] — combina ambas relaciones
- [[Intersección]] — solo tuplas en común
- [[CC_Anti Join]] — diferencia basada en condición de join
