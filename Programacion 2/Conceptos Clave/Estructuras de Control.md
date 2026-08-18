---
tags: [java, estructuras-de-control, programacion-2]
aliases: [Estructuras de Control Java, if else Java, switch Java, while Java, for Java]
---

# CC_Estructuras de Control

> Controlan el flujo de ejecución de un programa: condicionales y bucles.

## Bloques

- Se delimitan con llaves `{ }`.
- Si el bloque tiene una sola sentencia, las llaves son opcionales.
- La indentación es solo para leer mejor — el compilador la ignora.

## Condicional simple (if / else)

```java
if (condición booleana) {
    bloque 1
}
```

```java
if (condición booleana) {
    bloque 1
} else {
    bloque 2
}
```

## Condicional múltiple (switch)

```java
switch (expresión entera) {
    case valor1:
        sentencia1;
        break;
    case valor2:
        sentencia2;
        break;
    default:
        sentencia3;
}
```

> ⚠️ No olvidar el `break;` — si falta, sigue ejecutando el siguiente `case`.

## if-else encadenado vs switch

```java
if (nota == 10)
    System.out.println("Sobresaliente");
else if (nota == 9)
    System.out.println("Distinguido");
else if (nota >= 7)
    System.out.println("Muy bueno");
else
    System.out.println("No alcanzó");
```

> Ambos resuelven lo mismo. `switch` es más prolijo cuando comparás un mismo valor contra opciones puntuales; `if-else` es mejor para rangos y condiciones más complejas.

## Bucles

```java
while (condición booleana) {
    sentencia1;
    sentencia2;
}
```

```java
do {
    sentencia1;
    sentencia2;
} while (condición booleana);
```

```java
for (inicialización; condición; variación) {
    sentencia1;
    sentencia2;
}
```

## Relacionado

- [[Clase 2 - Introducción a Java]]
- [[Operadores]]
- [[Arreglos]]
