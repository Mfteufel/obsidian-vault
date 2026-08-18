---
tags: [base-de-datos, algebra-relacional, sql-server]
aliases: [División, Division, Relational Division]
---

# CC_División

> Operación que retorna las tuplas de R que están **relacionadas con TODAS las tuplas de S**.

## Concepto
La División (÷) responde preguntas del tipo *"¿cuáles X están relacionados con TODOS los Y?"*

**Notación formal:**
$$R \div S$$

## División — ÷

**Tabla R**


|alumno|materia|
|---|---|
|Ana|Mate|
|Ana|Física|
|Luis|Mate|
|María|Mate|
|María|Física|

**Tabla S**

|materia|
|---|
|Mate|
|Física|

**Resultado: R ÷ S** _(alumnos que cursaron TODAS las materias de S)_

|alumno|
|---|
|Ana|
|María|



Si R(A, B) y S(B), entonces R ÷ S devuelve los valores de A que aparecen combinados con **todos** los valores de B en R.

## Equivalencia en SQL Server
SQL Server no tiene operador de división nativo. Se implementa con doble negación:

```sql
-- ¿Qué clientes compraron TODOS los productos?
SELECT DISTINCT id_cliente
FROM Pedidos p1
WHERE NOT EXISTS (
    SELECT id_producto FROM Productos pr
    WHERE NOT EXISTS (
        SELECT 1 FROM Pedidos p2
        WHERE p2.id_cliente = p1.id_cliente
          AND p2.id_producto = pr.id_producto
    )
);
```

## Ejemplo práctico
Proveedores que suministran **todos** los productos del catálogo:

```sql
SELECT id_proveedor
FROM Suministros s
WHERE NOT EXISTS (
    SELECT id_producto FROM Productos p
    WHERE NOT EXISTS (
        SELECT 1 FROM Suministros s2
        WHERE s2.id_proveedor = s.id_proveedor
          AND s2.id_producto = p.id_producto
    )
);
```

## Equivalencia algebraica
$$R \div S = \pi_A(R) - \pi_A((\pi_A(R) \times S) - R)$$

## Cuándo usarla
- Preguntas con cuantificador universal: *"todos"*, *"cada uno"*, *"siempre"*.

## Relacionado
- [[Producto_Cartesiano]] — parte de la definición algebraica
- [[Diferencia]] — usada en la implementación
- [[Proyección]] — usada en la implementación
