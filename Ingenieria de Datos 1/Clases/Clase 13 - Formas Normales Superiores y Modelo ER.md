
![[IDD I_Clase13_AFB.pdf]]

# Clase 13 — Formas Normales Superiores y Modelo ER

**Materia:** Ingeniería de Datos
**Institución:** UADE
**Tema central:** 4FN, 5FN, Modelo ER, Desnormalización + repaso para el examen final

---

## 🎯 Objetivo de la clase

Avanzar en la normalización con **formas normales superiores** (BCNF, 4FN, 5FN), explorar **dependencias multivaluadas y de reunión**, y aplicar estos conceptos en casos prácticos.

---

## 🔢 La cadena completa de formas normales

```
1FN ⊂ 2FN ⊂ 3FN ⊂ BCNF ⊂ 4FN ⊂ 5FN
```

| Forma | Elimina | Ver |
|-------|---------|-----|
| **BCNF** | Determinantes que no son superclave | [[CC_BCNF]] |
| **4FN** | Dependencias **multivaluadas** no triviales | [[CC_4FN]] |
| **5FN / PJNF** | Dependencias de **reunión** no implicadas por las CC | [[CC_5FN]] |

---

## 📌 4FN — Cuarta Forma Normal
→ Ver [[CC_4FN]] y [[CC_Dependencias_Multivaluadas]]

- Estar en **BCNF** + sin **DVM no triviales**
- DVM: `X ↠ Y` → X determina un **conjunto** de valores de Y, independiente del resto
- Regla: para toda DVM no trivial, **X debe ser superclave**
- Ejemplo: `Curso ↠ Estudiante` y `Curso ↠ Profesor` → descomponer en 2 tablas

---

## 📌 5FN — Quinta Forma Normal
→ Ver [[CC_5FN]] y [[CC_Dependencias_Reunion]]

- Estar en **4FN** + toda **DR** debe ser deducible de las claves candidatas
- DR: la relación se reconstruye **solo** con el JOIN de sus proyecciones
- **Restricción cíclica**: 3+ proyecciones interdependientes → relación *n-descomponible*
- JOIN de 2 proyecciones → tuplas espurias / JOIN de las 3 → relación original ✅

---

## 📌 Modelo Entidad-Relación
→ Ver [[DER]]

- Herramienta **conceptual** para modelar datos y relaciones
- Entidad → tabla / Atributo → columna / Relación → FK o tabla intermedia
- Cardinalidades: **1:1**, **1:N**, **N:N**
- Tipos de atributos: simples, compuestos, monovaluados, multivaluados, derivados

---

## 📌 Desnormalización
→ Ver [[CC_Desnormalizacion]]

- Redundancia **intencional** para mejorar rendimiento (menos JOINs)
- Técnicas: duplicar datos, precalcular, fusionar tablas, vistas, triggers
- SQL Server: no hay vistas materializadas nativas → **vistas indexadas**

---

## 📝 Repaso Examen Final

### Ejercicio de normalización completo

Relación R con 19 atributos y 16 DF (incluye 2 DVM: `T ↠ U`, `T ↠ S`):

- **a)** CC = {S, T, U} → los únicos atributos que **no aparecen a la derecha** de ninguna DF
- **b)** Proceso completo: 1FN → 2FN (parciales) → 3FN (transitivas) → BCNF (`{P,Q}→{R}`) → 4FN (separa las DVM) → 5FN
- **c)** Si existiera `{T} → {S}`: la CC pasa a **{T, U}** y la MVD `T ↠ S` deja de motivar descomposición

> Resultado final: 12 relaciones (R1a, R1b, R2'a, R2'b, R2'', R2''', R3a1, R3a2, R3b, R3c, R3d/R3e, R4a, R4b)

### Otros temas de repaso

- **Niveles de aislamiento** y anomalías de lectura → [[CC_Transacciones]]
- **Hashing dinámico** (extensible, lineal, dinámico) → [[Hashing (Algoritmos de Clave)]]
- **Registros extendidos / no extendidos** (longitud fija vs variable)
- **Integridad de entidades** (PK única, sin NULL) + **integridad referencial** (FK válidas) → [[Restricciones_Modelo_Relacional]]
- **Axiomas de Armstrong** y su relación con proyecciones e independencia → [[CC_Axiomas_Armstrong]] · [[CC_Teorema_Rissanen]]

### Ejercicios SQL del final

- Esquema `Ventas` + subconsulta anidada con `AVG()` → [[Subconsultas]] · [[CC_Esquemas]]
- Función que devuelve tabla con CTE + `TOP 1 ... ORDER BY` (sin MAX/MIN ni subconsultas en FROM) → [[Funciones_Usuario]] · [[CC_CTE]]

---

## 📚 Bibliografía

- **C. J. Date** (7° Ed.) — Cap. 12: Normalización adicional II · Cap. 13: Modelado Semántico
- **Elmasri / Navathe** (5° Ed.) — Cap. 11: Algoritmos de diseño · Cap. 7: Mapeado ER a relacional

---

## 🔗 Relacionado

[[CC_4FN]] · [[CC_5FN]] · [[CC_Dependencias_Multivaluadas]] · [[CC_Dependencias_Reunion]] · [[CC_Desnormalizacion]] · [[DER]] · [[CC_BCNF]] · [[CC_Teorema_Rissanen]] · [[CC_Transacciones]] · [[Clase 12 - Normalizacion Formas Normales]]

---

**Próximo encuentro:** Cierre del ciclo — **Parcial 2** y **Examen Final**
