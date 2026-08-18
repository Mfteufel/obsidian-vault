---
tags: [base-de-datos, sql, ddl, sql-server]
aliases: [DDL, Data Definition Language, CREATE, ALTER, DROP]
---

# DDL — Data Definition Language

> Define y modifica la **estructura** de los objetos. No opera sobre datos.

## Comandos

| Comando | Para qué sirve |
|---------|---------------|
| `CREATE` | Crear nuevos objetos |
| `ALTER` | Modificar objetos existentes |
| `DROP` | Eliminar objetos permanentemente ⚠️ |

## Objetos que maneja

`DATABASE` · `SCHEMA` · `TABLE` · `VIEW` · `INDEX` · `PROCEDURE` · `FUNCTION` · `TRIGGER` · `USER` · `ROLE`

## CREATE — ejemplos clave

```sql
CREATE DATABASE EmpresaDB;
CREATE SCHEMA Ventas;

CREATE TABLE Empleados (
    IdEmpleado  INT           PRIMARY KEY,
    Nombre      NVARCHAR(50)  NOT NULL,
    Salario     DECIMAL(10,2),
    Genero      CHAR(1)       CHECK (Genero IN ('M','F','O')),
    Email       VARCHAR(100)  UNIQUE,
    FAlta       DATETIME      DEFAULT GETDATE(),
    IdDepto     INT,
    FOREIGN KEY (IdDepto) REFERENCES Departamentos(IdDepto)
);
```

## ALTER — operaciones comunes

| Operación | Sintaxis |
|-----------|----------|
| Agregar columna | `ALTER TABLE T ADD col tipo` |
| Modificar columna | `ALTER TABLE T ALTER COLUMN col tipo` |
| Eliminar columna | `ALTER TABLE T DROP COLUMN col` |
| Renombrar columna | `EXEC sp_rename 'T.col', 'nuevo', 'COLUMN'` |
| Renombrar tabla | `EXEC sp_rename 'T', 'nuevo', 'OBJECT'` |
| Agregar constraint | `ALTER TABLE T ADD CONSTRAINT nombre CHECK (...)` |
| Eliminar constraint | `ALTER TABLE T DROP CONSTRAINT nombre` |

## DROP

```sql
DROP TABLE Empleados;
DROP VIEW VistaVentas;
DROP DATABASE EmpresaDB;
```

## Relacionado
- [[DML]] — opera sobre los datos dentro de las estructuras de DDL
- [[Vistas]] — objeto DDL especial
- [[Restricciones_Modelo_Relacional]]
