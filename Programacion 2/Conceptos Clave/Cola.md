---
tags: [tda, cola, queue, fifo, estructuras-de-datos, programacion-2]
aliases: [Cola, Queue, FIFO]
---

# CC_Cola

> **FIFO** — First In, First Out. El **primero** que entra es el **primero** que sale.

## La analogía

La fila para cargar la **SUBE** a fin de mes. El que llega primero, se atiende primero. Respetá el orden.

## Operaciones (la interfaz)

```java
// Operaciones fundamentales de una Cola
InicializarCola();

Acolar(dato);   // Enqueue: a la fila
Desacolar();    // Dequeue: el siguiente, por favor

Primero();      // ¿A quién le toca?
ColaVacia();    // ¿Se fueron todos?
```

| Operación | Qué hace |
|---|---|
| `InicializarCola()` | Deja la cola lista y vacía |
| `Acolar(dato)` | Agrega al **final** |
| `Desacolar()` | Saca y devuelve el del **frente** |
| `Primero()` | Devuelve el del frente **sin sacarlo** |
| `ColaVacia()` | `true` si no queda nada |

## Cómo se ve

```
Desacolar ←  [ 3 | 5 | 2 | 0 | 9 ]  ← Acolar
              ↑
           Primero()
```

> A diferencia de la [[Pila]], acá se entra por un extremo y se sale por el otro.

## Usos típicos

- Cola de impresión
- Servidores web (atender pedidos en orden)
- Procesamiento de tareas por lotes

## Relacionado

- [[Clase 3 - Tipos de Datos Abstractos]]
- [[Pila]]
- [[Cola con Prioridad]]
- [[TDA]]
- [[Implementación Estática con Arreglos]]
