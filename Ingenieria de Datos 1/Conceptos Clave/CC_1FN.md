---
tags: [modelado, normalizacion, formas-normales, 1fn, diseño-bd]
aliases: [1FN, Primera Forma Normal, valores atomicos]
---

# 1FN — Primera Forma Normal

## Definición

> Una relación está en 1FN si **todos los dominios contienen valores atómicos** — sin grupos repetitivos ni multivaluados.

---

## Las 3 condiciones

| Condición | Qué significa |
|-----------|---------------|
| **Valores atómicos** | Cada celda contiene un solo valor indivisible |
| **Sin grupos repetitivos** | Nada de listas o múltiples valores en una celda |
| **Clave primaria** | La tabla identifica cada registro de forma única |

---

## Problema típico — celda multivaluada

Tabla **antes de 1FN** (viola la regla):

| ID_Proyecto | NombreProyecto | Especialidad | JefeEspecialidad |
|------------|---------------|-------------|-----------------|
| 1 | App Móvil | Java, Conectividad, Celula | Lozano, Morales, Ríos |
| 2 | Web Corporativa | Conectividad, Router Cisco | Morales, Renzo |

❌ Las columnas `Especialidad` y `JefeEspecialidad` tienen **múltiples valores** por celda.

---

## Solución — crecimiento vertical

Se descompone cada valor en su **propia fila**:

| ID_Proyecto | NombreProyecto | Especialidad | JefeEspecialidad |
|------------|---------------|-------------|-----------------|
| 1 | App Móvil | Java | Lozano |
| 1 | App Móvil | Conectividad | Morales |
| 1 | App Móvil | Celula | Ríos |
| 2 | Web Corporativa | Conectividad | Morales |
| 2 | Web Corporativa | Router Cisco | Renzo |

✅ Ahora cada celda es atómica.

> Clave: **crecimiento vertical** (más filas), no horizontal (más columnas).

---

## Dependencias funcionales del ejemplo

Una vez en 1FN, se identifican las DF:

```
{ID_Proyecto}                → {NombreProyecto, Ciudad, JefeProyecto}
{ID_Proyecto, Especialidad}  → {JefeEspecialidad}
{Ciudad}                     → {CodPostal}
{JefeProyecto}               → {Acceso, Telefono}
```

> ⚠️ Acá ya se ven problemas: hay dependencias parciales y transitivas → se resuelven en 2FN y 3FN.

---

## Equivalente conceptual en SQL

```sql
-- Mal (no atómico) — NUNCA hacer esto:
-- Especialidad VARCHAR(200) = 'Java, Conectividad, Celula'

-- Bien (1FN) — una fila por valor:
CREATE TABLE ProyectoEspecialidad (
    ID_Proyecto   INT,
    Especialidad  VARCHAR(50),
    PRIMARY KEY (ID_Proyecto, Especialidad)
);
```

---

## Relacionado

[[CC_2FN]] · [[CC_3FN]] · [[CC_Dependencias_Funcionales]] · [[CC_Anomalias_BD]] · [[Clase 12 - Normalizacion Formas Normales]]
