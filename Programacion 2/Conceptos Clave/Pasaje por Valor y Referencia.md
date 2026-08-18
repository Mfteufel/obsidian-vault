---
tags: [java, pasaje-por-valor, pasaje-por-referencia, programacion-2]
aliases: [Pasaje por Valor Java, Pasaje por Referencia Java, Pass by value, Pass by reference]
---

# CC_Pasaje por Valor y Referencia

> Cómo se comportan las variables cuando se las pasa a un método.

## Reglas

| Tipo de dato | Cómo se pasa |
|---|---|
| Primitivos (`int`, `char`, etc.) | **Siempre por valor** |
| Objetos (clases) | **Por referencia** (la referencia en sí se pasa por valor) |

## Pasaje por valor

Se pasa una **copia** del valor. El método modifica la copia — la variable original **no cambia**.

```java
public static void cambiar(int u) {
    u = u * 5;
}

public static void main(String[] args) {
    int u = 5;
    cambiar(u);
    System.out.println(u); // sigue siendo 5
}
```

## Pasaje por referencia

Se pasa la **dirección de memoria**. El método trabaja directo sobre el objeto original — **sí cambia**.

```java
public static class Num {
    int info;
}

public static void cambiar(Num u) {
    u.info = u.info * 5;
}

public static void main(String[] args) {
    Num u = new Num();
    u.info = 5;
    cambiar(u);
    System.out.println(u.info); // ahora es 25
}
```

## Relacionado

- [[Clase 2 - Introducción a Java]]
- [[Métodos]]
- [[Clases]]
