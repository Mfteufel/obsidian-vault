---
tags: [android, dependency-injection, solid, ioc, arquitectura, desarrollo-aplicaciones-1]
aliases: [DI, Inyección de Dependencias, Dependency Injection, Dependency Inversion, IoC, Inversión de Control]
---

# CC_Inyección de Dependencias (DI)

> Patrón de diseño donde las dependencias de una clase se pasan **desde afuera** (por constructor, campo o método) en vez de que la clase las cree ella misma. Genera **desacoplamiento**.

## La cadena de conceptos

```
SOLID (principio) → IoC (principio) → DI (técnica) → Hilt (herramienta)
```

| Nivel | Concepto | Frase clave |
|---|---|---|
| Principio (SOLID) | **Dependency Inversion** (la D de SOLID) | "Los módulos de alto nivel no deben depender de módulos de bajo nivel. Ambos deben depender de abstracciones" |
| Principio | **Inversión de Control (IoC)** | "No busques tus dependencias, ya te las doy yo" |
| Técnica | **Inyección de Dependencias** | Pasar dependencias desde afuera para desacoplar |
| Herramienta | **[[Hilt]]** | Automatiza la DI con `@Inject` y `@Provides` |

---

## El problema que resuelve

Con un **singleton manual** (como `RetrofitClient.getInstance()`), tu Fragment (alto nivel) **conoce y busca activamente** la implementación concreta (bajo nivel).

```java
// ANTES — sin DI
Retrofit retrofit = RetrofitClient.getInstance();
PokemonApiService api = retrofit.create(PokemonApiService.class);
```

Esto trae 3 problemas cuando la app crece:

| Necesitás... | Con singleton manual |
|---|---|
| Agregar un header de `Authorization` a **todos** los requests | Hay que tocar la clase a mano en un solo lugar rígido |
| **Testear** Fragments que usan el service | El `static` no se puede reemplazar por un mock |
| Compartir la misma instancia entre Fragments **sin `static`** | Imposible, el singleton manual depende de `static` |

---

## Con DI

```java
// DESPUÉS — con DI
@Inject
PokemonApiService api;
// Un agente externo (Hilt) crea Retrofit, lo configura,
// e inyecta el service ya listo para usar
```

El Fragment **ya no busca** su dependencia — solo la **declara**. El **framework** (Hilt) se la entrega. Eso es Inversión de Control en acción.

---

## Comparación directa

| | **Sin DI** (singleton manual) | **Con DI** |
|---|---|---|
| ¿Quién crea el objeto? | La propia clase, buscándolo activamente | Un agente externo (el framework) |
| Acoplamiento | Alto — conoce la implementación concreta | Bajo — depende de una abstracción |
| Testeable | Difícil (el `static` no se mockea) | Fácil (se inyecta un mock) |
| Compartir instancia | Solo con `static` | Con scopes (`@Singleton`, etc.) |

---

## Las 3 formas de inyectar

| Forma | Ejemplo |
|---|---|
| **Por constructor** | `public LoginViewModel(ApiService api) { this.api = api; }` |
| **Por campo** | `@Inject PokemonApiService api;` |
| **Por método** | `@Inject public void setApi(ApiService api) { ... }` |

> En Android con Hilt, la inyección **por campo** es la más común dentro de Activities y Fragments.

---

## Cómo lo automatiza Hilt

DI a mano (sin framework) implicaría crear y pasar cada dependencia manualmente en cada punto donde se usa — mucho código repetido. **Hilt** automatiza esto con anotaciones:

| Anotación | Reemplaza a... |
|---|---|
| `@Inject` | El "buscar" la dependencia manualmente |
| `@Provides` | La lógica de `getInstance()` de un singleton manual |
| `@Singleton` | El campo `static` que guardaba la única instancia |

> Setup completo, paso a paso: [[Hilt]]

---

## Relacionado

- [[Clase 04 - Inyección de Dependencias y Storage]]
- [[Hilt]]
- [[Retrofit - Consumo de API REST]]
- [[Fragment]]
