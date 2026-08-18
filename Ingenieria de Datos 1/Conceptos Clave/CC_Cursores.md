---
tags: [sql, sql-server, cursores, iteracion, fila-por-fila]
aliases: [Cursor, Cursores SQL, CURSOR SQL Server]
---

# Cursores

## ¿Qué es un cursor?

Un cursor permite recorrer los resultados de una consulta **fila por fila**.

Se usa cuando necesitás operaciones detalladas que no se pueden hacer en bloque con una sola sentencia SQL.

> ⚠️ Los cursores son lentos comparados con operaciones en conjunto. Usarlos solo cuando es necesario.

---

## Ciclo de vida — 6 pasos

```
DECLARE → OPEN → FETCH (1°) → WHILE (procesar + FETCH NEXT) → CLOSE → DEALLOCATE
```

| Paso | Comando | Qué hace |
|------|---------|----------|
| 1 | `DECLARE ... CURSOR FOR` | Define la consulta del cursor |
| 2 | `OPEN` | Abre el cursor y ejecuta la query |
| 3 | `FETCH NEXT ... INTO` | Lee la primera fila |
| 4 | `WHILE @@FETCH_STATUS = 0` | Bucle mientras haya filas |
| 5 | `CLOSE` | Cierra el cursor |
| 6 | `DEALLOCATE` | Libera los recursos |

---

## Sintaxis completa

```sql
-- 1. Declarar variables para recibir valores
DECLARE @ID_Producto INT;
DECLARE @CantidadStock INT;

-- 2. Declarar el cursor
DECLARE productos_cursor CURSOR FOR
    SELECT ID_Producto, CantidadStock
    FROM Produccion.C11_Productos
    WHERE CantidadStock > 100;

-- 3. Abrir
OPEN productos_cursor;

-- 4. Leer primera fila
FETCH NEXT FROM productos_cursor INTO @ID_Producto, @CantidadStock;

-- 5. Procesar fila a fila
WHILE @@FETCH_STATUS = 0
BEGIN
    -- Operación sobre la fila actual
    UPDATE Produccion.C11_Productos
    SET CantidadStock = CantidadStock - (@CantidadStock * 0.1)
    WHERE ID_Producto = @ID_Producto;

    -- Leer siguiente fila
    FETCH NEXT FROM productos_cursor INTO @ID_Producto, @CantidadStock;
END;

-- 6. Cerrar y liberar
CLOSE productos_cursor;
DEALLOCATE productos_cursor;
```

---

## @@FETCH_STATUS — valores posibles

| Valor | Significado |
|-------|-------------|
| `0` | FETCH exitoso — fila disponible |
| `-1` | No quedan más filas |
| `-2` | La fila ya no es válida (fue alterada) |

---

## Opciones del cursor

### 1. Movimiento de filas

| Opción | Descripción |
|--------|-------------|
| `FORWARD_ONLY` | Solo hacia adelante (default) |
| `SCROLL` | Permite ir en cualquier dirección |

### 2. Acceso a datos

| Opción | Descripción |
|--------|-------------|
| `STATIC` | Copia estática — no refleja cambios en la tabla |
| `DYNAMIC` | Refleja cambios en tiempo real (default) |
| `KEYSET` | Refleja solo modificaciones (no inserciones/eliminaciones) |

### 3. Concurrencia

| Opción         | Descripción                               |
| -------------- | ----------------------------------------- |
| `READ_ONLY`    | No se puede modificar (default)           |
| `SCROLL_LOCKS` | Bloquea filas para modificar              |
| `OPTIMISTIC`   | No bloquea, pero da error si hubo cambios |

### 4. Combinación especial

| Opción | Descripción |
|--------|-------------|
| `FAST_FORWARD` | `FORWARD_ONLY` + `READ_ONLY` optimizado |

---

## Tabla resumen de combinaciones

| Movimiento | Tipo de datos | Concurrencia | Descripción |
|------------|--------------|-------------|-------------|
| `FORWARD_ONLY` | `STATIC` | `READ_ONLY` | Solo avanza, copia fija, sin modificación |
| `SCROLL` | `DYNAMIC` | `SCROLL_LOCKS` | Cualquier dirección, cambios en tiempo real, con bloqueo |
| `FAST_FORWARD` | (implícito) | (implícito) | Solo avanza, optimizado, sin modificación |
| `FORWARD_ONLY` | `KEYSET` | `OPTIMISTIC` | Solo avanza, refleja cambios en filas seleccionadas, sin bloqueo |

---

## Opciones de FETCH

```sql
FETCH [ NEXT | PRIOR | FIRST | LAST | ABSOLUTE n | RELATIVE n ]
FROM nombre_cursor
INTO @var1, @var2;
```

| Opción | Descripción | Requiere SCROLL |
|--------|-------------|----------------|
| `NEXT` | Siguiente fila (default) | No |
| `PRIOR` | Fila anterior | Sí |
| `FIRST` | Primera fila | Sí |
| `LAST` | Última fila | Sí |
| `ABSOLUTE n` | Fila número n (positivo desde inicio, negativo desde fin) | No |
| `RELATIVE n` | n posiciones desde la fila actual | No |

---

## Ejemplo con opciones avanzadas

```sql
-- Cursor que refleja cambios y permite modificar filas
DECLARE productos_cursor CURSOR DYNAMIC SCROLL_LOCKS FOR
    SELECT ID_Producto, CantidadStock
    FROM Produccion.C11_Productos;
```

---

## Relacionado

[[CC_Transacciones]] · [[CC_CTE]] · [[CC_SQL_Dinamico]] · [[Clase 10 - SQL Dinamico]]
