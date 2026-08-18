---
tags: [java, metodos, programacion-2]
aliases: [Métodos Java, Methods Java, Funciones Java]
---

# CC_Métodos

> Un método es comparable a un procedimiento o función. Tiene un tipo de retorno (o `void` si no devuelve nada).

## Sintaxis

```java
<tipo de retorno> <nombre del método>(<tipo1> <param1>, ..., <tipoN> <paramN>) {
    instrucción1;
    instrucción2;
}
```

## Ejemplo

```java
int sumar(int num1, int num2) {
    int suma = 0;
    suma = num1 + num2;
    return suma;
}
```

Versión más corta, sin variable intermedia:

```java
int sumar(int num1, int num2) {
    return num1 + num2;
}
```

## Relacionado

- [[Clase 2 - Introducción a Java]]
- [[Clases]]
- [[Pasaje por Valor y Referencia]]
