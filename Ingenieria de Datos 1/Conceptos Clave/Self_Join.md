---
tags: [base-de-datos, sql-server, joins]
aliases: [Self Join, Auto Join, Join consigo mismo]
---

# CC_Self Join

> Un JOIN donde **una tabla se une consigo misma**, usando dos alias diferentes.

## Concepto

Se usa cuando una tabla tiene una **relación jerárquica interna**.
Por ejemplo: empleados que tienen un gerente que también es empleado.

La misma tabla aparece dos veces con **alias distintos** para diferenciarlas.

---

## Ejemplo — Empleados y Gerentes

**Tabla Empleados**

| ID_Emp | ID_Gte | Nombre |
|---|---|---|
| 1 | NULL | Aarón |
| 2 | 1 | Adela |
| 3 | 1 | Maria |

```sql
SELECT e.Nombre AS Empleado,
       g.Nombre AS Gerente
FROM Empleados e
LEFT JOIN Empleados g ON e.ID_Gte = g.ID_Emp;
```

**Resultado**

| Empleado | Gerente |
|---|---|
| Aarón | NULL |
| Adela | Aarón |
| Maria | Aarón |

> Se usa **LEFT JOIN** para incluir al jefe máximo (que no tiene gerente → `NULL`).

---

## ¿Cuándo usarlo?

- Empleados y sus jefes (jerarquías)
- Categorías con subcategorías
- Comparar filas de la misma tabla entre sí

---

## Relacionado

- [[Outer_Join]] — LEFT JOIN usado para preservar los sin gerente
- [[Clase 8 - JOINs en SQL]] — nota de clase con resumen general
