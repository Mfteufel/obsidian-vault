---
tags: [base-de-datos, sql-server, tsql, variables]
aliases: [Variables, DECLARE, Variables T-SQL]
---

# CC_Variables T-SQL

> Objetos que almacenan **valores temporales** durante la ejecución de un script o procedimiento.

## Reglas de nomenclatura

- Siempre empiezan con `@`
- El segundo carácter: letra (A-Z) o guión bajo `_`
- Longitud máxima: 128 caracteres
- Sin espacios ni caracteres especiales (`%`, `$`, `&`)
- No pueden ser palabras reservadas (`SELECT`, `WHERE`, etc.)

---

## Declarar y asignar

```sql
-- Declarar
DECLARE @PrecioMin DECIMAL(10,2);

-- Asignar con SET (un valor escalar)
SET @PrecioMin = 50.00;

-- Asignar con SELECT (desde consulta)
SELECT @PrecioMin = MIN(Precio) FROM Productos;

-- Usar en consulta
SELECT Nombre, Precio FROM Productos WHERE Precio > @PrecioMin;
```

---

## SET vs SELECT — ¿cuándo usar cada uno?

| Situación | SET | SELECT |
|---|---|---|
| Valor directo | ✅ Correcto | ✅ Correcto |
| Una variable por vez | ✅ Lo habitual | ✅ También puede |
| Varias variables a la vez | ❌ No | ✅ Sí |
| Consulta devuelve 1 fila, 1 col | ✅ Asigna | ✅ Asigna |
| Consulta devuelve 1 fila, varias cols | ❌ Error (salvo SET por separado) | ✅ Correcto |
| Consulta sin filas | Variable → `NULL` | Conserva valor anterior |
| Consulta con varias filas | ❌ Error | Queda el último valor procesado |

> ⚠️ Usar `SET` cuando se espera un único valor escalar. Usar `SELECT` para asignar varias variables desde una misma fila.

---

## Variables de sistema (@@)

Variables predefinidas por SQL Server. Comienzan con `@@`.

| Variable | Descripción |
|---|---|
| `@@ERROR` | Código de error de la última instrucción |
| `@@ROWCOUNT` | Filas afectadas por la última instrucción |
| `@@IDENTITY` | Último valor de identidad generado |
| `@@TRANCOUNT` | Número de transacciones activas |
| `@@VERSION` | Versión de SQL Server |
| `@@SPID` | ID del proceso de la sesión actual |
| `@@SERVERNAME` | Nombre del servidor |
| `@@LANGUAGE` | Idioma actual de la sesión |
| `@@MAX_CONNECTIONS` | Máximo de conexiones simultáneas |
| `@@CPU_BUSY` | Tiempo de CPU usado desde inicio |
| `@@TOTAL_READ` | Lecturas de disco desde inicio |
| `@@TOTAL_WRITE` | Escrituras en disco desde inicio |

```sql
-- Ejemplo: capturar error con @@ERROR
BEGIN TRY
    UPDATE Productos SET Precio = Precio / 0 WHERE ID_Producto = 1;
END TRY
BEGIN CATCH
    PRINT 'Código de error: ' + CAST(@@ERROR AS NVARCHAR(10));
END CATCH;
```

---

## Relacionado

- [[Estructuras_Control_TSQL]] — IF, WHILE, TRY/CATCH que usan variables
- [[Procedimientos_Almacenados]] — las variables se usan dentro de SPs
- [[Clase 9 - Procedimientos y Funciones]]
