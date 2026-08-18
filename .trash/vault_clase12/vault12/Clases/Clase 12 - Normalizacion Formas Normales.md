# Clase 12 — Normalización (Formas Normales)

**Materia:** Ingeniería de Datos  
**Institución:** UADE  
**Tema central:** Proceso de normalización — 1FN, 2FN, 3FN, BCNF y Teorema de Rissanen

---

## 🎯 Objetivo de la clase

Profundizar en el proceso de **normalización**: reducir redundancias, eliminar anomalías y optimizar la estructura de las tablas.

- Aplicar las formas normales básicas (1FN, 2FN, 3FN)
- Entender BCNF como refuerzo de 3FN
- Conocer el Teorema de Rissanen (descomposición sin pérdida)

---

## 📌 ¿Qué es una forma normal?

> Una relación está en **determinada forma normal** si satisface un cierto conjunto de restricciones.

Cada forma normal **incluye** a la anterior:

```
1FN ⊂ 2FN ⊂ 3FN ⊂ BCNF
```

Para llegar a 2FN hay que estar en 1FN. Para 3FN hay que estar en 2FN. Y así.

---

## 🔢 Las formas normales — resumen

| Forma | Se basa en | Elimina |
|-------|-----------|---------|
| **1FN** | Atributos atómicos | Grupos repetitivos / multivaluados |
| **2FN** | Dependencias funcionales | Dependencias **parciales** |
| **3FN** | Dependencias transitivas | Dependencias **transitivas** |
| **BCNF** | Superclaves | Determinantes que no son superclave |

---

### 1FN — Primera Forma Normal
→ Ver [[CC_1FN]]

- Cada celda tiene **un solo valor** (atómico)
- Sin grupos repetitivos ni listas en una celda
- La tabla tiene clave primaria
- Solución: **crecimiento vertical** (descomponer en filas)

---

### 2FN — Segunda Forma Normal
→ Ver [[CC_2FN]]

- Estar en 1FN
- Todos los atributos no clave dependen de la **clave completa**
- Elimina **dependencias parciales** (cuando un atributo depende solo de parte de una PK compuesta)
- Relevante solo si la **PK es compuesta**

---

### 3FN — Tercera Forma Normal
→ Ver [[CC_3FN]]

- Estar en 2FN
- Ningún atributo no clave depende de **otro atributo no clave**
- Elimina **dependencias transitivas**

---

### BCNF — Forma Normal de Boyce-Codd
→ Ver [[CC_BCNF]]

- Versión más estricta de 3FN
- Para **toda DF no trivial X → Y**, X debe ser **superclave**
- Toda relación en BCNF también está en 3FN

---

### Teorema de Rissanen
→ Ver [[CC_Teorema_Rissanen]]

- Define cuándo una **descomposición es independiente** (sin pérdida)
- Los atributos comunes entre R1 y R2 deben formar una clave candidata de al menos una

---

## 📊 Fundamentos según Edgar F. Codd

| Forma | Característica indispensable |
|-------|---------------------------|
| **1FN** | Atributos atómicos, filas únicas, crecimiento vertical |
| **2FN** | En 1FN + PK preferentemente simple + dependencia total de la clave |
| **3FN** | En 2FN + sin dependencias transitivas del atributo clave |

---

## 🔗 Relacionado

[[CC_1FN]] · [[CC_2FN]] · [[CC_3FN]] · [[CC_BCNF]] · [[CC_Teorema_Rissanen]] · [[CC_Tipos_DF]] · [[CC_Cerradura_Atributos]] · [[CC_Dependencias_Funcionales]]

---

**Próxima clase (13):** Formas normales superiores — 4FN (multivaluadas), 5FN (reunión) + prácticas para el parcial
