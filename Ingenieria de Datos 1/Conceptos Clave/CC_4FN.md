---
tags: [modelado, normalizacion, formas-normales, 4fn, dependencias-multivaluadas]
aliases: [4FN, 4NF, Cuarta Forma Normal]
---

# 4FN — Cuarta Forma Normal

## Definición

> Una relación está en **4FN** si y solo si está en **BCNF** y **no tiene dependencias multivaluadas no triviales**.

Formalmente:

```
Para toda DVM no trivial X ↠ Y,
X debe ser SUPERCLAVE
```

---

## Las dos condiciones

- **Estar en BCNF** → toda DF no trivial tiene una superclave como determinante
- **Sin DVM no triviales** → ningún atributo tiene conjuntos de valores independientes asociados a otro atributo

→ Ver [[CC_Dependencias_Multivaluadas]] para entender la DVM en detalle.

---

## Ejemplo del curso

Relación `R = (Curso, Estudiante, Profesor)` con CC = {Curso, Estudiante, Profesor}:

| Curso | Estudiante | Profesor |
|-------|-----------|----------|
| Matemáticas | Ana | López |
| Matemáticas | Ana | García |
| Matemáticas | Juan | López |
| Matemáticas | Juan | García |

**El problema:**

```
Curso ↠ Estudiante
Curso ↠ Profesor
Curso NO es superclave ❌
```

Los estudiantes de un curso son **independientes** de los profesores. Cada combinación se repite → redundancia.

---

## Solución — descomponer

**CURSO_ESTUDIANTE** — CC {Curso, Estudiante}:

| Curso | Estudiante |
|-------|-----------|
| Matemáticas | Ana |
| Matemáticas | Juan |
| Matemáticas | Carlos |

**CURSO_PROFESOR** — CC {Curso, Profesor}:

| Curso | Profesor |
|-------|----------|
| Matemáticas | López |
| Matemáticas | García |

> Cada DVM queda en su propia tabla. Se elimina la redundancia sin perder información.

---

## Equivalente en SQL

```sql
CREATE TABLE CursoEstudiante (
    Curso      NVARCHAR(50) NOT NULL,
    Estudiante NVARCHAR(50) NOT NULL,
    PRIMARY KEY (Curso, Estudiante)
);

CREATE TABLE CursoProfesor (
    Curso    NVARCHAR(50) NOT NULL,
    Profesor NVARCHAR(50) NOT NULL,
    PRIMARY KEY (Curso, Profesor)
);
```

---

## ¿Por qué sirve la 4FN?

- **Reduce redundancia**: no se guarda cada combinación estudiante × profesor
- **Integridad**: menos lugares donde actualizar el mismo dato
- **Optimiza almacenamiento**

## Limitaciones en la práctica

- Más tablas → más `JOIN` → consultas más complejas
- SQL **no permite declarar** una DVM como restricción de integridad
- A veces conviene tolerar la redundancia si los datos cambian poco

---

## Relacionado

[[CC_Dependencias_Multivaluadas]] · [[CC_BCNF]] · [[CC_5FN]] · [[CC_1FN]] · [[CC_Teorema_Rissanen]] · [[Clase 13 - Formas Normales Superiores y Modelo ER]]
