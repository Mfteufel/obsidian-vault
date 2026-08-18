---
tags: [base-de-datos, algebra-relacional, sql-server]
aliases: [Renombrar, Rename, Rho, ρ]
---

# CC_Renombrar

> Operación que **cambia el nombre** de una relación o de sus atributos sin modificar los datos.

## Concepto
La operación Renombrar (ρ) permite:
1. Darle un nuevo nombre a una relación.
2. Renombrar uno o más atributos.
3. Ambas cosas a la vez.

Es fundamental para resolver **ambigüedades** en el auto-join y para hacer expresiones algebraicas más legibles.

**Notación formal:**
$$\rho_{NuevoNombre}(R)$$
$$\rho_{NuevoNombre(a1, a2, \ldots)}(R)$$


## Renombrar — ρ

**Tabla R**

|id|nombre|
|---|---|
|1|Ana|
|2|Luis|

**Resultado: ρ_Persona(codigo, apellido)(R)**

| codigo | apellido |
| ------ | -------- |
| 1      | Ana      |
| 2      | Luis     |



## Equivalencia en SQL Server

### Alias de tabla
```sql
SELECT e.nombre AS empleado, j.nombre AS jefe
FROM Empleados e
JOIN Empleados j ON e.id_jefe = j.id_empleado;
```

### Alias de columna
```sql
SELECT nombre AS nombre_cliente, email AS correo
FROM Clientes;
```

### Con AS en subconsultas
```sql
SELECT *
FROM (
    SELECT id_cliente, COUNT(*) AS total_pedidos
    FROM Pedidos
    GROUP BY id_cliente
) AS ResumenPedidos;
```

## Casos de uso clave
1. **Auto-join**: Necesitás dos "copias" de la misma tabla con nombres distintos.
2. **Subconsultas**: Darle nombre al resultado de una subquery.
3. **Legibilidad**: Acortar nombres largos en consultas complejas.

## Ejemplo práctico
Auto-join para encontrar empleados y sus jefes:

```sql
SELECT e.nombre AS empleado, j.nombre AS jefe
FROM Empleados AS e
JOIN Empleados AS j ON e.id_jefe = j.id_empleado;
```

## Propiedades
- No modifica los datos, solo los **metadatos** (nombres).
- En álgebra relacional es necesario para evitar ambigüedad en el Producto Cartesiano de una relación consigo misma.

## Relacionado
- [[Proyección]] — puede cambiar el orden de atributos
- [[Producto_Cartesiano]] — renombrar es clave para auto-joins
