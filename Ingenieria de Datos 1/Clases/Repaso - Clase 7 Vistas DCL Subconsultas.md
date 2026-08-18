---
tags: [repaso, sql, vistas, dcl, subconsultas]
---

# Repaso Clase 7 — Vistas, DCL, Subconsultas

## Sintaxis CREATE VIEW

```sql
CREATE VIEW NombreVista AS
SELECT columna1, columna2
FROM Tabla
WHERE condicion;
```

Vista actualizable necesita:
- Una sola tabla
- Sin GROUP BY
- Sin agregaciones
- Sin subconsultas
- Incluir la PK

## GRANT / REVOKE

```sql
-- Dar permiso
GRANT SELECT ON Clientes TO Pasante;

-- Sacar permiso
REVOKE SELECT ON Clientes FROM Pasante;

-- Denegar (gana sobre GRANT)
DENY DELETE ON Clientes TO Pasante;
```

## Ejemplo 1 — Subconsulta escalar

```sql
SELECT Nombre
FROM Estudiantes
WHERE ID_Estudiante = (
  SELECT ID_Estudiante 
  FROM Notas 
  WHERE Nota = 10
);
```
Devuelve un solo valor.

## Ejemplo 2 — Subconsulta con IN (filas múltiples)

```sql
SELECT Nombre
FROM Estudiantes
WHERE ID_Estudiante IN (
  SELECT ID_Estudiante 
  FROM Inscripciones 
  WHERE ID_Curso = 5
);
```
Devuelve varias filas, filtra por pertenencia.
