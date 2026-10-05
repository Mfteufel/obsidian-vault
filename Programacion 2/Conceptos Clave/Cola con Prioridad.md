---
tags: [tda, cola, prioridad, estructuras-de-datos, programacion-2]
aliases: [Cola con Prioridad, Priority Queue, Cola de Prioridad]
---

# CC_Cola con Prioridad

> Funciona como una [[Cola]] normal, pero cada elemento trae un **"pase VIP"** (prioridad).

## La regla

Un elemento con **prioridad alta** siempre se adelanta a uno de **baja**, sin importar cuándo llegó.

## Analogía

El patovica de un boliche. Igual que una fila, pero el VIP se cuela y pasa primero.

## ¿Y los empates?

Si dos elementos tienen la **misma prioridad**, el **implementador decide** las reglas de desempate (normalmente: el que llegó primero).

## Operaciones extra

```java
AcolarPrioridad(dato, prioridad);   // agrega con su nivel de prioridad
Prioridad();                        // consulta la prioridad del primero
```

> Además de esto, mantiene las operaciones de una cola normal: `Desacolar()`, `Primero()`, `ColaVacia()`.

## Ejemplo mental

| Llega | Dato | Prioridad | Sale en orden |
|---|---|---|---|
| 1º | A | Baja | 3º |
| 2º | B | Alta | 1º |
| 3º | C | Media | 2º |

> Aunque A llegó primero, sale último — la prioridad manda.

## Usos típicos

- Triage en una guardia médica
- Planificador de procesos del sistema operativo
- Algoritmos como Dijkstra

## Relacionado

- [[Clase 3 - Tipos de Datos Abstractos]]
- [[Cola]]
- [[Pila]]
- [[TDA]]
