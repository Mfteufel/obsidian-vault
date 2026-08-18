---
tags: [modelado, normalizacion, dependencias-funcionales, armstrong, inferencia]
aliases: [Axiomas de Armstrong, Armstrong, reglas de inferencia, cerradura F+]
---

# Axiomas de Armstrong

## ¿Qué son?

Un conjunto de **reglas de inferencia** para deducir nuevas dependencias funcionales a partir de las conocidas.

- Desarrollados por **William W. Armstrong** en 1974
- Son **completas** (cubren todas las inferencias válidas)
- Son **consistentes** (no generan contradicciones)

---

## ¿Para qué sirven?

- Encontrar la **cerradura F+** (todas las DF posibles)
- Calcular las **llaves** de un esquema
- Verificar si dos conjuntos de DF son equivalentes
- Deducir DF implícitas

---

## Las 6 reglas

| Nombre | Regla formal | Qué dice en castellano |
|--------|-------------|----------------------|
| **Reflexividad** | Si Y ⊆ X ⇒ X → Y | Un conjunto determina cualquier subconjunto suyo |
| **Aumento** | Si X → Y ⇒ XZ → YZ | Agregar atributos a ambos lados no rompe la DF |
| **Transitividad** | Si X → Y y Y → Z ⇒ X → Z | Si A determina B, y B determina C, entonces A determina C |
| **Descomposición** | Si X → YZ ⇒ X → Y y X → Z | Una DF con múltiples dependientes se puede partir |
| **Unión** | Si X → Y y X → Z ⇒ X → YZ | Dos DF con mismo determinante se pueden unir |
| **Pseudotransitividad** | Si X → Y y WY → Z ⇒ WX → Z | Variante de transitividad con atributos extra |

---

## Ejemplo del curso

**Dado:**
```
R = (A, B, C, G, H, I)
F = { A→B, A→C, CG→H, CG→I, B→H }
```

**Aplicando axiomas para obtener F+ (cerradura de F):**

| Axioma | DF deducida |
|--------|------------|
| Reflexividad | — (no aplica con estos datos) |
| Aumento | AC→BC, AG→BG, AH→BH, AI→BI, ... |
| Transitividad | A → H (porque A→B y B→H) |
| Descomposición | — (no aplica directamente) |
| Unión | A→BC, CG→HI, AG→HI |
| Pseudotransitividad | AG→H, AG→I |

---

## Cómo aplicar transitividad paso a paso

```
Tenemos: A → B  y  B → H

Regla de transitividad:
  Si X → Y  y  Y → Z  ⇒  X → Z

Entonces:
  A → B
  B → H
  ------
  A → H  ✅
```

---

## Cómo aplicar unión

```
Tenemos: CG → H  y  CG → I

Regla de unión:
  Si X → Y  y  X → Z  ⇒  X → YZ

Entonces:
  CG → H
  CG → I
  -------
  CG → HI  ✅
```

---

## F+ vs F

| Concepto | Descripción |
|----------|-------------|
| **F** | El conjunto de DF dado (el "original") |
| **F+** | La cerradura: TODAS las DF que se pueden deducir de F aplicando los axiomas |

> F ⊆ F+ siempre. F+ puede ser mucho más grande que F.

---

## Relacionado

[[CC_Dependencias_Funcionales]] · [[CC_Tipos_DF]] · [[CC_Cerradura_Atributos]] · [[Clase 11 - Normalizacion y Modelo ER]]
