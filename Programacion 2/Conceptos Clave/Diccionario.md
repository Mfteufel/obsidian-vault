---
tags: [tda, diccionario, map, clave-valor, estructuras-de-datos, programacion-2]
aliases: [Diccionario, Map, Diccionarios, Clave Valor, Key Value]
---

# CC_Diccionario

> No buscás el dato secuencialmente. Tenés una **Clave** y esa clave te entrega directamente el **Valor**.

## Analogía

Una **agenda telefónica**: buscás el nombre (clave) y obtenés el número (valor). No leés toda la agenda de arriba a abajo.

## Dos tipos

| | Diccionario Simple | Diccionario Múltiple |
|---|---|---|
| **Concepto** | Una clave, **UN** único valor | Una clave, **MUCHOS** valores |
| **Visual** | Buscás una palabra → obtenés una definición | Buscás "Juan" → obtenés casa, celular y trabajo |
| **Ejemplo** | DNI → Persona | Juan → {555-0100, 555-0101, 555-0102} |

## Operaciones

**Diccionario Simple:**

```java
Agregar(clave, valor);
Recuperar(clave);
Eliminar(clave);
```

**Diccionario Múltiple — método extra:**

```java
EliminarValor(clave, valorEspecífico);
```

> Necesitás `EliminarValor()` porque `Eliminar(clave)` te borraría **todos** los valores de esa clave.

## Ejemplo

```java
// Simple
agenda.Agregar("12345678", "Marco Pérez");
String nombre = agenda.Recuperar("12345678");

// Múltiple
telefonos.Agregar("Juan", "555-0100");
telefonos.Agregar("Juan", "555-0101");
telefonos.EliminarValor("Juan", "555-0100");   // borra solo ese
```

## Relacionado

- [[Clase 3 - Tipos de Datos Abstractos]]
- [[Conjunto]]
- [[TDA]]
- [[Cola]]
