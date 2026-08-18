---
tags: [poo, ocultamiento, getters, setters, programacion-2]
aliases: [Ocultamiento, Ocultación, Information Hiding, Getters y Setters, Get y Set]
---

# CC_Ocultamiento

> Proveer un **mecanismo controlado** para acceder a los atributos de las clases.

## La idea en una línea

Los atributos se declaran **privados**. Se accede a ellos solo por métodos públicos (**get** y **set**).

> El ocultamiento de la información es el **mecanismo mediante el cual obtenemos el [[Encapsulamiento]]**.

## Modificadores de acceso

| Modificador | Quién puede acceder |
|---|---|
| `private` | Solo la propia clase |
| `public` | Cualquiera |

## Getter y Setter

| Método | Para qué |
|---|---|
| **GET** | Leer el valor del atributo |
| **SET** | Modificar el valor del atributo |

```java
public class Persona {
    private String nombre;   // oculto
    private int edad;

    // GET
    public String getNombre() {
        return nombre;
    }

    // SET
    public void setNombre(String nombre) {
        this.nombre = nombre;
    }

    public int getEdad() {
        return edad;
    }

    public void setEdad(int edad) {
        if (edad >= 0) {      // ← acá está el control
            this.edad = edad;
        }
    }
}
```

## ¿Para qué sirve el control?

Si el atributo fuera público, cualquiera podría hacer:

```java
persona.edad = -50;   // ❌ estado inválido
```

Con setter, se valida antes de asignar:

```java
persona.setEdad(-50);   // ✅ el método lo rechaza
```

## Variante del PDF

En la clase los getters aparecen con otros nombres — la idea es la misma:

```java
public String decirNombre() {   // getter
    return nombre;
}

public void darNombre(String nombre) {   // setter
    this.nombre = nombre;
}
```

## Relacionado

- [[Clase 2 - Introducción a Java]]
- [[Encapsulamiento]]
- [[Clases]]
- [[Objeto]]
