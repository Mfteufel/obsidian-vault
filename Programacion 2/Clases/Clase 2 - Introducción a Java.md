---
tags: [clase, java, poo, objetos, clases, tipos-de-datos, variables, estructuras-de-control, programacion-2]
---
![[Conceptos_Fundamentales_de_Orientacion_a_Objetos.pdf]]
# Clase 02 — Introducción a Java + Conceptos de Orientación a Objetos

**Materia:** Programación 2
**Profesores:** Ing. Pablo Hernández (POO)

> Dos bloques: la sintaxis básica de Java y los conceptos fundamentales del modelo de objetos.

---

## Agenda

**Bloque A — Java**
1. Qué es Java
2. Tipos de datos → [[Tipos de Datos]]
3. Variables → [[Variables]]
4. Operadores → [[Operadores]]
5. Estructuras de control → [[Estructuras de Control]]
6. Arreglos → [[Arreglos]]
7. Métodos → [[Métodos]]
8. Pasaje por valor y referencia → [[Pasaje por Valor y Referencia]]

**Bloque B — Orientación a Objetos**
9. Objeto → [[Objeto]]
10. Clase → [[Clases]]
11. Abstracción → [[Abstracción]]
12. Encapsulamiento → [[Encapsulamiento]]
13. Ocultamiento → [[Ocultamiento]]

---

# BLOQUE A — Java

## 1. Qué es Java

- Nació a comienzos de los años 90. Se llamó primero **Oak**, después **Green**, y finalmente **Java** (por el café de Indonesia).
- Lenguaje **orientado a objetos**, sintaxis parecida a C/C++.
- **Estáticamente tipado**: el tipo de cada variable se fija en tiempo de compilación.
- Más rápido en ejecución que Python, pero más lento de desarrollar.

## 2. Tipos de datos (resumen)

| Categoría | Tipos |
|---|---|
| Primitivos numéricos | `byte`, `short`, `int`, `long`, `float`, `double` |
| Primitivos no numéricos | `boolean`, `char` |
| No primitivos | Cadenas, Arreglos, Otros objetos |

> Tabla completa de rangos y tamaños en [[Tipos de Datos]]

## 3. Variables

```java
int contador;
int contador, cantidad;
int contador = 0;
char c = 'f';
```

> Detalle en [[Variables]]

## 4. Operadores (resumen)

| Tipo | Operadores |
|---|---|
| Aritméticos | `+ - * / % ++ --` |
| Relacionales | `> < >= <= == !=` |
| Lógicos | `! \|\| &&` |

> Detalle en [[Operadores]]

## 5. Estructuras de control (resumen)

- Condicional simple: `if / else`
- Condicional múltiple: `switch`
- Bucles: `while`, `do-while`, `for`

> Sintaxis y ejemplos completos en [[Estructuras de Control]]

## 6. Arreglos

```java
int[] vector;
vector = new int[20];
int[] valores = {1, 2, 3, 4};
```

> Detalle en [[Arreglos]]

## 7. Métodos

- Comparable a un procedimiento o función. Tiene tipo de retorno (o `void`).

> Desarrollo en [[Métodos]]

## 8. Pasaje por valor y por referencia

- Los **tipos primitivos** se pasan siempre **por valor**.
- Los **objetos** se pasan **por referencia**.

> Ejemplos de código en [[Pasaje por Valor y Referencia]]

---

# BLOQUE B — Orientación a Objetos

## 9. Objeto vs Clase

| | Qué es |
|---|---|
| **Clase** | El **molde o plantilla**. Define qué atributos y métodos van a existir |
| **Objeto** | Una **instancia** concreta creada a partir de ese molde |

> Una clase → muchos objetos.

## 10. Qué compone a un objeto

| Parte | Qué es |
|---|---|
| **Atributos** | Los datos que describen el **estado** del objeto |
| **Métodos** | La funcionalidad, las **operaciones** que puede realizar |

**Ejemplo — clase `Perro`:**

| Atributos | Métodos |
|---|---|
| Nombre, Raza, Altura | `Comer()`, `Dormir()`, `Ladrar()` |

> Todo objeto posee **identidad**, un **estado** (atributos) y un **comportamiento** (métodos).

## 11. Las 3 características del modelo

| Característica | En una línea |
|---|---|
| [[Abstracción]] | Quedarse solo con los aspectos relevantes de la realidad |
| [[Encapsulamiento]] | Datos + operaciones juntos en una misma "cápsula" |
| [[Ocultamiento]] | Mecanismo controlado de acceso (get/set) → así se logra el encapsulamiento |

---

## Ejercicio de autoevaluación

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

```java
import Persona.Persona;

public class Main {
    public static void main(String[] args) {
        Persona pablo = new Persona();
        pablo.darNombre("Pablo Hernandez");

        System.out.println("Pablo tiene " + pablo.decirEdad() + " años de edad");

        for (int i = 0; i < 10; i++) {
            pablo.cumplirAnios();
        }
        System.out.println("Ahora Pablo tiene " + pablo.decirEdad());
        System.out.println(pablo.saludar());
    }
}
```

**Preguntas:**
1. ¿Cuáles son los atributos de la clase `Persona`? → `nombre` (String) y `edad` (int), ambos `private`.
2. ¿Podemos identificar algún método? → `decirNombre()`, `decirEdad()`, `darNombre()`, `cumplirAnios()`.

> 💡 Ojo: `Main` llama a `pablo.saludar()`, pero ese método **no existe** en `Persona` → no compila.

---

## Bibliografía

- PRESSMAN, Roger. *Ingeniería del software: un enfoque práctico*. 7.ª ed., 2010. McGraw Hill.
- FONTELA, Carlos. *UML. Modelado de software para profesionales*. 2010.

---

## Relacionado

- [[Tipos de Datos]]
- [[Variables]]
- [[Operadores]]
- [[Estructuras de Control]]
- [[Arreglos]]
- [[Métodos]]
- [[Objeto]]
- [[Clases]]
- [[Abstracción]]
- [[Encapsulamiento]]
- [[Ocultamiento]]
- [[Pasaje por Valor y Referencia]]
