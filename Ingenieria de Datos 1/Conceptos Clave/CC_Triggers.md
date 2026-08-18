---
tags: [sql, sql-server, triggers, automatizacion, auditoria, eventos]
aliases: [Trigger, Triggers SQL, disparador, disparadores]
---

# Triggers

## ¿Qué es un trigger?

Un objeto especial que se ejecuta **automáticamente** cuando ocurre un evento en una tabla o vista.

- Eventos: `INSERT`, `UPDATE`, `DELETE`
- Se usan para automatizar reglas de negocio o auditorías

---

## Tipos de triggers

| Tipo | Cuándo se ejecuta | Uso típico |
|------|------------------|-----------|
| `AFTER` | **Después** del evento | Auditoría, actualizaciones en cascada |
| `INSTEAD OF` | **En lugar** del evento | Validaciones, vistas actualizables |

---

## Tablas virtuales disponibles dentro del trigger

| Tabla | Contenido |
|-------|-----------|
| `INSERTED` | Filas nuevas (en INSERT y UPDATE) |
| `DELETED` | Filas eliminadas o anteriores (en DELETE y UPDATE) |

> En un `UPDATE`, `INSERTED` tiene los valores nuevos y `DELETED` los valores anteriores.

---

## Sintaxis básica

```sql
CREATE TRIGGER nombre_trigger
ON esquema.tabla
AFTER INSERT  -- o UPDATE, DELETE, o combinaciones
AS
BEGIN
    -- lógica del trigger
    -- se puede usar INSERTED y DELETED
END;
```

---

## Ejemplo del curso — Trigger de auditoría

```sql
CREATE TRIGGER trg_AfterInsert_Ventas
ON Ventas.C11_RegistroVentas
AFTER INSERT
AS
BEGIN
    -- Copia los datos insertados a la tabla de auditoría
    INSERT INTO Auditoria.AuditoriaVentas (ID_Producto, CantidadVendida, FechaVenta)
    SELECT ID_Producto, CantidadVendida, FechaVenta
    FROM INSERTED;

    PRINT 'Registro insertado en la tabla de auditoria.';
END;
```

---

## Ejemplo — Trigger INSTEAD OF (validación)

```sql
CREATE TRIGGER trg_ValidarStock
ON Ventas.C11_RegistroVentas
INSTEAD OF INSERT
AS
BEGIN
    -- Verificar stock antes de insertar
    IF EXISTS (
        SELECT 1
        FROM INSERTED i
        JOIN Produccion.C11_Productos p ON i.ID_Producto = p.ID_Producto
        WHERE i.CantidadVendida > p.CantidadStock
    )
    BEGIN
        PRINT 'Error: stock insuficiente.';
        RETURN;
    END;

    -- Si pasa la validación, insertar
    INSERT INTO Ventas.C11_RegistroVentas (ID_Producto, CantidadVendida, FechaVenta)
    SELECT ID_Producto, CantidadVendida, FechaVenta
    FROM INSERTED;
END;
```

---

## Trigger para múltiples eventos

```sql
CREATE TRIGGER trg_Auditoria_ABM
ON esquema.tabla
AFTER INSERT, UPDATE, DELETE
AS
BEGIN
    -- lógica que diferencia el tipo de operación
    IF EXISTS (SELECT * FROM INSERTED) AND EXISTS (SELECT * FROM DELETED)
        PRINT 'UPDATE detectado';
    ELSE IF EXISTS (SELECT * FROM INSERTED)
        PRINT 'INSERT detectado';
    ELSE
        PRINT 'DELETE detectado';
END;
```

---

## Buenas prácticas

- Mantenerlos **simples y rápidos** — se ejecutan dentro de la transacción que los disparó
- Evitar lógica de negocio compleja — usar procedimientos almacenados si es necesario
- Nombramiento claro: `trg_[AfterInsert/AfterUpdate/InsteadOf]_[Tabla]`

---

## Relacionado

[[CC_Transacciones]] · [[CC_Esquemas]] · [[CC_Cursores]] · [[Clase 10 - SQL Dinamico]]
