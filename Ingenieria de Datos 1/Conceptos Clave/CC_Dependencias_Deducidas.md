---
tags:
  - modelado
  - normalizacion
  - dependencias-funcionales
  - cerradura
  - inferencia
aliases:
  - Dependencias Deducidas
  - DF deducidas
  - verificar dependencia funcional
---
---



# Dependencias Deducidas

## ¿Qué es?

Un proceso para **verificar si una DF X → Y se puede deducir** a partir de un conjunto de dependencias F.

Se basa en calcular la cerradura X+ y comprobar si Y está dentro.

---

## Algoritmo — 3 pasos

```
Paso 1: Calcular X+
Paso 2: Aplicar todas las DFs de F sobre X+
Paso 3: Comprobar si Y ⊆ X+
         → Sí ⊆ X+  →  X → Y  se deduce  ✅
         → No ⊆ X+  →  X → Y  NO se deduce  ❌
```

---

## Ejemplo del curso

**Dado:**

```
R = (A, B, C, D, E, F)
F = { AB→C, BC→D, D→E, CF→B }
```

---

### Verificar AB → D

```
(AB)+ = {A, B}

AB→C  (A,B ∈ X+)  →  (AB)+ = {A, B, C}
BC→D  (B,C ∈ X+)  →  (AB)+ = {A, B, C, D}
D→E   (D ∈ X+)    →  (AB)+ = {A, B, C, D, E}
CF→B  (F ∉ X+)    →  no aplica
```

**¿D ∈ (AB)+?** → Sí ✅

> **Conclusión: AB → D se deduce de F**

---

### Verificar D → A

```
(D)+ = {D}

D→E  (D ∈ X+)  →  (D)+ = {D, E}
No hay más DFs aplicables.
```

**¿A ∈ (D)+?** → No ❌

> **Conclusión: D → A NO se deduce de F**

---

## Diferencia clave con la Cerradura

|Concepto|Pregunta que responde|
|---|---|
|[[CC_Cerradura_Atributos\|Cerradura X+]]|¿Qué atributos puedo derivar de X?|
|**Dependencias Deducidas**|¿Esta DF específica X → Y se puede derivar?|

Son el mismo mecanismo — la diferencia es el objetivo.

---

## Relacionado

[[CC_Cerradura_Atributos]] · [[CC_Axiomas_Armstrong]] · [[CC_Dependencias_Funcionales]] · [[Clase 11 - Normalizacion y Modelo ER]]