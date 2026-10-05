---
tags: [java, arreglos, arrays, programacion-2]
aliases: [Arreglos Java, Arrays Java, Vectores Java]
---

# CC_Arreglos

> Un arreglo (array) guarda varios valores del mismo tipo bajo un mismo nombre.

## Declaración

```java
<tipo de datos> [] <nombre del arreglo>;
```

**Ejemplo:**

```java
int[] vector;
```

## Reservar memoria (tamaño)

Antes de usarlo hay que darle tamaño con `new`:

```java
vector = new int[20];
```

## Declarar e inicializar con valores

```java
int[] valores = {1, 2, 3, 4};
```

→ esto crea el arreglo `[1, 2, 3, 4]`.

## Características

| Característica | Detalle |
|---|---|
| **Tamaño** | Fijo — se define al crear y no cambia |
| **Memoria** | Contigua |
| **Acceso** | Directo por índice, O(1) |
| **Índices** | Arrancan en **0** |

## Uso como base de TDAs

Los arreglos son la base de la **implementación estática** de [[Pila]], [[Cola]] y demás estructuras.

> Ver las 3 estrategias en [[Implementación Estática con Arreglos]]

## Relacionado

- [[Clase 2 - Introducción a Java]]
- [[Clase 3 - Tipos de Datos Abstractos]]
- [[Tipos de Datos]]
- [[Métodos]]
- [[Implementación Estática con Arreglos]]
