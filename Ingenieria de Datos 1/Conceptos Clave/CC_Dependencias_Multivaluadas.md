---
tags: [modelado, normalizacion, dependencias, dvm, 4fn]
aliases: [DVM, MVD, Dependencia Multivaluada, Dependencias Multivaluadas]
---

# Dependencias Multivaluadas (DVM)

## Definición

> Dada una relación R con atributos A, B y C, una **DVM** `A ↠ B` indica que los valores de B están determinados **únicamente por A** y son **independientes** de C.

**Notación:** `X ↠ Y` (flecha doble)

---

## La idea clave

- Para cada valor de A existe un **conjunto de valores** de B
- Ese conjunto se combina con múltiples valores de C
- Pero **B y C no tienen relación directa entre sí**

> Toda DF es una DVM, pero no toda DVM es una DF.

---

## Ejemplo del curso

Estudiante con idiomas y deportes **independientes**:

**0FN / UNF** (valores no atómicos):

| Estudiante | Idioma | Deporte |
|-----------|--------|---------|
| Ana | Inglés, Francés | Natación, Tenis |

**1FN** (valores atómicos) → aparece el producto cartesiano:

| Estudiante | Idioma | Deporte |
|-----------|--------|---------|
| Ana | Inglés | Natación |
| Ana | Francés | Natación |
| Ana | Inglés | Tenis |
| Ana | Francés | Tenis |

**Las DVM:**

```
Estudiante ↠ Idioma
Estudiante ↠ Deporte
Estudiante NO es superclave → viola 4FN
```

---

## Notación combinada

Cuando un atributo determina dos conjuntos independientes:

```
Estudiante ↠ Idioma | Deporte
```

---

## Cómo se eliminan

Se **descompone** la relación: una tabla por cada DVM.

| Estudiante | Idioma |
|-----------|--------|
| Ana | Inglés |
| Ana | Francés |

| Estudiante | Deporte |
|-----------|---------|
| Ana | Natación |
| Ana | Tenis |

→ Así la relación pasa a [[CC_4FN]].

---

## DF vs DVM

| | DF `X → Y` | DVM `X ↠ Y` |
|---|-----------|-------------|
| X determina | **un único valor** de Y | **un conjunto de valores** de Y |
| Se elimina en | 2FN / 3FN / BCNF | 4FN |
| SQL la soporta | Sí (PK, UNIQUE, FK) | No directamente |

---

## Relacionado

[[CC_4FN]] · [[CC_Dependencias_Funcionales]] · [[CC_Tipos_DF]] · [[CC_BCNF]] · [[Clase 13 - Formas Normales Superiores y Modelo ER]]
