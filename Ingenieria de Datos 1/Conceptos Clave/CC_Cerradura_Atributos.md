---
tags: [modelado, normalizacion, dependencias-funcionales, cerradura, armstrong]
aliases: [Cerradura, X+, cerradura de atributos, closure]
---

# Cerradura de Atributos (X+)

## ¿Qué es?

Dado un conjunto de atributos **X** y un conjunto de DF **F**:

> **X+** es el conjunto de **todos los atributos** que se pueden derivar de X aplicando las DF de F de forma iterativa.

---

## ¿Para qué sirve?

- **Verificar si X → Y se cumple:** si `Y ⊆ X+` → sí se cumple
- **Encontrar llaves:** si `X+` incluye todos los atributos de la relación → X es superclave
- **Demostrar que una DF es deducible** de F

---

## Algoritmo — 3 pasos

```
Paso 1: Inicializar   → X+ = X
Paso 2: Aplicar DFs   → para cada DF (Y → Z) en F:
                          si Y ⊆ X+, entonces agregar Z a X+
Paso 3: Repetir       → hasta que X+ no cambie más
```

---

## Ejemplo 1 — Calcular cerradura

**Dado:**
```
R = (A, B, C, D, E, F)
F = { AB→C, BC→D, D→E, CF→B }

¿Cuál es (AB)+?
```

**Paso a paso:**

| Iteración | DF aplicable | X+ resultante |
|-----------|-------------|--------------|
| Inicio | — | {A, B} |
| 1 | AB→C (A,B ∈ X+) | {A, B, C} |
| 2 | BC→D (B,C ∈ X+) | {A, B, C, D} |
| 3 | D→E (D ∈ X+) | {A, B, C, D, E} |
| 4 | CF→B — C ∈ X+, pero F ∉ X+ | No aplica |
| Fin | No hay más cambios | **(AB)+ = {A, B, C, D, E}** |

---

## Ejemplo 2 — Verificar si una DF se deduce

**Verificar si AB → D:**

```
(AB)+ = {A, B, C, D, E}   (calculado arriba)

¿D ∈ (AB)+?  → Sí ✅
Conclusión: AB → D se cumple
```

**Verificar si D → A:**

```
(D)+ = {D}
D→E (D ∈ X+) → (D)+ = {D, E}
No hay más DFs aplicables.

¿A ∈ (D)+?  → No ❌
Conclusión: D → A NO se cumple
```

---

## Cómo identificar si X es llave candidata

```
Si (X)+ = todos los atributos de R → X es superclave

Si además ningún subconjunto propio de X tiene esa propiedad → X es llave candidata
```

**Ejemplo con (AB)+:**
```
R = (A, B, C, D, E, F)
(AB)+ = {A, B, C, D, E}   ← no incluye F

Por lo tanto AB NO es superclave de R
```

---

## Diferencia: Cerradura de atributos vs cerradura de F

| Concepto | Notación | Qué es |
|----------|---------|--------|
| Cerradura de X | X+ | Todos los atributos derivables de X |
| Cerradura de F | F+ | Todas las DF derivables del conjunto F |

---

## Relacionado

[[CC_Axiomas_Armstrong]] · [[CC_Dependencias_Funcionales]] · [[CC_Tipos_DF]] · [[Clase 11 - Normalizacion y Modelo ER]]
