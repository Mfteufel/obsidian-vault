---
tags: [base-de-datos, algebra-relacional, sql-server]
aliases: [Proyección, PROJECT, Filtrado de columnas]
---

# CC_Proyección

> Operación que **filtra columnas** de una relación, descartando los atributos no especificados.

## Concepto
La proyección (π) devuelve una relación con un subconjunto de los atributos originales. Elimina duplicados automáticamente (en álgebra relacional pura).

**Notación formal:**
$$\pi_{\text{atributos}}(R)$$

## Proyección — π

**Tabla R**

| id  | nombre | edad |
| --- | ------ | ---- |
| 1   | Ana    | 30   |
| 2   | Luis   | 22   |
| 3   | María  | 30   |

**Resultado: π_nombre(R)**

| nombre |
| ------ |
| Ana    |
| Luis   |
| María  |



## Equivalencia en SQL Server
```sql
SELECT col1, col2, col3
FROM tabla;
```
> Para eliminar duplicados explícitamente en SQL usar `DISTINCT`.

## Ejemplo práctico
Obtener solo el nombre y email de los clientes:

```sql
SELECT DISTINCT nombre, email
FROM Clientes;
```

## Propiedades
- El resultado puede tener **menos columnas** que la relación original.
- En álgebra relacional elimina duplicados; en SQL hay que usar `DISTINCT`.
- No es conmutativa si los conjuntos de atributos son distintos.

## Relacionado
- [[Selección]] — filtra filas en vez de columnas
- [[Renombrar]] — cambia el nombre de atributos
