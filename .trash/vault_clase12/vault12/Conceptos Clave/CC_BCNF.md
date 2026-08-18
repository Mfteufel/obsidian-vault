---
tags: [modelado, normalizacion, formas-normales, bcnf, boyce-codd, superclave]
aliases: [BCNF, Boyce-Codd, Forma Normal de Boyce-Codd, FNBC]
---

# BCNF — Forma Normal de Boyce-Codd

## Definición

> Una relación está en BCNF si, para **cada dependencia funcional no trivial X → Y**, **X es una superclave**.

Es una versión más **estricta** de 3FN.

---

## Reglas

- Toda relación en **BCNF** también está en **3FN** (pero no al revés)
- Para toda DF no trivial, el **determinante debe ser superclave**

| Término | Definición |
|---------|-----------|
| **Superclave** | Conjunto de atributos que identifica de forma única cada fila (puede tener atributos de más) |
| **DF no trivial** | El dependiente NO es subconjunto del determinante |

---

## Ejemplo del curso

Tabla con dos DF en conflicto:

| Profesor | Curso | Aula |
|----------|-------|------|
| López | BD | 101 |
| López | IA | 101 |
| Pérez | Redes | 102 |

**DF identificadas:**

```
{Profesor, Curso} → {Aula}     ← clave candidata Y superclave ✅
{Aula}            → {Profesor}  ← Aula NO es superclave ❌
```

> El problema: `{Aula} → {Profesor}` es una DF no trivial donde `Aula` **no es superclave** → **NO está en BCNF**.

---

## Solución — descomponer

**R1 (Aula, Curso):**

| Aula | Curso |
|------|-------|
| 101 | BD |
| 101 | IA |
| 102 | Redes |

**R2 (Aula, Profesor):**

| Aula | Profesor |
|------|----------|
| 101 | López |
| 102 | Pérez |

> Al hacer **JOIN por Aula**, se recuperan los datos originales sin redundancia ni anomalías.

---

## Equivalente en SQL

```sql
CREATE TABLE AulaProfesor (
    Aula     INT PRIMARY KEY,
    Profesor VARCHAR(100)
);

CREATE TABLE AulaCurso (
    Aula  INT,
    Curso VARCHAR(50),
    PRIMARY KEY (Aula, Curso),
    FOREIGN KEY (Aula) REFERENCES AulaProfesor(Aula)
);

-- Reconstrucción original:
SELECT ap.Profesor, ac.Curso, ac.Aula
FROM AulaCurso ac
JOIN AulaProfesor ap ON ac.Aula = ap.Aula;
```

---

## 3FN vs BCNF

| | Condición |
|---|-----------|
| **3FN** | No hay dependencias transitivas (permite algunas excepciones) |
| **BCNF** | TODO determinante de una DF no trivial es superclave (más estricto) |

> Una tabla puede estar en 3FN pero **no** en BCNF si tiene un determinante que no es superclave.

---

## Relacionado

[[CC_3FN]] · [[CC_Teorema_Rissanen]] · [[CC_Tipos_DF]] · [[CC_Dependencias_Funcionales]] · [[Clase 12 - Normalizacion Formas Normales]]
