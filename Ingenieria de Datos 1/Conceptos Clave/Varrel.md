---
tags: [base-de-datos, modelo-relacional, teoria]
aliases: [Varrel, Variable de Relación, Relación modificable]
---

# Varrel (Variable de Relación)

> Una **varrel** es una tabla cuyos datos pueden ser modificados. Se opone al concepto teórico de relación, que es inmutable.

Concepto acuñado por **C. J. Date**.

## Comparación: Relación vs Varrel

| Concepto | Definición | Mutabilidad |
|----------|------------|-------------|
| **Relación** (teoría) | Conjunto inmutable de tuplas con un esquema fijo | ❌ No modificable |
| **Varrel** (práctica) | Variable que contiene una relación, modificable en el tiempo | ✅ Modificable |

## Operaciones que modifican una varrel

| Operación | SQL Server | Descripción |
|-----------|------------|-------------|
| Inserción | `INSERT` | Agrega nuevas tuplas |
| Eliminación | `DELETE` | Elimina tuplas existentes |
| Actualización | `UPDATE` | Modifica valores de tuplas |

## Ejemplo

```sql
-- La tabla EMPLEADOS es una varrel: su contenido cambia en el tiempo

INSERT INTO EMPLEADOS VALUES (5, 'Laura', 102);

UPDATE EMPLEADOS SET dpt_ID = 103 WHERE emp_ID = 2;

DELETE FROM EMPLEADOS WHERE emp_ID = 4;
```

## Estado de la varrel EMPLEADOS a lo largo del tiempo

**Estado inicial:**

| emp_ID | nombre  | dpt_ID |
|--------|---------|--------|
| 1      | Juan    | 101    |
| 2      | Mariela | 102    |
| 3      | Pedro   | 101    |
| 4      | Martin  | 103    |

**Después de INSERT + UPDATE + DELETE:**

| emp_ID | nombre  | dpt_ID |
|--------|---------|--------|
| 1      | Juan    | 101    |
| 2      | Mariela | 103    |
| 3      | Pedro   | 101    |
| 5      | Laura   | 102    |

## Relacionado
- [[Restricciones_Modelo_Relacional]] — las restricciones se aplican sobre varrels
