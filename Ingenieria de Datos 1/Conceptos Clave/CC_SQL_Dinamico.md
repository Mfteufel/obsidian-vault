---
tags: [sql, sql-server, sql-dinamico, exec, sp-executesql, seguridad]
aliases: [SQL Dinamico, Dynamic SQL, EXEC, sp_executesql]
---

# SQL Dinámico

## ¿Qué es?

Una técnica para **construir y ejecutar consultas SQL en tiempo de ejecución**.

- El SQL estático se conoce en tiempo de compilación
- El SQL dinámico se construye como texto mientras el programa corre

---

## Cuándo usarlo

- Nombre de tabla o columna variable (parámetro)
- Filtros condicionales que cambian según contexto
- Consultas de búsqueda flexible

---

## Dos formas de ejecutar

### 1. `EXEC(@sql)` — simple, pero inseguro

```sql
-- Consulta con SQL dinamico
DECLARE @columna NVARCHAR(50) = 'NombreProducto';
DECLARE @valor   NVARCHAR(100) = 'ProductoA';

-- Construcion dinamica de consulta
DECLARE @sql NVARCHAR(MAX);

SET @sql = 'SELECT * FROM Produccion.C11_Productos WHERE '
         + @columna + ' = ''' + @valor + '''';

EXEC(@sql);
```

> ⚠️ Vulnerable a **SQL Injection** si `@valor` viene del usuario.

---

### 2. `sp_executesql` — parametrizado y seguro ✅

```sql
DECLARE @columna NVARCHAR(50) = 'NombreProducto';
DECLARE @valor   NVARCHAR(100) = 'ProductoA';

DECLARE @sql NVARCHAR(MAX);

SET @sql = 'SELECT * FROM Produccion.C11_Productos WHERE '
         + @columna + ' = @valor';

-- El @valor se pasa como parámetro, no concatenado
EXEC sp_executesql
    @sql,
    N'@valor NVARCHAR(100)',
    @valor;
```

---

## Comparativa: EXEC vs sp_executesql

| Aspecto | `EXEC(@sql)` | `sp_executesql` |
|---------|-------------|----------------|
| Parámetros seguros | ❌ | ✅ |
| SQL Injection | Vulnerable | Protegido |
| Plan de ejecución reutilizado | ❌ | ✅ (mejor performance) |
| Sintaxis | Más simple | Más verbosa |
| **Recomendado para producción** | ❌ | ✅ |

---

## SQL Injection — ¿por qué importa?

```sql
-- Usuario malicioso ingresa como @valor:
-- ProductoA'; DROP TABLE Productos; --

-- Con EXEC, el SQL resultante sería:
SELECT * FROM Productos WHERE Nombre = 'ProductoA'; DROP TABLE Productos; --'
-- ¡Se ejecutaría el DROP TABLE!

-- Con sp_executesql, @valor se trata como dato, no como código
-- El ataque NO funciona ✅
```

---

## Ejemplo — tabla dinámica

```sql
DECLARE @tabla NVARCHAR(100) = 'Produccion.C11_Productos';
DECLARE @sql   NVARCHAR(MAX);

SET @sql = 'SELECT * FROM ' + @tabla;  -- nombre de tabla no puede parametrizarse
EXEC(@sql);

-- Nota: los NOMBRES de tablas/columnas no se pueden parametrizar en sp_executesql
-- Solo los VALORES se parametrizan. Hay que validar nombres manualmente.
```

---

## Pasos para construir SQL dinámico

1. **Declarar** variable `NVARCHAR(MAX)` para la query
2. **Construir** la cadena concatenando partes fijas y variables
3. **Ejecutar** con `EXEC` o `sp_executesql`

---

## Relacionado

[[CC_Cursores]] · [[CC_Transacciones]] · [[CC_CTE]] · [[CC_Esquemas]] · [[Clase 10 - SQL Dinamico]]
