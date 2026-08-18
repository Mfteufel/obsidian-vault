---
tags: [base-de-datos, sql, vistas, ddl, sql-server]
aliases: [Vistas, VIEW, Vista SQL]
---

# Vistas (VIEW)

> Consulta predefinida guardada en la BD que se comporta como **tabla virtual**. No almacena datos, los lee al consultarla.

## Sintaxis

```sql
CREATE VIEW identificador AS
SELECT columnas
FROM tabla
WHERE condición;

-- Consultar
SELECT * FROM identificador;
```

## Tipos

| Tipo | Descripción | Ejemplo |
|------|-------------|---------|
| **Simple** | Una tabla, sin agregaciones | Filtro de empleados de Ventas |
| **Compleja** | JOINs, GROUP BY, subconsultas | Ventas por empleado |
| **Indexada** | Con índice físico (`WITH SCHEMABINDING`) | Para consultas frecuentes |
| **Columnas calculadas** | Expresiones en el SELECT | Salario anual = Salario × 12 |

## Ejemplos

```sql
-- Simple
CREATE VIEW VistaEmpleados AS
SELECT Nombre, Apellido, Salario FROM Empleados
WHERE Departamento = 'Ventas';

-- Compleja
CREATE VIEW VistaVentas AS
SELECT e.Nombre, SUM(v.Cantidad) AS TotalVentas
FROM Empleados e
JOIN Ventas v ON e.IdEmpleado = v.IdEmpleado
GROUP BY e.Nombre;

-- Con columna calculada
CREATE VIEW VistaSalarioAnual AS
SELECT Nombre, Salario, Salario * 12 AS SalarioAnual
FROM Empleados;
```

## Ventajas

| Ventaja | Descripción |
|---------|-------------|
| Simplificación | Oculta consultas complejas |
| Seguridad | Expone solo las columnas necesarias |
| Organización | Abstrae la estructura real |
| Rendimiento | Las indexadas almacenan resultado físicamente |

## Para ser actualizable debe

- Basarse en **una sola tabla**
- No tener `GROUP BY`, `DISTINCT`, `HAVING`
- No tener funciones de agregación (`SUM`, `COUNT`...)
- No tener subconsultas
- Incluir la **clave primaria**

## Optimización de consultas

1. Usar **Execution Plan** (`Ctrl+M` en SSMS)
2. Evitar `SELECT *` y subconsultas innecesarias
3. Crear **índices** en columnas filtradas con frecuencia
4. Usar `TOP` para limitar resultados
5. Usar índices compuestos si se filtra por varias columnas

## Ejemplo avanzado — Vista con múltiples LEFT JOINs

```sql
-- Vista integrada de recursos humanos
CREATE VIEW RecursosHumanos.vw_EmpleadosConDatos AS
SELECT
    E.IdEmpleado,
    E.Nombre AS NombreEmpleado,
    E.Salario,
    D.NombreDepartamento,
    C.NombreCategoria,
    H.NombreHijo,
    H.FechaNacimiento,
    ES.Descripcion AS TituloEstudio
FROM RecursosHumanos.Empleados E
LEFT JOIN RecursosHumanos.Departamento D ON E.IdDepartamento = D.IdDepartamento
LEFT JOIN RecursosHumanos.Categoria C ON E.IdCategoria = C.IdCategoria
LEFT JOIN RecursosHumanos.Hijos H ON E.IdEmpleado = H.IdEmpleado
LEFT JOIN RecursosHumanos.Estudios ES ON E.IdEmpleado = ES.IdEmpleado;

-- Ejecutar la vista
SELECT * FROM RecursosHumanos.vw_EmpleadosConDatos;
```

> Se usa `LEFT JOIN` para no perder empleados que no tengan hijos o estudios cargados.

---

## Relacionado
- [[DDL]] — las vistas se crean con DDL
- [[DML]] — se consultan con SELECT
- [[DCL]] — permisos de acceso a las vistas
- [[Clase 7 - Vistas, DCL y Subconsultas]]
