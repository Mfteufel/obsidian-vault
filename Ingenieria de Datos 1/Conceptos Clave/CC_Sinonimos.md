---
tags: [sql, sql-server, sinonimos, alias, abstraccion]
aliases: [Sinonimo, Sinónimos SQL, CREATE SYNONYM]
---

# Sinónimos

## ¿Qué es un sinónimo?

Un **alias** para un objeto de la base de datos.

- El sinónimo apunta a: tablas, vistas, procedimientos, funciones
- El objeto puede estar en la misma BD, otra BD, u otro servidor

---

## Beneficios

| Beneficio | Descripción |
|-----------|-------------|
| **Simplificación** | Nombres cortos y fáciles de recordar |
| **Independencia de ubicación** | Si el objeto se mueve, solo se actualiza el sinónimo |
| **Abstracción** | Las consultas no cambian aunque cambie la estructura interna |

---

## Sintaxis

```sql
-- Crear sinónimo
CREATE SYNONYM nombre_sinonimo
FOR esquema.objeto;

-- Usar el sinónimo exactamente como la tabla original
SELECT * FROM nombre_sinonimo;

-- Eliminar sinónimo
DROP SYNONYM nombre_sinonimo;
```

---

## Ejemplo del curso

```sql
-- Crear sinónimo para tabla en esquema Produccion
CREATE SYNONYM ProductosAlias
FOR Produccion.C11_Productos;

-- Usar el sinónimo
SELECT * FROM ProductosAlias;
-- Equivale a:
SELECT * FROM Produccion.C11_Productos;
```

---

## ¿A qué objetos puede apuntar?

| Tipo de objeto | Local | Otra BD | Otro servidor |
|---------------|-------|---------|--------------|
| Tablas | ✅ | ✅ | ✅ |
| Vistas | ✅ | ✅ | ❌ |
| Procedimientos almacenados | ✅ | ✅ | ❌ |
| Funciones definidas por usuario | ✅ | ✅ | ❌ |

---

## Caso de uso típico

```sql
-- Escenario: renombrar una tabla en producción
-- Sin sinónimo: hay que cambiar TODAS las consultas del sistema

-- Con sinónimo:
-- 1. Renombrar la tabla real
EXEC sp_rename 'Produccion.C11_Productos', 'C11_Productos_v2';

-- 2. Actualizar solo el sinónimo
DROP SYNONYM ProductosAlias;
CREATE SYNONYM ProductosAlias FOR Produccion.C11_Productos_v2;

-- Resultado: todas las consultas que usaban ProductosAlias siguen funcionando ✅
```

---

## Sinónimo vs Alias de columna vs Alias de tabla

| Concepto | Ámbito | Persistencia |
|----------|--------|-------------|
| `CREATE SYNONYM` | Objeto de BD completo | Permanente (hasta DROP) |
| `AS` en SELECT | Columna o tabla en query | Solo esa consulta |
| `ALIAS` de tabla | Tabla en query | Solo esa consulta |

---

## Relacionado

[[CC_Esquemas]] · [[CC_SQL_Dinamico]] · [[Clase 10 - SQL Dinamico]]
