---
tags: [clase, sql, vistas, dcl, subconsultas, sql-server]
---
![[IDD I_Clase07_AFB.pdf]]

# Clase 7 — Vistas, DCL y Subconsultas

**Objetivo:** Seguridad en BD, permisos con GRANT/REVOKE, vistas SQL y subconsultas anidadas.

---

## Vistas (VIEW)

Una **vista** es una consulta guardada que se comporta como tabla virtual.
No almacena datos, los lee de las **tablas subyacentes** al momento de consultar.

**Tipos de vistas:**

| Tipo                | Característica                           |
| ------------------- | ---------------------------------------- |
| Simple              | Una sola tabla, sin agregaciones         |
| Compleja            | JOINs, GROUP BY, subconsultas            |
| Indexada            | Con índice físico (`WITH SCHEMABINDING`) |
| Columnas calculadas | Expresiones en el SELECT                 |

**Vista actualizable** → requiere: una sola tabla, sin `GROUP BY`, sin agregaciones, sin subconsultas, incluir la PK.

---

## DCL — Seguridad y permisos

El flujo completo es: **Login → Usuario → Esquema → Permisos**.

**3 comandos clave:**

| Comando  | Efecto                                               |
| -------- | ---------------------------------------------------- |
| `GRANT`  | Da permiso                                           |
| `REVOKE` | Quita permiso                                        |
| `DENY`   | Deniega explícitamente (tiene prioridad sobre GRANT) |

**Niveles de autenticación en SQL Server:**
- **Windows:** usa credenciales del SO
- **SQL Server:** login + contraseña propios del servidor

**Roles predefinidos:**

| Rol                 | Acceso                              |
| ------------------- | ----------------------------------- |
| `db_owner`          | Control total                       |
| `db_datareader`     | Solo leer                           |
| `db_datawriter`     | Insertar, actualizar, eliminar      |
| `db_securityadmin`  | Gestionar roles, SIN acceso a datos |
| `db_backupoperator` | Hacer backups, SIN acceso a datos   |

---

## Subconsultas (Consultas Anidadas)

Una **subconsulta** es un SELECT dentro de otro SELECT.
Se ubica en: `WHERE`, `HAVING`, `FROM` o `SELECT`.

**Tipos:**

| Tipo | Descripción |
|---|---|
| Escalar | Devuelve un solo valor |
| Filas múltiples | Devuelve varias filas |
| Múltiples columnas | Devuelve como tabla (va en FROM) |
| Correlacionada | Referencia a la consulta externa |

**Operadores de conjunto:**
`IN`, `NOT IN`, `ANY`, `ALL`, `EXISTS`, `NOT EXISTS`

---

## Conceptos Clave relacionados

- [[Vistas]] — tipos, limitaciones, optimización y ejemplos completos
- [[DCL]] — GRANT, REVOKE, DENY, roles y esquemas
- [[Subconsultas]] — tipos, operadores y ejemplos
