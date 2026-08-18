
![[IDD I_Clase11_AFB.pdf]]

# Clase 11 — Normalización y Modelo ER

**Materia:** Ingeniería de Datos  
**Institución:** UADE  
**Tema central:** Dependencias Funcionales — base teórica para la normalización

---

## 🎯 Objetivo de la clase

Entender el concepto de **dependencias funcionales (DF)** y su aplicación en el diseño de bases de datos.

- Identificar DF en conjuntos de datos reales
- Reconocer tipos de DF (trivial, completa, transitiva)
- Aplicar los **Axiomas de Armstrong** para deducir DF
- Calcular la **Cerradura** de un conjunto de atributos

---

## ⚠️ Punto de partida — Anomalías

Las **anomalías** son problemas causados por datos mal estructurados.
→ Ver [[CC_Anomalias_BD]]

| Tipo | Problema |
|------|----------|
| **Inserción** | No se puede agregar un dato sin que falte otro |
| **Modificación** | Hay que cambiar lo mismo en múltiples filas |
| **Eliminación** | Borrar un dato implica perder otro |

---

## 📐 Dependencias Funcionales

→ Ver [[CC_Dependencias_Funcionales]]

**Notación:** `X → Y` (X determina Y)

- **X** = determinante (lado izquierdo)
- **Y** = dependiente (lado derecho)

Regla clave: un valor único de X **siempre** produce un único valor de Y.

---

## 🔑 Tipos de Llaves

→ Ver [[CC_Dependencias_Funcionales]]

| Tipo | Descripción |
|------|-------------|
| **Candidata** | Mínimo conjunto de atributos que identifica una tupla |
| **Primaria** | Llave candidata elegida como identificador oficial |
| **Foránea** | Referencia a la PK de otra tabla (integridad referencial) |
| **Superclave** | Cualquier conjunto que incluya a una candidata |

---

## 📋 Tipos de Dependencias Funcionales

→ Ver [[CC_Tipos_DF]]

| Tipo | Definición breve | Ejemplo |
|------|-----------------|---------|
| **Trivial** | Y ⊆ X (Y ya está en X) | `{ID, Nombre} → {Nombre}` |
| **Completa** | Y depende de TODO X, no de un subconjunto | `{ID_Cliente, Nombre} → {Dirección}` |
| **Parcial** | Y depende solo de parte de X | `{ID_Cliente, Dirección} → {Nombre}` |
| **Transitiva** | X→Y→Z (indirecta) | `ID_Cliente → Ciudad → Código_Postal` |

---

## 📏 Axiomas de Armstrong

→ Ver [[CC_Axiomas_Armstrong]]

Reglas para **deducir** nuevas DF a partir de las conocidas.

| Axioma | Regla |
|--------|-------|
| Reflexividad | Si Y ⊆ X ⇒ X → Y |
| Aumento | Si X → Y ⇒ XZ → YZ |
| Transitividad | Si X → Y y Y → Z ⇒ X → Z |
| Descomposición | Si X → YZ ⇒ X → Y y X → Z |
| Unión | Si X → Y y X → Z ⇒ X → YZ |
| Pseudotransitividad | Si X → Y y WY → Z ⇒ WX → Z |

---

## 🔒 Cerradura de Atributos (X+)

→ Ver [[CC_Cerradura_Atributos]]

**X+** = todos los atributos que se pueden derivar de X usando las DF de F.

**Pasos:**
1. Inicializar: `X+ = X`
2. Aplicar cada DF: si `Y → Z` y `Y ⊆ X+`, entonces agregar `Z` a `X+`
3. Repetir hasta que no haya cambios

**Uso:** verificar si `X → Y` se cumple → comprobar si `Y ⊆ X+`

---

## 🔍 Dependencias Deducidas

→Ver [[CC_Dependencias_Deducidas]]

**Objetivo:** verificar si una DF específica `X → Y` se puede deducir de F.

**Pasos:**

1. Calcular `X+`
2. Aplicar todas las DFs de F
3. Comprobar si `Y ⊆ X+` → sí está = se deduce ✅ / no está = no se deduce ❌

```
Ejemplo:
AB → D?  →  (AB)+ = {A,B,C,D,E}  →  D ∈ (AB)+  →  ✅ se deduce
D  → A?  →  (D)+  = {D,E}        →  A ∉ (D)+   →  ❌ no se deduce
```


---

## 🔗 Relacionado

[[CC_Anomalias_BD]] · [[CC_Dependencias_Funcionales]] · [[CC_Tipos_DF]] · [[CC_Axiomas_Armstrong]] · [[CC_Cerradura_Atributos]]

---

**Próxima clase (12):** Normalización — 1FN, 2FN, 3FN, BCNF, Teorema de Rissanen
