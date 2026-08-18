---
tags: [java, clases, poo, programacion-2]
aliases: [Clases Java, Classes Java, Clase, Molde, Plantilla]
---

# CC_Clases

> Una clase es el **molde o plantilla** a partir del cual se crean los objetos. Define qué atributos y métodos van a tener.

## Clase vs Objeto

| | Qué es | Ejemplo |
|---|---|---|
| **Clase** | El molde. Existe una sola | `Perro` |
| **Objeto** | La instancia concreta. Puede haber muchas | `firulais`, `rocky`, `luna` |

> Una clase → múltiples objetos.

Informalmente también se puede pensar una clase como una **estructura** que agrupa variables y métodos.

---

## Sintaxis

```java
class <nombre clase> {
    <tipo de variable> <nombre de variable>;

    <tipo de método> <nombre de método>(<parámetros>) {
        instrucción1;
        instrucción2;
    }
}
```

## Ejemplo simple

```java
class Punto {
    int x;
    int y;
}
```

## Ejemplo completo (con métodos y ocultamiento)

```java
package Persona;

public class Persona {
    private String nombre;
    private int edad;

    public String decirNombre() {
        return nombre;
    }

    public int decirEdad() {
        return edad;
    }

    public void darNombre(String nombre) {
        this.nombre = nombre;
    }

    public void cumplirAnios() {
        edad++;
    }
}
```

- Atributos → `nombre`, `edad` (privados)
- Métodos → `decirNombre()`, `decirEdad()`, `darNombre()`, `cumplirAnios()`

---

## Usar una clase (instanciar)

Se usa igual que un tipo primitivo, pero antes hay que reservarle memoria con `new`:

```java
<nombre clase> <nombre variable>;
<nombre variable> = new <nombre clase>();
```

**Ejemplo:**

```java
Punto p;
p = new Punto();
```

O todo junto:

```java
Punto p = new Punto();
```

Acceder a sus atributos con `.`:

```java
p.x = 5;
p.y = 2;
```

Usar sus métodos:

```java
Persona pablo = new Persona();
pablo.darNombre("Pablo Hernandez");
System.out.println(pablo.decirNombre());
```

---

## Paquetes

- Las bibliotecas de Java se llaman **paquetes**. Se accede con notación de punto.
- `java.lang` es el paquete que se usa casi siempre → contiene la clase `System`.

```java
int n = 3;
char c = 'x';
System.out.println(n);
System.out.println(c);
System.out.println("Hola!");
```

## Relacionado

- [[Clase 2 - Introducción a Java]]
- [[Objeto]]
- [[Métodos]]
- [[Encapsulamiento]]
- [[Ocultamiento]]
- [[Pasaje por Valor y Referencia]]
