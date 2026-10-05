---
tags: [clase, tda, estructuras-de-datos, pilas, colas, conjuntos, diccionarios, programacion-2]
---
![[Clase_2.pdf]]
# Clase 03 — Tipos de Datos Abstractos (TDA)

**Materia:** Programación 2

> Separar **qué hace** una estructura de **cómo está hecha por dentro**. Esa es toda la idea del TDA.

---

## Agenda

1. Qué es un TDA → [[TDA]]
2. Niveles de abstracción → [[Niveles de Abstracción]]
3. La interfaz como contrato → [[Interfaz]]
4. Pilas y Colas (LIFO vs FIFO) → [[Pila]] · [[Cola]]
5. Colas con prioridad → [[Cola con Prioridad]]
6. Conjuntos y Diccionarios → [[Conjunto]] · [[Diccionario]]
7. Implementación estática con arreglos → [[Implementación Estática con Arreglos]]

---

## 1. Qué es un TDA

Un **Tipo de Dato Abstracto** funciona como una **caja negra**: le entran datos, salen resultados, y no te importa qué pasa adentro.

| Parte | Qué es |
|---|---|
| **Semántica** (el comportamiento) | Qué hace la estructura desde el punto de vista del usuario |
| **Implementación** (el cómo) | Los detalles técnicos ocultos: arrays, punteros, etc. |

> Separamos la **especificación** de la **implementación**.

> Detalle en [[TDA]]

## 2. Niveles de abstracción

| Nivel | Quién | Qué necesita saber |
|---|---|---|
| **Alto** | El usuario | Solo manejar. El volante gira, el acelerador avanza |
| **Bajo** | El creador | Inyección de combustible, pistones, transmisión |

> La programación avanzada exige pensar como el **mecánico**, mientras le construís el auto al **usuario**.

> Detalle en [[Niveles de Abstracción]]

## 3. La interfaz: el contrato

- La **interfaz** es una clase abstracta con los métodos que el TDA ofrece.
- Si prometiste `apilar()`, lo tenés que cumplir.
- **Intercambiabilidad**: si respetás la interfaz, podés cambiar toda la implementación interna y el usuario ni se entera.

**El costo de no usar TDAs:**

> Si tu código depende de CÓMO está programado el TDA por dentro (ej: accedés directo al índice del arreglo en vez de usar el método), el día que se cambie la implementación **explota todo**.

> Detalle en [[Interfaz]]

## 4. Pilas vs Colas

| | Pila (Stack) | Cola (Queue) |
|---|---|---|
| **Regla** | LIFO — último en entrar, primero en salir | FIFO — primero en entrar, primero en salir |
| **Analogía** | Pila de platos sucios | Fila para cargar la SUBE |
| **Uso típico** | Ctrl+Z, historial del navegador | Cola de impresión, servidores web |
| **Operaciones** | `Apilar()`, `Desapilar()`, `Tope()` | `Acolar()`, `Desacolar()`, `Primero()` |

> Detalle en [[Pila]] y [[Cola]]

## 5. Otros TDAs

| TDA | En una línea |
|---|---|
| [[Cola con Prioridad]] | Cola donde cada elemento trae un "pase VIP" |
| [[Conjunto]] | Sin orden, **sin duplicados** |
| [[Diccionario]] | Mapeo clave → valor |

## 6. Implementación estática

**Hoy (estático):** los TDAs se guardan en **arreglos**. Tamaño fijo, memoria contigua. Seguro pero rígido.

**Más adelante (dinámico):** listas enlazadas con punteros.

Las **3 estrategias** con arreglos:

| Estrategia | Alineación | Puntero |
|---|---|---|
| 1 | Izquierda | Variable `int` separada apunta a la primera posición libre |
| 2 | Derecha (tope en índice 0) | Costoso: hay que desplazar todo el arreglo, O(n) |
| 3 | Izquierda | Sin variable separada: se sacrifica el índice 0 para guardar la cantidad |

> Detalle en [[Implementación Estática con Arreglos]]

---

## Tabla resumen — Caja de herramientas

| TDA | Regla de flujo | Analogía | Operaciones clave |
|---|---|---|---|
| **Pila** | LIFO | Platos sucios | `Apilar()`, `Desapilar()` |
| **Cola** | FIFO | Fila del supermercado | `Acolar()`, `Desacolar()` |
| **Conjunto** | Sin orden, sin duplicados | Bolsa de sorpresas | `Agregar()`, `Pertenece()` |
| **Diccionario** | Mapeo clave-valor | Agenda telefónica | `Agregar(K,V)`, `Recuperar(K)` |

---

## Relacionado

- [[TDA]]
- [[Niveles de Abstracción]]
- [[Interfaz]]
- [[Pila]]
- [[Cola]]
- [[Cola con Prioridad]]
- [[Conjunto]]
- [[Diccionario]]
- [[Implementación Estática con Arreglos]]
- [[Abstracción]]
- [[Encapsulamiento]]
- [[Arreglos]]
- [[Clase 2 - Introducción a Java]]
