---
tags: [base-de-datos, algebra-relacional, sql-server]
aliases: [Unión, UNION, Union Set]
---

# CC_Unión

> Operación de conjuntos que combina **todas las tuplas** de dos relaciones compatibles, eliminando duplicados.

## Concepto
La unión (∪) retorna todas las filas que aparecen en R, en S, o en ambas.

**Condición de compatibilidad:**
- Mismo número de atributos.
- Tipos de datos compatibles en cada posición.

**Notación formal:**
$$R \cup S$$

## Unión — ∪

**Tabla R**

|nombre|
|---|
|Ana|
|Luis|

**Tabla S**

| nombre |
| ------ |
| Luis   |
| María  |
**Resultado: R ∪ S**

|nombre|
|---|
|Ana|
|Luis|
|María|


## Equivalencia en SQL Server
```sql
SELECT col1, col2 FROM TablaA
UNION
SELECT col1, col2 FROM TablaB;
```
> `UNION ALL` preserva duplicados (más eficiente cuando no importan).

## Ejemplo práctico
Listar todos los emails de clientes y proveedores:

```sql
SELECT email FROM Clientes
UNION
SELECT email FROM Proveedores;
```

## Propiedades
- **Conmutativa:** R ∪ S = S ∪ R
- **Asociativa:** (R ∪ S) ∪ T = R ∪ (S ∪ T)
- Elimina duplicados por defecto.

## Relacionado
- [[Intersección]] — solo tuplas comunes
- [[Diferencia]] — tuplas de R que no están en S
