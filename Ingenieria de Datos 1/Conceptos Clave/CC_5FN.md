---
tags: [modelado, normalizacion, formas-normales, 5fn, pjnf, dependencias-reunion]
aliases: [5FN, 5NF, Quinta Forma Normal, PJNF, Forma Normal de Proyeccion-Union]
---

# 5FN — Quinta Forma Normal

## Definición

> Una relación está en **5FN** si y solo si está en **4FN** y todas las **Dependencias de Reunión (DR)** son de hecho **Dependencias Funcionales (DF)** — es decir, están implicadas por las claves candidatas.

También conocida como **PJNF** (Forma Normal de Proyección-Unión).

---

## Condiciones

- Estar en [[CC_4FN]] — sin DVM no triviales
- Toda [[CC_Dependencias_Reunion|DR]] debe deducirse de las claves candidatas
- La relación **no puede descomponerse** en dos o más proyecciones sin perder datos al reconstruirla con `JOIN` natural

---

## Ejemplo del curso

`Pedidos (Producto, Proveedor, Cliente)` — CC = {Producto, Proveedor, Cliente}:

| Producto | Proveedor | Cliente |
|----------|-----------|---------|
| A | P1 | C2 |
| A | P2 | C1 |
| B | P1 | C1 |
| A | P1 | C1 |

**Significado de cada par:**

- `(A, P1)` → el producto A es **suministrado** por P1
- `(P1, C1)` → el proveedor P1 **trabaja con** C1
- `(C1, A)` → el cliente C1 **solicita** el producto A

Todos los atributos dependen de la **combinación completa** de los tres → no hay DVM no triviales → **está en 4FN**.

---

## Anomalías que resuelve

| Anomalía | Ejemplo |
|----------|---------|
| **Eliminación** | Borrar el par (C1, A) obliga a eliminar las tuplas (A,P1,C1) y (A,P2,C1) |
| **Modificación** | Cambiar el par (A, P1) obliga a modificar (A,P1,C2) y (A,P1,C1) |
| **Inserción** | Agregar (B, P3, C1) exige validar las tres relaciones parciales a la vez |

---

## Detalle clave de las proyecciones

Al descomponer en `Producto_Proveedor`, `Proveedor_Cliente` y `Cliente_Producto`:

- JOIN de **2** → genera **tuplas espurias** ❌
- JOIN de las **3** → reconstruye la relación original ✅

> Por eso la 5FN se verifica con la reunión de **todas** las proyecciones.

---

## ¿Cuándo se usa?

- Bases de datos **muy grandes y complejas**
- Relaciones entre **3 o más conjuntos de atributos** con restricciones cíclicas
- Aplicaciones donde eliminar toda redundancia es crítico

En la práctica es poco frecuente llegar a 5FN — el costo en JOINs suele superar el beneficio.

---

## Relacionado

[[CC_Dependencias_Reunion]] · [[CC_4FN]] · [[CC_BCNF]] · [[CC_Teorema_Rissanen]] · [[CC_Anomalias_BD]] · [[Clase 13 - Formas Normales Superiores y Modelo ER]]
