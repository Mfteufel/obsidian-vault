---
tags: [modelado, normalizacion, rissanen, descomposicion, descomposicion-sin-perdida]
aliases: [Teorema de Rissanen, Rissanen, descomposicion independiente, descomposicion sin perdida]
---

# Teorema de Rissanen

## Definición

> La descomposición de R en R1 y R2 es **independiente** (sin pérdida) si y solo si:
>
> 1. Toda DF de R se puede **deducir** de las DF de R1 y R2, **y**
> 2. Los **atributos comunes** de R1 y R2 forman una **clave candidata** de al menos una de las dos.

---

## ¿Para qué sirve?

Garantiza que al **descomponer** una tabla para normalizarla:

- No se pierde información
- Se puede **reconstruir** la tabla original con un JOIN
- No se generan filas falsas (spurious tuples)

---

## Las 2 condiciones explicadas

### Condición 1 — Preservar dependencias

```
Se pueden reconstruir TODAS las DF de R
usando solo las DF de R1 y R2.
```

### Condición 2 — Atributo común = clave candidata

```
Los atributos que comparten R1 y R2
deben ser clave candidata de R1 O de R2 (al menos una).
```

Esto asegura que el atributo compartido actúa como **identificador único** al combinar.

---

## Ejemplo (basado en el caso BCNF)

```
R (Profesor, Curso, Aula)

Descomposición:
  R1 (Aula, Curso)
  R2 (Aula, Profesor)

Atributo común: Aula

¿Aula es clave candidata de R2?
  {Aula} → {Profesor}  →  Sí, Aula identifica cada fila de R2 ✅

Por lo tanto la descomposición es INDEPENDIENTE (sin pérdida).
```

---

## Verificación con JOIN

```sql
-- Si la descomposición cumple Rissanen,
-- este JOIN devuelve EXACTAMENTE las filas originales:
SELECT r2.Profesor, r1.Curso, r1.Aula
FROM R1 r1
JOIN R2 r2 ON r1.Aula = r2.Aula;
```

> Si apareciera una fila que no estaba en R original → la descomposición tendría pérdida (mal).

---

## Por qué importa

Al normalizar separamos tablas. Rissanen nos da el **criterio formal** para saber si esa separación es correcta:

```
Descomposición buena  → JOIN reconstruye R exacto
Descomposición mala   → JOIN genera filas falsas / pierde DF
```

---

## Relacionado

[[CC_BCNF]] · [[CC_3FN]] · [[CC_Cerradura_Atributos]] · [[CC_Dependencias_Funcionales]] · [[Clase 12 - Normalizacion Formas Normales]]
