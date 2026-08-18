---
tags: [base-de-datos, clase, sql, ddl, dml]
aliases: [Clase 6, SQL]
---

![[IDD I_Clase06_AFB.pdf]]


# Clase 06 — SQL: Structured Query Language

**Materia:** Ingeniería de Datos I
**Profesor:** Alfonso Fernández Buttera
**Módulo:** 3 — SQL y Seguridad

---

## ¿Qué es SQL?

Lenguaje **declarativo** estándar (ISO/ANSI) para gestionar bases de datos relacionales. Describe *qué* se quiere, no *cómo* hacerlo. En SQL Server se extiende con **T-SQL** (extensión procedural).

## Sublenguajes

| Sublenguaje | Qué hace                     | Comandos principales                   |
| ----------- | ---------------------------- | -------------------------------------- |
| **DDL**     | Define y modifica estructura | `CREATE`, `ALTER`, `DROP`              |
| **DML**     | Manipula datos               | `SELECT`, `INSERT`, `UPDATE`, `DELETE` |
| **DCL**     | Controla permisos            | `GRANT`, `REVOKE`                      |
| **TCL**     | Controla transacciones       | `COMMIT`, `ROLLBACK`, `SAVEPOINT`      |

---

## DDL — Data Definition Language

Define y modifica la **estructura** de los objetos. No opera sobre datos.

### CREATE

Crea nuevos objetos: `DATABASE`, `SCHEMA`, `TABLE`, `VIEW`, `INDEX`, `PROCEDURE`, `FUNCTION`, `TRIGGER`, `USER`, `ROLE`.

```sql
CREATE DATABASE EmpresaDB;

CREATE SCHEMA Ventas;

CREATE TABLE Empleados (
    IdEmpleado        INT            PRIMARY KEY,
    Nombre            NVARCHAR(50)   NOT NULL,
    FechaNacimiento   DATE,
    Salario           DECIMAL(10,2),
    Genero            CHAR(1)        NOT NULL CHECK (Genero IN ('M','F','O')),
    CorreoElectronico VARCHAR(100)   UNIQUE,
    FContratacion     DATETIME       DEFAULT GETDATE(),
    Estado            BIT            DEFAULT 1,
    IdDepartamento    INT,
    FOREIGN KEY (IdDepartamento) REFERENCES Departamentos(IdDepartamento)
);

CREATE VIEW VistaVentas AS
SELECT Nombre, Apellido, Salario FROM Empleados
WHERE Departamento = 'Ventas';

CREATE INDEX idx_Nombre ON Empleados(Nombre);
```

### ALTER

Modifica objetos existentes sin borrarlos.

```sql
ALTER TABLE Empleados ADD FechaBaja DATE;
ALTER TABLE Empleados ALTER COLUMN Salario DECIMAL(12,2);
ALTER TABLE Empleados DROP COLUMN Telefono;

EXEC sp_rename 'Empleados.Nombre', 'NombreCompleto', 'COLUMN';
EXEC sp_rename 'Empleados', 'Personal', 'OBJECT';

ALTER TABLE Empleados ADD CONSTRAINT chk_salario CHECK (Salario > 0);
ALTER TABLE Empleados DROP CONSTRAINT chk_salario;
```

### DROP

> ⚠️ Elimina el objeto y sus datos **permanentemente**.

```sql
DROP TABLE Empleados;
DROP VIEW VistaEmpleados;
DROP DATABASE EmpresaDB;
```

---

## DML — Data Manipulation Language

Opera directamente sobre los **datos** de las tablas.

### INSERT

```sql
-- Especificando columnas (el resto toma DEFAULT o NULL)
INSERT INTO Empleados (IdEmpleado, Nombre)
VALUES (1, 'Carlos López');

-- Todas las columnas en orden
INSERT INTO Empleados
VALUES (2, 'Ana Gómez', NULL, 1, 3, NULL);
```

> ⚠️ El tipo de dato debe coincidir. Si no, error.

### DELETE

```sql
DELETE FROM Empleados 
WHERE IdEmpleado = 3;

DELETE FROM Empleados 
WHERE Salario < 3500;

-- Sin WHERE elimina TODOS los registros
DELETE FROM Empleados;
```

### UPDATE

```sql
UPDATE Empleados
SET Salario = Salario + 500 
WHERE IdEmpleado = 3;

UPDATE Empleados 
SET IdDepartamento = 1 
WHERE Salario > 4000;

-- Sin WHERE actualiza TODAS las filas ⚠️
UPDATE Empleados 
SET Salario = 3000.00;
```

### SELECT

Orden de escritura vs orden de ejecución:

| Escritura | Ejecución | Cláusula | Rol |
|-----------|-----------|----------|-----|
| 5 | 1 | `FROM` | Origen de datos |
| 6 | 2 | `WHERE` | Filtro de filas |
| 3 | 3 | `GROUP BY` | Agrupamiento |
| 4 | 4 | `HAVING` | Filtro de grupos |
| 1 | 5 | `SELECT` | Columnas a mostrar |
| 2 | 6 | `ORDER BY` | Orden del resultado |

```sql
-- Básico
SELECT * FROM Empleados;
SELECT DISTINCT Departamento FROM Empleados;
SELECT TOP 5 Nombre, Salario FROM Empleados ORDER BY Salario DESC;

-- Con alias y expresiones
SELECT Nombre, Salario * 12 AS SalarioAnual FROM Empleados;

-- WHERE con operadores
SELECT Nombre FROM Empleados WHERE Salario BETWEEN 3000 AND 5000;
SELECT Nombre FROM Empleados WHERE Nombre LIKE 'A%';
SELECT Nombre FROM Empleados WHERE Telefono IS NULL;
SELECT Nombre FROM Empleados WHERE IdDepartamento IN (1, 2);

-- Subconsulta en WHERE
SELECT Nombre FROM Empleados
WHERE Salario > (SELECT AVG(Salario) FROM Empleados);

-- GROUP BY + HAVING
SELECT IdDepartamento, COUNT(*) AS Total, AVG(Salario) AS Promedio
FROM Empleados
GROUP BY IdDepartamento
HAVING COUNT(*) > 1;

-- ORDER BY
SELECT Nombre, Salario FROM Empleados ORDER BY Salario DESC;
SELECT Nombre, Salario FROM Empleados ORDER BY 2 DESC; -- por posición

-- JOIN
SELECT e.Nombre, d.NombreDepartamento
FROM Empleados e
INNER JOIN Departamentos d ON e.IdDepartamento = d.IdDepartamento;
```

→ [[SELECT]] — [[GROUP_BY]]

---

## Vistas

Consulta predefinida guardada en la BD que se comporta como **tabla virtual**. No almacena datos, los lee al momento de consultarla.

**Tipos:**

| Tipo | Descripción |
|------|-------------|
| Simple | Una tabla, sin agregaciones |
| Compleja | JOINs, GROUP BY, subconsultas |
| Indexada | Con índice físico (`WITH SCHEMABINDING`) |
| Con columnas calculadas | Expresiones en el SELECT |

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

-- Consultar
SELECT * FROM VistaEmpleados;
```

**Para que una vista sea actualizable debe:**
- Estar basada en una sola tabla
- No tener `GROUP BY`, `DISTINCT`, `HAVING`
- No tener funciones de agregación
- No tener subconsultas
- Incluir la clave primaria

**Optimización de consultas:**
1. Usar Execution Plan (`Ctrl+M`)
2. Evitar `SELECT *` y subconsultas innecesarias
3. Crear índices en columnas filtradas
4. Usar `TOP` para limitar datos

→ [[Vistas]]

---

## Próxima clase — Clase 7: SQL y Seguridad

- Instrucciones SQL y aplicación práctica
- Seguridad: perfiles de usuario y permisos (`GRANT`, `REVOKE`)
- Anidamientos y operadores de conjunto: `ALL`, `ANY`
