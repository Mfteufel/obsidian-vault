---
tags: [modelado, normalizacion, dependencias, join-dependency, 5fn]
aliases: [DR, Join Dependency, Dependencia de Reunion, Dependencias de Reunion, Dependencia de Union]
---

# Dependencias de Reunión (DR)

## Definición

> Una **DR** ocurre cuando una relación puede descomponerse en **dos o más proyecciones** que, al combinarse con `JOIN`, **recrean perfectamente** la relación original — sin perder ni agregar información.

También llamada **Join Dependency** o dependencia de unión.

---

## Restricción cíclica

Aparece cuando hay **3 o más proyecciones interdependientes** en un ciclo:

- Producto ↔ Proveedor
- Proveedor ↔ Cliente
- Cliente ↔ Producto

**Regla del negocio:** si A se relaciona con B, B con C y C con A → **debe existir la tupla (A, B, C)**.

> Una relación con restricción cíclica es **n-descomponible** (n > 2): se divide en n proyecciones que al unirse reconstruyen la original.

---

## Ejemplo del curso

Relación `Pedidos (Producto, Proveedor, Cliente)`:

| Producto | Proveedor | Cliente |
|----------|-----------|---------|
| A | P1 | C2 |
| A | P2 | C1 |
| B | P1 | C1 |
| A | P1 | C1 |

**Proyecciones:**

| Producto_Proveedor | Proveedor_Cliente | Cliente_Producto |
|---|---|---|
| (A,P1) (A,P2) (B,P1) | (P1,C2) (P2,C1) (P1,C1) | (C2,A) (C1,A) (C1,B) |

**Clave del ejemplo:**

- JOIN de **2** proyecciones → aparecen **tuplas espurias** (filas de más)
- JOIN de las **3** proyecciones → se recupera la relación original exacta

> Eso es una DR: solo la reunión de las *n* proyecciones reconstruye R.

---

## ¿Por qué importan?

Si una relación tiene una DR no implicada por sus claves candidatas → hay **redundancia** y anomalías de inserción, modificación y eliminación.

La [[CC_5FN]] exige eliminar esas DR mediante descomposición.

---

## Relacionado

[[CC_5FN]] · [[CC_4FN]] · [[CC_Dependencias_Multivaluadas]] · [[CC_Teorema_Rissanen]] · [[Clase 13 - Formas Normales Superiores y Modelo ER]]
