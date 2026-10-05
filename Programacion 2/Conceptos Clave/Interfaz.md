---
tags: [tda, interfaz, contrato, programacion-2]
aliases: [Interfaz, Interface, Contrato, Clase abstracta]
---

# CC_Interfaz

> La interfaz es el **contrato**: la lista de métodos que el [[TDA]] promete ofrecer.

## Las 3 ideas clave

| Idea | Qué significa |
|---|---|
| **El Contrato** | Es una clase abstracta con los métodos que el TDA ofrece. Si prometiste `apilar()`, lo tenés que cumplir |
| **Intercambiabilidad** | Si respetás la interfaz, podés cambiar TODA la implementación interna (de estática a dinámica) y el usuario ni se entera |
| **La Analogía** | Es como el enchufe de la pared. No te importa si la electricidad viene de una represa o de un panel solar — vos enchufás y carga |

## Ejemplo en Java

```java
public interface Pila {
    void Apilar(int dato);
    int Desapilar();
    int Tope();
    boolean PilaVacia();
}
```

Dos implementaciones distintas, **misma interfaz**:

```java
public class PilaConArreglo implements Pila {
    private int[] datos = new int[100];
    private int tope = 0;
    // ...
}

public class PilaConLista implements Pila {
    private Nodo cabeza;
    // ...
}
```

El código que las usa **no cambia**:

```java
Pila p = new PilaConArreglo();   // o new PilaConLista()
p.Apilar(5);
```

## El costo de romper el contrato

> ⚠️ **ERROR: Dependencia fuertemente acoplada.**
> Si accedés directo al índice del arreglo en vez de usar el método, el día que se cambie la implementación explota todo.

## Relacionado

- [[Clase 3 - Tipos de Datos Abstractos]]
- [[TDA]]
- [[Niveles de Abstracción]]
- [[Encapsulamiento]]
- [[Ocultamiento]]
