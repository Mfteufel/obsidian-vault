---
tags: [tda, abstraccion, programacion-2]
aliases: [Niveles de Abstracción, Nivel alto, Nivel bajo, Usuario vs Creador]
---

# CC_Niveles de Abstracción

> El mismo sistema se ve distinto según desde dónde lo mires.

## Auto vs Mecánico

| | Nivel Alto (El Usuario) | Nivel Bajo (El Creador) |
|---|---|---|
| **Quién** | El que maneja | El ingeniero / mecánico |
| **Qué sabe** | El volante gira, el acelerador avanza | Inyección de combustible, pistones, transmisión |
| **Detalles del motor** | Intencionalmente ocultos | Todos visibles |

> La programación avanzada exige que aprendas a **pensar como el mecánico**, mientras le **construís el auto al usuario**.

## Mascota vs Veterinario

| Abstracción de Usuario | Abstracción de Implementación |
|---|---|
| Comportamiento esperado | Estructura interna |
| Ronronea, come, duerme | Órganos, huesos, sistemas biológicos |

> Un [[TDA]] permite interactuar con el "gato" sin tener que operar sus "órganos" manualmente cada vez que querés que maúlle.

## Aplicado a código

```java
// NIVEL ALTO — lo que ve el usuario del TDA
Pila p = new Pila();
p.Apilar(5);
int x = p.Desapilar();

// NIVEL BAJO — lo que sabe el que la implementó
// ¿arreglo o lista enlazada? ¿el tope está al inicio o al final?
// ¿qué pasa si se llena? → problema del creador, no del usuario
```

## Relacionado

- [[Clase 3 - Tipos de Datos Abstractos]]
- [[TDA]]
- [[Interfaz]]
- [[Abstracción]]
