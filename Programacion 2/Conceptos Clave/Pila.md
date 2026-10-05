---
tags: [tda, pila, stack, lifo, estructuras-de-datos, programacion-2]
aliases: [Pila, Stack, LIFO]
---

# CC_Pila

> **LIFO** — Last In, First Out. El **último** que entra es el **primero** que sale.

## La analogía

Una pila gigante de **platos sucios** en la bacha. No podés sacar el de abajo sin romper todo — sacás el de arriba.

## Operaciones (la interfaz)

```java
// Operaciones fundamentales de una Pila
InicializarPila();

Apilar(dato);   // Push: agregar un plato
Desapilar();    // Pop: sacar el plato de arriba

Tope();         // Mirar el de arriba sin sacarlo
PilaVacia();    // ¿Quedan platos?
```

| Operación | Qué hace |
|---|---|
| `InicializarPila()` | Deja la pila lista y vacía |
| `Apilar(dato)` | Agrega un elemento arriba |
| `Desapilar()` | Saca y devuelve el de arriba |
| `Tope()` | Devuelve el de arriba **sin sacarlo** |
| `PilaVacia()` | `true` si no queda nada |

## Cómo se ve

```
   ↓ Apilar      ↑ Desapilar
 ┌─────────┐
 │    9    │  ← Tope()
 ├─────────┤
 │    3    │
 ├─────────┤
 │    0    │
 ├─────────┤
 │    2    │
 ├─────────┤
 │    5    │
 └─────────┘
```

> Apilar y Desapilar trabajan **siempre por el mismo extremo**.

## Usos típicos

- **Ctrl+Z** (deshacer)
- Historial del navegador (botón "atrás")
- Llamadas a funciones (el *call stack*)

## Relacionado

- [[Clase 3 - Tipos de Datos Abstractos]]
- [[Cola]]
- [[TDA]]
- [[Implementación Estática con Arreglos]]
