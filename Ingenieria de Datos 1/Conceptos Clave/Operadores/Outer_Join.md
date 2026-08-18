---
tags: [base-de-datos, algebra-relacional, sql-server, joins]
aliases: [Outer Join, Left Join, Right Join, Full Join, Join Externo]
---

# CC_Outer Join

> Join que preserva **tuplas sin coincidencia**, rellenando con `NULL` los atributos faltantes.

## Concepto
A diferencia del Inner Join, el Outer Join no descarta filas que no tienen pareja en la otra tabla. Existen tres variantes:

| Variante | Preserva |
|---|---|
| LEFT OUTER JOIN | Todas las filas de la tabla izquierda |
| RIGHT OUTER JOIN | Todas las filas de la tabla derecha |
| FULL OUTER JOIN | Todas las filas de ambas tablas |

**Notación formal:**
$$R ⟕ S \quad \text{(Left Outer Join)}$$
$$R ⟖ S \quad \text{(Right Outer Join)}$$
$$R ⟗ S \quad \text{(Full Outer Join)}$$


## Outer Join

**Tabla R**

|id|nombre|
|---|---|
|1|Ana|
|2|Luis|
|3|María|

**Tabla S**

|id|ciudad|
|---|---|
|1|Córdoba|
|4|Mendoza|


**LEFT OUTER JOIN**

|id|nombre|ciudad|
|---|---|---|
|1|Ana|Córdoba|
|2|Luis|NULL|
|3|María|NULL|

**RIGHT OUTER JOIN**

|id|nombre|ciudad|
|---|---|---|
|1|Ana|Córdoba|
|4|NULL|Mendoza|

**FULL OUTER JOIN**

|id|nombre|ciudad|
|---|---|---|
|1|Ana|Córdoba|
|2|Luis|NULL|
|3|María|NULL|
|4|NULL|Mendoza|



## Equivalencia en SQL Server

### Left Outer Join
```sql
SELECT c.nombre, p.id_pedido
FROM Clientes c
LEFT JOIN Pedidos p ON c.id_cliente = p.id_cliente;
-- Incluye clientes SIN pedidos (id_pedido = NULL)
```

### Right Outer Join
```sql
SELECT c.nombre, p.id_pedido
FROM Clientes c
RIGHT JOIN Pedidos p ON c.id_cliente = p.id_cliente;
```

### Full Outer Join
```sql
SELECT c.nombre, p.id_pedido
FROM Clientes c
FULL OUTER JOIN Pedidos p ON c.id_cliente = p.id_cliente;
```

## Cuándo usarlo
- **LEFT JOIN**: Obtener todos los registros de la tabla principal aunque no tengan relación.
- **FULL JOIN**: Detectar registros huérfanos en ambas tablas.

---

## ANTI JOINs — filtrar los que NO coinciden

Agregando `WHERE ... IS NULL` se convierten en **ANTI JOINs**.
Devuelven **solo las filas sin coincidencia**.

### LEFT ANTI JOIN
```sql
SELECT e.Nombre, d.departamento
FROM Empleados e
LEFT JOIN Departamentos d ON e.ID_Dpto = d.ID_Dpto
WHERE d.ID_Dpto IS NULL;
-- Resultado: solo empleados SIN departamento
```

### RIGHT ANTI JOIN
```sql
SELECT e.Nombre, d.departamento
FROM Empleados e
RIGHT JOIN Departamentos d ON e.ID_Dpto = d.ID_Dpto
WHERE e.ID_Dpto IS NULL;
-- Resultado: solo departamentos SIN empleados
```

### FULL ANTI JOIN
```sql
SELECT e.Nombre, d.departamento
FROM Empleados e
FULL JOIN Departamentos d ON e.ID_Dpto = d.ID_Dpto
WHERE e.ID_Dpto IS NULL OR d.ID_Dpto IS NULL;
-- Resultado: filas sin match en ninguno de los dos lados
```

---

## Ejemplo real — LEFT JOIN con Pedidos y Clientes

```sql
-- LEFT JOIN: todos los pedidos, con o sin cliente
SELECT Pedidos.ID_Pedido, Pedidos.FechaPedido,
       Clientes.NombreCliente, Pedidos.ID_Sucursal
FROM Pedidos
LEFT JOIN Clientes ON Pedidos.ID_Cliente = Clientes.ID_Cliente;

-- LEFT ANTI JOIN: pedidos SIN cliente registrado
SELECT Pedidos.ID_Pedido, Pedidos.FechaPedido,
       Clientes.NombreCliente, Pedidos.ID_Sucursal
FROM Pedidos
LEFT JOIN Clientes ON Pedidos.ID_Cliente = Clientes.ID_Cliente
WHERE Clientes.ID_Cliente IS NULL;
```

---

## Relacionado
- [[Equi_Join]] — versión sin NULLs (solo coincidencias)
- [[Semi_Join]] — similar al Left Join pero solo retorna columnas de un lado
- [[Anti_Join]] — retorna los que NO tienen coincidencia
- [[Clase 8 - JOINs en SQL]] — nota de clase con resumen de todos los tipos
