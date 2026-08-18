---
tags: [clase, sql, tsql, procedimientos, funciones, sql-server]
---
![[IDD I_Clase09_AFB.pdf]]

# Clase 9 — Procedimientos y Funciones

**Objetivo:** Crear procedimientos almacenados y funciones para operaciones complejas, y entender estructuras de control en T-SQL.

---

## Identificadores y Variables

- **Identificador** = nombre de un objeto en la BD (tabla, columna, vista, etc.)
- **Variable** = almacena valores temporales durante la ejecución. Siempre empieza con `@`

```sql
DECLARE @PrecioMin DECIMAL(10,2);
SET @PrecioMin = 50.00;
SELECT Nombre, Precio FROM Productos WHERE Precio > @PrecioMin;
```

**SET vs SELECT para asignar variables:**

| Situación | SET | SELECT |
|---|---|---|
| Valor directo | ✅ | ✅ |
| Varias variables a la vez | ❌ | ✅ |
| Consulta sin filas | Variable → NULL | Conserva valor anterior |
| Consulta con varias filas | ❌ Error | Queda el último valor |
| Uso recomendado | Un solo valor escalar | Varias variables desde una fila |

---

## Bloques de código (Párrafos)

| Tipo | Descripción |
|---|---|
| **Implícito** | Sin `BEGIN/END`, solo 1 instrucción |
| **Explícito** | Con `BEGIN ... END`, múltiples instrucciones |

---

## Estructuras de control

| Estructura | Para qué sirve |
|---|---|
| `IF ... ELSE` | Ejecutar código según condición |
| `CASE` sencillo | Compara una expresión con varios valores |
| `CASE` de búsqueda | Evalúa condiciones independientes |
| `WHILE` | Repite un bloque mientras la condición sea verdadera |
| `BREAK` / `CONTINUE` | Salir del bucle / saltar a la siguiente iteración |
| `TRY ... CATCH` | Capturar y manejar errores |

---

## Procedimientos Almacenados

Conjunto de sentencias SQL guardadas en el servidor. Se ejecutan con `EXEC`.

- Reutilizables, compilados, con parámetros de entrada/salida
- Máx. 2 GB de código — Máx. 2100 parámetros
- Pueden hacer `INSERT`, `UPDATE`, `DELETE`
- Se modifican con `ALTER`, se eliminan con `DROP`

```sql
CREATE PROCEDURE sp_ObtenerCliente @ID_Cliente INT
AS BEGIN
    SELECT NombreCliente, Ciudad FROM Clientes WHERE ID_Cliente = @ID_Cliente;
END;

EXEC sp_ObtenerCliente @ID_Cliente = 5;
```

---

## Funciones de usuario

Diseñadas para **devolver un valor** y usarse dentro de consultas.

| Tipo | Devuelve | Se usa en |
|---|---|---|
| **Escalar** | Un solo valor | `SELECT`, `WHERE` |
| **iTVF** (inline) | Una tabla | `FROM` |
| **mTVF** (multideclarativa) | Una tabla (con lógica compleja) | `FROM` |

> ❌ Las funciones **no pueden** hacer `INSERT`, `UPDATE`, `DELETE` ni usar parámetros `OUTPUT`.

---

## Funciones del sistema

**Ver detalles completos en:** [[Funciones_Sistema]]

| Categoría | Ejemplos clave |
|---|---|
| Agregado | `SUM()`, `AVG()`, `COUNT()`, `MIN()`, `MAX()` |
| Cadena | `LEN()`, `UPPER()`, `LOWER()`, `REPLACE()`, `SUBSTRING()` |
| Fecha/Hora | `GETDATE()`, `DATEADD()`, `DATEDIFF()`, `YEAR()` |
| Conversión | `CAST()`, `CONVERT()`, `TRY_CAST()` |
| Matemáticas | `ROUND()`, `ABS()`, `SQRT()`, `SIGN()` |
| Categorías | `ROW_NUMBER()`, `RANK()`, `DENSE_RANK()`, `NTILE()` |
| Analíticas | `LAG()`, `LEAD()`, `FIRST_VALUE()`, `LAST_VALUE()` |
| Configuración | `@@ERROR`, `@@ROWCOUNT`, `@@VERSION`, `@@SERVERNAME` |

---

## Conceptos Clave relacionados

- [[Procedimientos_Almacenados]] — estructura completa, parámetros, ALTER/DROP
- [[Funciones_Usuario]] — escalar, iTVF, mTVF con ejemplos
- [[Funciones_Sistema]] — todas las categorías con ejemplos
- [[Estructuras_Control_TSQL]] — IF/ELSE, CASE, WHILE, TRY/CATCH
- [[Variables_TSQL]] — DECLARE, SET vs SELECT, variables de sistema @@
