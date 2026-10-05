---
tags: [poo, abstraccion, modelado, programacion-2]
aliases: [Abstracción, Abstraction, Modelo de la realidad]
---

# CC_Abstracción

> Se intenta obtener un **modelo de la realidad**, quedándonos solo con los aspectos **relevantes** de aquello que queremos representar.

## La idea en una línea

No modelás *todo* lo que existe — modelás **solo lo que tu sistema necesita**.

## Ejemplo — modelar una Persona

Una persona real tiene miles de características. Pero si estás haciendo un sistema de inscripciones, alcanza con:

| Atributo | ¿Se incluye? |
|---|---|
| Nombre | ✅ Sí |
| Edad | ✅ Sí |
| DNI | ✅ Sí |
| Color de ojos | ❌ No aporta |
| Comida favorita | ❌ No aporta |

```java
class Persona {
    private String nombre;
    private int edad;
    private String dni;
}
```

## Depende del contexto

La misma entidad se abstrae distinto según para qué:

| Sistema | Atributos relevantes de "Persona" |
|---|---|
| Universidad | Legajo, carrera, materias aprobadas |
| Hospital | Grupo sanguíneo, alergias, obra social |
| Banco | CUIT, saldo, historial crediticio |

> No hay una abstracción "correcta" única — hay una correcta **para tu problema**.

## Relacionado

- [[Clase 2 - Introducción a Java]]
- [[Clase 3 - Tipos de Datos Abstractos]]
- [[Objeto]]
- [[Clases]]
- [[Encapsulamiento]]
- [[TDA]]
- [[Niveles de Abstracción]]
