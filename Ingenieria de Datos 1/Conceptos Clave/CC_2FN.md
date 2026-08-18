---
tags: [modelado, normalizacion, formas-normales, 2fn, dependencia-parcial]
aliases: [2FN, Segunda Forma Normal, dependencia parcial]
---

# 2FN — Segunda Forma Normal

## Definición

> Una relación está en 2FN si está en **1FN** y todos los atributos no clave dependen **por completo** de la clave primaria.

Es decir: **sin dependencias parciales**.

---

## Conceptos clave

| Término | Definición |
|---------|-----------|
| **Atributo no clave** | El que no participa en la PK |
| **Dependencia parcial** | Un atributo no clave depende solo de **parte** de una PK compuesta |
| **Clave compuesta** | PK formada por más de un atributo |

> ⚠️ La 2FN **solo importa si la PK es compuesta**. Si la PK es un solo atributo, estar en 1FN ya implica 2FN.

---

## Regla formal

```
Sea R {a, b, c, d}  con  PK {a, b}  donde  b → d

b → d es una dependencia PARCIAL
  (d depende solo de b, no de la clave completa {a,b})

Descomposición:
  R1 {b, d}      PK {b}
  R2 {a, b, c}   PK {a, b}   FK(R1) {b}
```

---

## Ejemplo del curso

Tabla en 1FN con PK compuesta `{ID_Proyecto, Especialidad}`:

| ID_Proyecto | Especialidad | JefeEspecialidad |
|------------|-------------|-----------------|
| 1 | Lenguaje Java | Germán Lozano |
| 1 | Conectividad | Pedro Morales |
| 3 | SQL | Laura Pineda |

**Problema:** `JefeEspecialidad` depende solo de `Especialidad`, no de toda la PK.

```
{Especialidad} → {JefeEspecialidad}   ← dependencia PARCIAL
```

---

## Solución — separar en dos tablas

**Tabla 1: Especialidad → Jefe**

| Especialidad | JefeEspecialidad |
|-------------|-----------------|
| Lenguaje Java | Germán Lozano |
| Conectividad | Pedro Morales |
| SQL | Laura Pineda |

**Tabla 2: Proyecto ↔ Especialidad** (tabla puente)

| ID_Proyecto | ID_Especialidad |
|------------|----------------|
| 1 | Lenguaje Java |
| 1 | Conectividad |
| 3 | SQL |

✅ Ahora cada atributo depende de su clave completa.

---

## Verificación con cerradura

```
R1 {ID_Proyecto, NombreProyecto, Ciudad, JefeProyecto, Acceso, Telefono}
(ID_Proyecto)+ = {ID_Proyecto, NombreProyecto, Ciudad, JefeProyecto, Acceso, Telefono}
  → ID_Proyecto determina TODO R1 ✅

R2 {ID_Proyecto, Especialidad, JefeEspecialidad}
(ID_Proyecto, Especialidad)+ = {ID_Proyecto, Especialidad, JefeEspecialidad}
  → la clave compuesta determina TODO R2 ✅
```

---

## Equivalente en SQL

```sql
CREATE TABLE Especialidad (
    Especialidad     VARCHAR(50) PRIMARY KEY,
    JefeEspecialidad VARCHAR(100)
);

CREATE TABLE ProyectoEspecialidad (
    ID_Proyecto  INT,
    Especialidad VARCHAR(50),
    PRIMARY KEY (ID_Proyecto, Especialidad),
    FOREIGN KEY (Especialidad) REFERENCES Especialidad(Especialidad)
);
```

---

## Relacionado

[[CC_1FN]] · [[CC_3FN]] · [[CC_Tipos_DF]] · [[CC_Cerradura_Atributos]] · [[Clase 12 - Normalizacion Formas Normales]]
