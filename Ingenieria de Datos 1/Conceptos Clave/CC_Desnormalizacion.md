---
tags: [sql, sql-server, modelado, desnormalizacion, rendimiento, vistas, triggers]
aliases: [Desnormalizacion, Denormalizacion, Desnormalizar]
---

# Desnormalización

## ¿Qué es?

> El proceso **inverso** a la normalización: introducir **redundancia intencional** para mejorar el rendimiento de las consultas.

Se logra **combinando tablas** o **agregando datos redundantes** → menos `JOIN` necesarios.

---

## ¿Cuándo se usa?

- Consultas lentas por **múltiples JOINs**
- Bases de **lectura intensiva** (más lecturas que escrituras)
- **Data Warehousing / BI** — prioridad: velocidad de consulta

## ¿Cuándo NO?

- Bases **altamente transaccionales** (prioridad: integridad y escritura)
- Bases **pequeñas** con tiempos de consulta aceptables

---

## Normalización vs Desnormalización

| Aspecto | Normalización | Desnormalización |
|---------|--------------|------------------|
| Integridad | Garantiza consistencia | Riesgo de inconsistencias |
| Rendimiento | Más lenta (JOINs) | Más rápida en lecturas |
| Espacio | Menor | Mayor (datos duplicados) |
| Mantenimiento | Más fácil | Más complejo |

---

## Técnicas

- **Duplicar datos** — ej: nombre del cliente en la tabla de pedidos
- **Precalcular agregados** — ej: guardar el total de ventas
- **Fusionar tablas** — ej: Cliente + Pedido en una sola
- **Columnas redundantes** — ej: nombre del producto en Detalle_Pedido

---

## Desnormalización con Vistas

Permite consolidar datos **sin alterar el esquema** normalizado.

```sql
CREATE VIEW Vista_Desnormalizada AS
SELECT
    P.ID_Pedido,
    P.Fecha,
    C.ID_Cliente,
    C.Nombre AS Nombre_Cliente,
    PR.Nombre_Producto,
    DP.Cantidad,
    PR.Precio,
    DP.Cantidad * PR.Precio AS Total
FROM Pedido P
JOIN Cliente C ON P.ID_Cliente = C.ID_Cliente
JOIN Detalle_Pedido DP ON P.ID_Pedido = DP.ID_Pedido
JOIN Producto PR ON DP.ID_Producto = PR.ID_Producto;

-- Consulta simple, sin JOINs para el usuario:
SELECT * FROM Vista_Desnormalizada WHERE ID_Cliente = 1;
```

| Enfoque | Ventaja | Desventaja |
|---------|---------|------------|
| **Vista dinámica** | Siempre actualizada, no ocupa disco | Calcula en cada consulta |
| **Vista materializada** | Resultados precalculados → rápida | Ocupa espacio, requiere sincronización |

> ⚠️ **SQL Server no tiene vistas materializadas nativas.** La alternativa es una **vista indexada** (índice clustered sobre la vista).

---

## Mantener una tabla desnormalizada con Trigger

```sql
CREATE TRIGGER Trigger_Update_Desnormalizada
ON Detalle_Pedido
AFTER INSERT, UPDATE, DELETE
AS
BEGIN
    -- Eliminar registros obsoletos
    DELETE FROM Tabla_Desnormalizada
    WHERE ID_Pedido IN (SELECT ID_Pedido FROM DELETED);

    -- Insertar registros nuevos o actualizados
    INSERT INTO Tabla_Desnormalizada
    SELECT
        P.ID_Pedido, P.Fecha,
        C.ID_Cliente, C.Nombre,
        DP.ID_Producto, PR.Nombre_Producto,
        DP.Cantidad, PR.Precio,
        DP.Cantidad * PR.Precio AS Total
    FROM Detalle_Pedido DP
    JOIN Pedido P ON DP.ID_Pedido = P.ID_Pedido
    JOIN Cliente C ON P.ID_Cliente = C.ID_Cliente
    JOIN Producto PR ON DP.ID_Producto = PR.ID_Producto
    WHERE DP.ID_Pedido IN (SELECT ID_Pedido FROM INSERTED);
END;
```

- ✅ Sincronización automática, en tiempo real
- ❌ Ralentiza INSERT/UPDATE/DELETE en las tablas base

**Alternativa:** Job periódico en SQL Server Agent (`TRUNCATE` + `INSERT` cada hora/noche) — menor impacto en tiempo real, más simple.

---

## Alternativas antes de desnormalizar

- **Índices** sobre las columnas de búsqueda
- **Vistas indexadas** (precálculo)
- **Caching** de resultados frecuentes

---

## Relacionado

[[Vistas]] · [[CC_Triggers]] · [[Redundancia]] · [[CC_Anomalias_BD]] · [[CC_1FN]] · [[Clase 13 - Formas Normales Superiores y Modelo ER]]
