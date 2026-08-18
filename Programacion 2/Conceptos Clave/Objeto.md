---
tags: [java, poo, objetos, programacion-2]
aliases: [Objeto, Objetos, Instancia, Object]
---

# CC_Objeto

> Un objeto es una entidad que combina, en una sola unidad, sus **datos** y su **comportamiento**.

## Las dos partes de un objeto

| Parte | Qué es |
|---|---|
| **Atributos** | Los datos que describen el **estado** del objeto |
| **Métodos** | La funcionalidad, las **operaciones** que puede realizar |

## Características de todo objeto

- **Identidad** → es distinguible de cualquier otro
- **Estado** → definido por sus atributos
- **Comportamiento** → dado por sus métodos

## Ejemplo — clase Perro

| Atributos | Métodos |
|---|---|
| Nombre | `Comer()` |
| Raza | `Dormir()` |
| Altura | `Ladrar()` |

```java
class Perro {
    String nombre;
    String raza;
    double altura;

    void comer() { }
    void dormir() { }
    void ladrar() {
        System.out.println("Guau!");
    }
}
```

## Crear un objeto (instanciar)

```java
Perro firulais = new Perro();
firulais.nombre = "Firulais";
firulais.ladrar();
```

> `Perro` es la **clase** (el molde). `firulais` es el **objeto** (la instancia).

## Relacionado

- [[Clase 2 - Introducción a Java]]
- [[Clases]]
- [[Encapsulamiento]]
- [[Abstracción]]
