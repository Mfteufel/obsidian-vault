---
tags: [base-de-datos, algebra-relacional, sql-server]
aliases: [Producto Cartesiano, Cross Join, Producto Cruz]
---

# CC_Producto Cartesiano

> Combina **cada fila de R con cada fila de S**, generando todas las combinaciones posibles.

## Concepto
El producto cartesiano (×) produce una relación con todos los atributos de R y S, y |R| × |S| tuplas.

**Notación formal:**
$$R \times S$$

Producto Cartesiano — ×
**Tabla R**

|letra|
|---|
|A|
|B|

**Tabla S**

|número|
|---|
|1|
|2|

**Resultado: R × S**

| letra | número |
| ----- | ------ |
| A     | 1      |
| A     | 2      |
| B     | 1      |
| B     | 2      |

## Equivalencia en SQL Server
```sql
-- Uso EXPLÍCITO (recomendado)
SELECT * FROM TablaA CROSS JOIN TablaB;

-- Uso IMPLÍCITO (sin palabra clave)
SELECT * FROM TablaA, TablaB;
```

> ⚠️ Si se agrega un `WHERE` de igualdad al implícito, se comporta como **INNER JOIN**.

## Ejemplo práctico
Combinar todos los productos con todos los proveedores:

```sql
SELECT p.nombre, prov.nombre
FROM Productos
CROSS JOIN Proveedores;
```

## Cuidado ⚠️
Si `Productos` tiene 100 filas y `Proveedores` 50, el resultado tendrá **5000 filas**. Usar con cuidado en tablas grandes.

## Propiedades
- **Conmutativo** en cardinalidad: |R × S| = |S × R|
- Base para construir otros joins (Theta Join, Equi Join, Natural Join).

## Relacionado
- [[CC_Theta Join]] — producto cartesiano + condición de filtrado
- [[CC_Natural Join]] — producto cartesiano + igualdad de atributos comunes
