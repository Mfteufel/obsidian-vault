---
tags: [base-de-datos, sql-server, tsql, funciones]
aliases: [Funciones de usuario, UDF, Función escalar, iTVF, mTVF]
---

# CC_Funciones de Usuario

> Funciones definidas por el usuario que **devuelven un valor o tabla** y pueden usarse dentro de consultas SQL.

## Tipos

| Tipo | Devuelve | Se usa en |
|---|---|---|
| **Escalar** | Un solo valor | `SELECT`, `WHERE`, `JOIN` |
| **iTVF** (inline table-valued) | Una tabla (1 solo SELECT) | `FROM` |
| **mTVF** (multistatement table-valued) | Una tabla (lógica compleja) | `FROM` |

> ❌ Las funciones **no pueden** hacer `INSERT`, `UPDATE`, `DELETE`, ni usar parámetros `OUTPUT`.

---

## Función Escalar

Devuelve un único valor de cualquier tipo de dato.

```sql
CREATE FUNCTION fn_SalarioAnual
    (@Salario DECIMAL(10,2))
RETURNS DECIMAL(10,2)
AS
BEGIN
    RETURN @Salario * 12;
END;

-- Uso
SELECT Nombre, dbo.fn_SalarioAnual(Salario) AS SalarioAnual
FROM Empleados;
```

---

## iTVF — Inline Table-Valued Function

Devuelve una tabla a partir de **un solo SELECT**. Más eficiente que mTVF.

```sql
CREATE FUNCTION fn_EmpleadosPorDpto
    (@ID_Dpto INT)
RETURNS TABLE
AS
RETURN
(
    SELECT Nombre, Salario
    FROM Empleados
    WHERE ID_Departamento = @ID_Dpto
);

-- Uso
SELECT * FROM dbo.fn_EmpleadosPorDpto(3);
```

---

## mTVF — Multistatement Table-Valued Function

Devuelve una tabla definida explícitamente. Permite lógica compleja con múltiples instrucciones.

```sql
CREATE FUNCTION fn_ResumenDpto
    (@ID_Dpto INT)
RETURNS @TablaResultado TABLE
(
    Nombre   NVARCHAR(100),
    Salario  DECIMAL(10,2)
)
AS
BEGIN
    INSERT INTO @TablaResultado
    SELECT Nombre, Salario
    FROM Empleados
    WHERE ID_Departamento = @ID_Dpto;

    RETURN;
END;

-- Uso
SELECT * FROM dbo.fn_ResumenDpto(3);
```

---

## SP vs Función — diferencias clave

| Característica | Procedimiento | Función |
|---|---|---|
| Devuelve | Múltiples resultados o nada | Un valor o una tabla |
| Modifica datos | ✅ INSERT, UPDATE, DELETE | ❌ No permitido |
| Parámetros OUTPUT | ✅ Sí | ❌ No |
| Se usa en SELECT | ❌ No | ✅ Sí |
| Se ejecuta con | `EXEC` | En la consulta directamente |

---

## Relacionado

- [[Procedimientos_Almacenados]] — cuando necesitás modificar datos o lógica más compleja
- [[Funciones_Sistema]] — funciones integradas de SQL Server
- [[Variables_TSQL]] — variables usadas dentro de funciones
- [[Clase 9 - Procedimientos y Funciones]]
