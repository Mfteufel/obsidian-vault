---
tags: [sql, sql-server, transacciones, acid, concurrencia, integridad]
aliases: [Transaccion, Transacciones SQL, BEGIN TRANSACTION, COMMIT, ROLLBACK]
---

# Transacciones

## ¿Qué es una transacción?

Un conjunto de operaciones SQL que se ejecutan como **unidad atómica**.

> **Todo o nada:** si algo falla, se deshace todo.

---

## ACID

| Letra | Propiedad | Qué garantiza |
|-------|-----------|---------------|
| **A** | Atomicidad | Todo o nada |
| **C** | Consistencia | Solo se guardan datos válidos |
| **I** | Aislamiento | Las transacciones no se afectan entre sí |
| **D** | Durabilidad | Los datos confirmados no se pierden |

---

## Comandos principales

| Comando | Qué hace |
|---------|----------|
| `BEGIN TRANSACTION` | Marca el inicio |
| `COMMIT` | Confirma los cambios (permanentes) |
| `ROLLBACK` | Deshace todos los cambios desde el inicio |

---

## Estructura recomendada con TRY...CATCH

```sql
BEGIN TRY
    BEGIN TRANSACTION;

        UPDATE Produccion.C11_Productos
        SET CantidadStock = CantidadStock - 50
        WHERE ID_Producto = 1;

        INSERT INTO Ventas.C11_RegistroVentas (CantidadVendida, FechaVenta)
        VALUES (1,50, GETDATE());
	-- Si todo salio bien, confirma transaccion
    COMMIT;
    PRINT 'Transaccion completada con exito.';
END TRY
BEGIN CATCH
	-- Si ocurre un error, revierte los cambios
    ROLLBACK;
    -- Mostrar mensaje de error
    PRINT 'Error en la transaccion.';
    PRINT ERROR_MESSAGE();
END CATCH;
```

---

## Tipos de transacciones

| Tipo | Descripción | Ejemplo |
|------|-------------|---------|
| **Autocommit** | Cada sentencia es una transacción sola | Comportamiento por defecto |
| **Implícita** | Se inicia automáticamente, se cierra con `COMMIT`/`ROLLBACK` | `SET IMPLICIT_TRANSACTIONS ON` |
| **Explícita** | Se declara manualmente con `BEGIN TRAN` | Más común en producción |

```sql
-- Autocommit (default)
INSERT INTO Productos VALUES (1, 'Mouse', 2000); -- COMMIT automático

-- Implícita
SET IMPLICIT_TRANSACTIONS ON;
INSERT INTO Productos VALUES (2, 'Teclado', 3000); -- no confirma sola
COMMIT TRAN; -- cierre manual

-- Explícita
BEGIN TRAN
    INSERT INTO Productos VALUES (3, 'Monitor', 7000);
    UPDATE Stock SET Cantidad = Cantidad - 1 WHERE ID_Producto = 3;
COMMIT TRAN
```

---

## Opciones avanzadas

| Tipo | Opción | Descripción |
|------|--------|-------------|
| Punto intermedio | `SAVE TRAN Punto1` | Crea punto de control — permite rollback parcial |
| Reversión parcial | `ROLLBACK TRAN Punto1` | Revierte hasta el punto guardado |
| Abortar automático | `SET XACT_ABORT ON` | SQL Server hace ROLLBACK automático ante cualquier error |
| Chequeo de estado | `@@TRANCOUNT` | Cantidad de transacciones activas anidadas |
| Estado | `XACT_STATE()` | `1` = activa · `-1` = inválida · `0` = no hay transacción |

---

## Niveles de aislamiento

Controlan **qué tan aislada** está la transacción de otras concurrentes.

| Nivel | Bloquea | Permite | Comando |
|-------|---------|---------|---------|
| `READ UNCOMMITTED` | Nada | Lee datos no confirmados (dirty reads) | `SET TRANSACTION ISOLATION LEVEL READ UNCOMMITTED` |
| `READ COMMITTED` | Lecturas sucias | Lecturas no repetibles | `SET TRANSACTION ISOLATION LEVEL READ COMMITTED` |
| `REPEATABLE READ` | Sucias + no repetibles | Phantom reads | `SET TRANSACTION ISOLATION LEVEL REPEATABLE READ` |
| `SERIALIZABLE` | Todo lo anterior | Ningún problema | `SET TRANSACTION ISOLATION LEVEL SERIALIZABLE` |
| `SNAPSHOT` | Usa versión de fila | Requiere habilitar en la BD | `SET TRANSACTION ISOLATION LEVEL SNAPSHOT` |

> **Default en SQL Server:** `READ COMMITTED`

---



### Anomalías de lectura (Clase 13 — repaso final)

| Anomalía | Qué pasa |
|----------|----------|
| **Lectura sucia** (Dirty Read) | Una transacción lee datos que **otra aún no confirmó** con COMMIT |
| **Lectura no repetible** (Non-Repeatable Read) | Dos lecturas del **mismo dato** dan resultados distintos porque otra transacción lo **modificó** en el medio |
| **Lectura fantasma** (Phantom Read) | La misma consulta devuelve **filas nuevas** porque otra transacción **insertó** datos entre ambas ejecuciones |

**Qué anomalía permite cada nivel:**

| Nivel | Sucia | No repetible | Fantasma |
|-------|-------|-------------|----------|
| READ UNCOMMITTED | ❌ Sí | ❌ Sí | ❌ Sí |
| READ COMMITTED | ✅ No | ❌ Sí | ❌ Sí |
| REPEATABLE READ | ✅ No | ✅ No | ❌ Sí |
| SERIALIZABLE | ✅ No | ✅ No | ✅ No |

## Ejemplo con nivel de aislamiento

```sql
SET XACT_ABORT ON; --Fuerza rollback automatico ante error
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;
-- Nivel por defecto

BEGIN TRY
    BEGIN TRANSACTION ActualizarStock -- Se puede nombrar
		
		-- Sentencias SQL aqui
        UPDATE Inventario SET Stock = Stock - 10 WHERE ID_Producto = 100;
        INSERT INTO Ventas VALUES (100, 10, GETDATE());

    COMMIT TRANSACTION ActualizarStock
END TRY
BEGIN CATCH
    ROLLBACK TRANSACTION ActualizarStock
    PRINT 'Error: ' + ERROR_MESSAGE()
END CATCH
```

---

## Relacionado

[[CC_Cursores]] · [[CC_Triggers]] · [[CC_SQL_Dinamico]] · [[Clase 10 - SQL Dinamico]]
