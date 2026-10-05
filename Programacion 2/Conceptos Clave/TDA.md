---
tags: [tda, abstraccion, estructuras-de-datos, programacion-2]
aliases: [TDA, Tipo de Dato Abstracto, Abstract Data Type, ADT]
---

# CC_TDA

> Un **Tipo de Dato Abstracto** es una estructura que funciona como **caja negra**: entran datos, salen resultados, y los detalles internos están ocultos.

## Las dos caras de un TDA

| | Semántica (el comportamiento) | Implementación (el cómo) |
|---|---|---|
| **Qué define** | Qué hace la estructura | Los detalles técnicos |
| **Punto de vista** | Del usuario | Del creador |
| **Ejemplo** | "Quiero guardar este número y sacarlo después" | "¿Lo guardo en un array? ¿Uso punteros?" |
| **Visible** | ✅ Sí | ❌ No — está oculto |

> **Separamos la especificación de la implementación.**

## Por qué importa

Si tu código depende de **cómo** está programado el TDA por dentro, se acopla fuertemente y se rompe cuando cambia la implementación.

```java
// ❌ MAL: dependés de la implementación interna
int ultimo = pila.arreglo[pila.tope];

// ✅ BIEN: usás el contrato público
int ultimo = pila.Tope();
```

> *"En mi compu anda joya, en producción es un desastre."* → eso pasa por no respetar el contrato.

## TDAs que vemos en la materia

| TDA | Regla |
|---|---|
| [[Pila]] | LIFO |
| [[Cola]] | FIFO |
| [[Cola con Prioridad]] | FIFO + prioridad |
| [[Conjunto]] | Sin orden, sin duplicados |
| [[Diccionario]] | Clave → Valor |

## Relacionado

- [[Clase 3 - Tipos de Datos Abstractos]]
- [[Interfaz]]
- [[Niveles de Abstracción]]
- [[Abstracción]]
- [[Encapsulamiento]]
