---
tags: [sql, sql-server, esquemas, organizacion, permisos, seguridad]
aliases: [Esquema, Schema, CREATE SCHEMA]
---

# Esquemas (Schemas)

## ¿Qué es un esquema?

Un **contenedor lógico** que agrupa objetos de la base de datos.

- Agrupa: tablas, vistas, procedimientos, funciones, triggers
- Funciona como un "namespace" para los objetos

> Ejemplo: `Produccion.Productos` → esquema `Produccion`, tabla `Productos`

---

## Beneficios

| Beneficio | Descripción |
|-----------|-------------|
| **Organización** | Separa objetos por área o módulo |
| **Seguridad** | Se asignan permisos a nivel de esquema entero |
| **Flexibilidad** | Usuarios tienen sus propios objetos sin interferir |
| **Despliegue controlado** | Se mueven objetos entre esquemas sin renombrarlos |

---

## Sintaxis

```sql
-- Crear esquema
CREATE SCHEMA nombre_esquema;

-- Crear tabla dentro del esquema
CREATE TABLE nombre_esquema.nombre_tabla (
    columna1 INT,
    columna2 VARCHAR(100)
);

-- Dar permisos sobre todo el esquema
GRANT SELECT ON SCHEMA::nombre_esquema TO usuario;
```

---

## Ejemplos del curso

```sql
-- Los ejemplos de la clase usan esquemas como:
SELECT * FROM Produccion.C11_Productos;
SELECT * FROM Ventas.C11_RegistroVentas;
SELECT * FROM Auditoria.AuditoriaVentas;
```

---

## Esquema por defecto: `dbo`

Si no se especifica esquema, SQL Server usa `dbo` (database owner).

```sql
-- Estas dos líneas son equivalentes si el esquema por defecto es dbo:
SELECT * FROM Productos;
SELECT * FROM dbo.Productos;
```

---

## Relación con permisos (GRANT / REVOKE)

```sql
-- Dar permiso SELECT sobre todo el esquema Ventas al usuario juan
GRANT SELECT ON SCHEMA::Ventas TO juan;

-- Quitar el permiso
REVOKE SELECT ON SCHEMA::Ventas FROM juan;
```

---

## Diagrama conceptual

```
Base de Datos
├── Esquema: dbo
│   └── Tabla: Clientes
├── Esquema: Produccion
│   ├── Tabla: C11_Productos
│   └── Vista: v_StockActual
└── Esquema: Ventas
    ├── Tabla: C11_RegistroVentas
    └── Procedimiento: sp_RegistrarVenta
```

---

## Relacionado

[[CC_Sinonimos]] · [[CC_Triggers]] · [[CC_Transacciones]] · [[Clase 10 - SQL Dinamico]]
