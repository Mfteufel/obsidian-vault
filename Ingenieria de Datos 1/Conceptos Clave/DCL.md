---
tags: [base-de-datos, sql, dcl, sql-server, seguridad]
aliases: [DCL, Data Control Language, GRANT, REVOKE]
---

# DCL — Data Control Language

> Controla el **acceso y los permisos** sobre los objetos de la base de datos.

## Comandos

| Comando | Para qué sirve |
|---------|---------------|
| `GRANT` | Otorga permisos a un usuario o rol |
| `REVOKE` | Quita permisos a un usuario o rol |

## Flujo completo de seguridad

**Login → Usuario → Esquema → Permisos**

### 1. Crear un login (a nivel servidor)
```sql
-- Autenticación de SQL Server (usuario + contraseña)
CREATE LOGIN login_ejemplo WITH PASSWORD = 'ContraseñaSegura2024';
```

### 2. Crear un usuario (a nivel base de datos)
```sql
USE MiBaseDeDatos;
CREATE USER usuario_ejemplo FOR LOGIN login_ejemplo;
```

### 3. Crear y asignar un esquema
```sql
-- Crear esquema
CREATE SCHEMA esquema_ejemplo;

-- Asignar esquema por defecto al usuario
ALTER USER usuario_ejemplo WITH DEFAULT_SCHEMA = esquema_ejemplo;
```

---

## Comandos de permisos

### GRANT — dar permisos
```sql
-- Permiso de SELECT
GRANT SELECT ON esquema_ejemplo.tabla_ejemplo TO usuario_ejemplo;

-- Permisos múltiples
GRANT INSERT, UPDATE ON esquema_ejemplo.tabla_ejemplo TO usuario_ejemplo;

-- Permiso de ejecución sobre todo el esquema
GRANT EXECUTE ON SCHEMA::esquema_ejemplo TO usuario_ejemplo;
```

### REVOKE — quitar permisos
```sql
REVOKE SELECT ON esquema_ejemplo.tabla_ejemplo FROM usuario_ejemplo;
REVOKE INSERT, UPDATE ON esquema_ejemplo.tabla_ejemplo FROM usuario_ejemplo;
```

### DENY — denegar explícitamente
```sql
-- DENY tiene prioridad sobre GRANT
DENY DELETE ON esquema_ejemplo.tabla_ejemplo TO usuario_ejemplo;
```

> ⚠️ `DENY` anula cualquier `GRANT` que el usuario tenga, incluso por herencia de rol.

---

## Tipos de autenticación en SQL Server

| Tipo | Descripción |
|---|---|
| **Windows** | Usa las credenciales del sistema operativo |
| **SQL Server** | Login y contraseña propios del servidor |

---

## Roles predefinidos

| Rol | Acceso |
|---|---|
| `db_owner` | Control total sobre la base de datos |
| `db_datareader` | Solo leer datos |
| `db_datawriter` | Insertar, actualizar y eliminar |
| `db_securityadmin` | Gestionar roles y permisos, **SIN acceso a datos** |
| `db_backupoperator` | Realizar backups, **SIN acceso a datos** |

```sql
-- Asignar un rol a un usuario
USE MiBaseDeDatos;
ALTER ROLE db_owner ADD MEMBER nombre_usuario;
```

---

## Relacionado
- [[SQL]] — contexto general
- [[DDL]] — CREATE LOGIN, CREATE USER
- [[Vistas]] — se pueden usar para controlar acceso a columnas
- [[Clase 7 - Vistas, DCL y Subconsultas]]
