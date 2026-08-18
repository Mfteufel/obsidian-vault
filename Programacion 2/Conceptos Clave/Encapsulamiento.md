---
tags: [poo, encapsulamiento, programacion-2]
aliases: [Encapsulamiento, Encapsulation, Cápsula]
---

# CC_Encapsulamiento

> Poner en una misma entidad, o **"cápsula"**, los datos y las operaciones posibles sobre esos datos.

*(Rebecca Wirfs-Brock — Designing Object-Oriented Software)*

## La idea en una línea

La cápsula agrupa **datos + operaciones** como una sola unidad protegida.

## Sin encapsular vs encapsulado

| Sin encapsular | Encapsulado |
|---|---|
| Los datos andan sueltos | Datos y métodos viven juntos en la clase |
| Cualquiera los modifica | Solo los métodos de la clase los tocan |
| Fácil de romper | Estado consistente y protegido |

## Ejemplo

```java
public class CuentaBancaria {
    private double saldo;   // el dato

    public void depositar(double monto) {   // la operación
        if (monto > 0) {
            saldo += monto;
        }
    }

    public double consultarSaldo() {
        return saldo;
    }
}
```

- El `saldo` **no se puede tocar desde afuera**.
- Solo se modifica pasando por `depositar()`, que valida que el monto sea positivo.

## Relación con el ocultamiento

> El **ocultamiento** de la información es el **mecanismo** mediante el cual obtenemos el encapsulamiento.

Es decir: encapsulamiento es el *qué*, ocultamiento es el *cómo* → [[Ocultamiento]]

## Relacionado

- [[Clase 2 - Introducción a Java]]
- [[Ocultamiento]]
- [[Objeto]]
- [[Clases]]
- [[Abstracción]]
