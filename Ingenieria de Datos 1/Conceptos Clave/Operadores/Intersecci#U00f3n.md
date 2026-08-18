---
tags: [base-de-datos, algebra-relacional, sql-server]
aliases: [Intersección, INTERSECT, Set Intersection]
---

# CC_Intersección

> Operación de conjuntos que retorna **solo las tuplas comunes** a ambas relaciones.

## Concepto
La intersección (∩) devuelve las filas que aparecen tanto en R como en S.

**Notación formal:**
$$R \cap S$$


**Tabla R**

|nombre|
|---|
|Ana|
|Luis|

**Tabla S**

|nombre|
|---|
|Luis|
|María|

**Resultado: R ∩ S**

|nombre|
|---|
|Luis|


Se puede expresar con diferencia: R ∩ S = R − (R − S)

## Equivalencia en SQL Server
```sql
SELECT col1, col2 FROM TablaA
INTERSECT
SELECT col1, col2 FROM TablaB;
```

## Ejemplo práctico
Clientes que también son proveedores (mismo email):

```sql
SELECT email FROM Clientes
INTERSECT
SELECT email FROM Proveedores;
```

## Propiedades
- **Conmutativa:** R ∩ S = S ∩ R
- **Asociativa:** (R ∩ S) ∩ T = R ∩ (S ∩ T)
- R ∩ S ⊆ R y R ∩ S ⊆ S

## Relacionado
- [[Unión]] — todas las tuplas de ambas
- [[Diferencia]] — tuplas en R pero no en S
