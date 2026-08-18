---
tags: [modelado, normalizacion, dependencias-funcionales, tipos-df]
aliases: [DF Trivial, DF Completa, DF Parcial, DF Transitiva, tipos de dependencias funcionales]
---

# Tipos de Dependencias Funcionales

## Resumen rápido

| Tipo | Condición | ¿Útil para diseño? |
|------|-----------|-------------------|
| **Trivial** | Y ⊆ X | ❌ No aporta info |
| **Completa** | Y depende de TODO X | ✅ Sí — base de 2FN |
| **Parcial** | Y depende de PARTE de X | ⚠️ Problema para 2FN |
| **Transitiva** | X → Y → Z | ⚠️ Problema para 3FN |

---

## 1. DF Trivial

> Ocurre cuando **Y es subconjunto de X**.

La dependencia se cumple siempre, automáticamente. No aporta información nueva.

**Condición formal:** `Y ⊆ X`

**Ejemplo:**

| ID_Cliente | Nombre | Dirección |
|-----------|--------|-----------|
| 101 | Ana García | Calle 1 |
| 102 | Juan Pérez | Calle 2 |

```
{ID_Cliente, Nombre} → {Nombre}   ← TRIVIAL (Nombre ya está en el determinante)
{ID_Cliente}         → {Nombre}   ← NO TRIVIAL (aporta información)
```

---

## 2. DF Completa

> Y depende de **todo** el conjunto determinante X, no de ningún subconjunto.

Todos los atributos del determinante son **necesarios**.

**Ejemplo:**

| ID_Cliente | Nombre     | Dirección |
| ---------- | ---------- | --------- |
| 101        | Ana García | Calle 1   |
| 101        | Juan Pérez | Calle 2   |
| 102        | Ana García | Calle 3   |

```
{ID_Cliente, Nombre} → {Dirección}   ← COMPLETA
  (necesito AMBOS para encontrar la dirección)

{ID_Cliente, Dirección} → {Nombre}   ← PARCIAL
  (con solo Dirección ya puedo encontrar Nombre en este modelo)
```

> ⚠️ La completitud depende del **modelo de datos** — qué garantiza el negocio.

---

## 3. DF Parcial

> Y depende de **solo una parte** del determinante compuesto.

Es lo opuesto a completa. Indica que el determinante tiene atributos innecesarios.

**Problema:** genera redundancia → viola 2FN.

```
{NoPedido, ID_Producto} → {NombreProducto}   ← PARCIAL
  NombreProducto solo depende de ID_Producto, no necesita NoPedido
```

**Solución:** separar en tablas distintas.

---

## 4. DF Transitiva

> X determina Z a través de un intermediario Y.

**Cadena:** `X → Y → Z` implica `X → Z` (transitiva)

**Ejemplo:**

| ID_Cliente | Nombre | Ciudad | Código_Postal |
|-----------|--------|--------|--------------|
| 101 | Ana García | Buenos Aires | 1001 |
| 102 | Juan Pérez | Rosario | 2000 |
| 103 | Luis Ramírez | Córdoba | 5000 |

```
{ID_Cliente} → {Ciudad}          ← directa
{Ciudad}     → {Código_Postal}   ← directa
{ID_Cliente} → {Código_Postal}   ← TRANSITIVA (y no trivial)
```

**Problema:** `Código_Postal` no depende directamente de `ID_Cliente` → viola 3FN.

**Solución:** separar `Ciudad` y `Código_Postal` a otra tabla.

---

## Resumen visual de la cadena de normalización

```
DF Parcial  → viola 2FN → separar dependencias parciales
DF Transitiva → viola 3FN → separar dependencias transitivas
```

---

## Relacionado

[[CC_Dependencias_Funcionales]] · [[CC_Axiomas_Armstrong]] · [[CC_Cerradura_Atributos]] · [[Clase 11 - Normalizacion y Modelo ER]]
