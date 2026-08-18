---
tags: [base-de-datos, sql-server, tsql, procedimientos]
aliases: [Procedimiento Almacenado, Stored Procedure, SP]
---

# CC_Procedimientos Almacenados

> Conjunto de sentencias SQL **guardadas en el servidor** que se ejecutan como una unidad con `EXEC`.

## Características

- **Reutilizable** — se llama desde cualquier parte
- **Compilado** — SQL Server lo optimiza al crearlo
- **Parámetros** — entrada y salida
- **Seguridad** — se otorga permiso de ejecución sin dar acceso directo a las tablas
- Máx. **2 GB** de código — Máx. **2100 parámetros**
- Puede hacer `INSERT`, `UPDATE`, `DELETE` (a diferencia de las funciones)

---

## Estructura completa

```sql
CREATE PROCEDURE NombreProcedimiento
    @parametro1 tipo_de_dato,
    @parametro2 tipo_de_dato = 'valor_default',  -- valor por defecto
    @resultado  tipo_de_dato OUTPUT               -- parámetro de salida
    [ WITH ENCRYPTION ]   -- oculta el código fuente
    [ FOR REPLICATION ]   -- solo para replicación
AS
BEGIN
    -- instrucciones SQL + T-SQL
    RETURN [entero];  -- opcional: retorna código de estado
END;
```

---

## Ejemplo básico

```sql
CREATE PROCEDURE sp_ObtenerCliente
    @ID_Cliente INT
AS
BEGIN
    SELECT NombreCliente, Ciudad
    FROM Clientes
    WHERE ID_Cliente = @ID_Cliente;
END;
```

---

## Ejecución

```sql
-- Por posición
EXEC sp_ObtenerCliente 5;

-- Por nombre de parámetro (orden no importa)
EXEC sp_ObtenerCliente @ID_Cliente = 5;
```

> ⚠️ Si se usa el nombre del parámetro explícitamente, el orden no importa. Sin nombre, el orden sí importa.

---

## Modificar y eliminar

```sql
-- Modificar (requiere declaración completa)
ALTER PROCEDURE sp_ObtenerCliente 
@ID_Cliente INT
AS BEGIN
    SELECT NombreCliente, Ciudad, Telefono  -- versión actualizada
    FROM Clientes WHERE ID_Cliente = @ID_Cliente;
END;

-- Eliminar
DROP PROCEDURE sp_ObtenerCliente;
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

- [[Funciones_Usuario]] — alternativa para devolver valores en consultas
- [[Variables_TSQL]] — variables usadas dentro del SP
- [[Estructuras_Control_TSQL]] — IF, WHILE, TRY/CATCH dentro del SP
- [[DCL]] — GRANT EXECUTE para dar permisos de ejecución
- [[Clase 9 - Procedimientos y Funciones]]
