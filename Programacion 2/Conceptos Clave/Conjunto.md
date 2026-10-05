---
tags: [tda, conjunto, set, estructuras-de-datos, programacion-2]
aliases: [Conjunto, Set, Conjuntos]
---

# CC_Conjunto

> Una colección **sin orden posicional** y con una regla única: **NO SE ADMITEN DUPLICADOS**.

## Las 2 reglas

| Regla | Qué implica |
|---|---|
| **Sin orden** | No importa quién entró primero. No hay "posición 3" |
| **Sin duplicados** | Si el elemento ya está, no entra de nuevo |

## Analogía

Una **bolsa de sorpresas**: metés cosas, sacás cosas, pero no hay fila ni podés meter dos veces lo mismo.

## Operaciones (la interfaz)

```java
Agregar(dato);              // Entra solo si no es un clon
Sacar(dato);                // Elimina el elemento
Pertenece(dato) -> boolean; // ¿Está o no está?
Elegir();                   // Saca un elemento arbitrario
```

| Operación | Devuelve |
|---|---|
| `Agregar(dato)` | — (ignora si ya existe) |
| `Sacar(dato)` | — |
| `Pertenece(dato)` | `boolean` |
| `Elegir()` | Un elemento **cualquiera** (arbitrario) |

## Uso real

Ideal para **verificar membresías rápidamente**:

> ¿Este usuario ya existe en el sistema?

```java
if (usuariosRegistrados.Pertenece(dni)) {
    System.out.println("Ya está registrado");
}
```

## Relacionado

- [[Clase 3 - Tipos de Datos Abstractos]]
- [[Diccionario]]
- [[TDA]]
- [[Pila]]
- [[Cola]]
