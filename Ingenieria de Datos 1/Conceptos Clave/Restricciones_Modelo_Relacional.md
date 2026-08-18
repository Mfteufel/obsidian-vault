---
tags: [base-de-datos, modelo-relacional, sql-server]
aliases: [Restricciones, Constraints, Integridad]
---

# Restricciones del Modelo Relacional

> Reglas que garantizan la **integridad y consistencia** de los datos en una base de datos relacional.

## Resumen de restricciones

| # | Restricción | Descripción | SQL Server |
|---|-------------|-------------|------------|
| 1 | **Dominio** | Controla los tipos de datos permitidos en un atributo | `INT`, `VARCHAR`, `DATE`... |
| 2 | **Clave Primaria** | Identifica de forma única cada fila. No puede repetirse | `PRIMARY KEY` |
| 3 | **Clave Foránea** | Mantiene la integridad referencial entre tablas | `FOREIGN KEY` |
| 4 | **Integridad de Entidad** | La clave primaria no puede ser NULL | implícito en `PRIMARY KEY` |
| 5 | **Integridad Referencial** | El valor de una FK debe existir en la tabla referenciada | `FOREIGN KEY ... REFERENCES` |
| 6 | **Unicidad** | Asegura valores únicos en un atributo que no es PK | `UNIQUE` |
| 7 | **CHECK** | Impone condiciones sobre los valores permitidos | `CHECK (condición)` |
| 8 | **NOT NULL** | Evita que una columna tenga valores NULL | `NOT NULL` |
| 9 | **DEFAULT** | Define un valor por defecto si no se especifica uno | `DEFAULT valor` |

## Ejemplo en SQL Server

```sql
CREATE TABLE Empleados (
    emp_ID   INT           PRIMARY KEY,          -- PK + Integridad de Entidad
    nombre   VARCHAR(100)  NOT NULL,              -- NOT NULL
    email    VARCHAR(200)  UNIQUE,                -- Unicidad
    edad     INT           CHECK (edad >= 18),    -- CHECK
    dpt_ID   INT           DEFAULT 101,           -- DEFAULT
    FOREIGN KEY (dpt_ID) REFERENCES Dptos(dpt_ID) -- FK + Integridad Referencial
);
```

## Tipos de claves

| Tipo | Descripción |
|------|-------------|
| **Superclave** | Cualquier conjunto de atributos que identifica unívocamente una tupla |
| **Clave candidata** | Superclave mínima (sin atributos redundantes) |
| **Clave primaria (PK)** | Clave candidata elegida como identificador principal |
| **Clave foránea (FK)** | Atributo que referencia la PK de otra tabla |

## Relacionado
- [[Selección]] — las condiciones de selección respetan dominios
- [[Equi_Join]] — los joins suelen hacerse sobre FK = PK
