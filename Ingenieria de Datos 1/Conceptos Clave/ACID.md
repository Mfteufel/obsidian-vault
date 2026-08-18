

## Definición

**ACID** es un conjunto de propiedades que garantizan que las **transacciones en una base de datos** se ejecuten de manera **segura, consistente y confiable**.

ACID significa:

- **Atomicity (Atomicidad)**
    
- **Consistency (Consistencia)**
    
- **Isolation (Aislamiento)**
    
- **Durability (Durabilidad)**
    

Estas propiedades aseguran que los datos **no se corrompan**, incluso si ocurren errores o fallos del sistema.

---

# Atomicidad (Atomicity)

## Concepto

Una transacción se ejecuta **completamente o no se ejecuta en absoluto**.

Si una parte falla, **toda la transacción se revierte (rollback)**.

## Ejemplo

Transferencia bancaria:

1. Restar $100 de la cuenta A  
2. Sumar $100 a la cuenta B

Si el sistema falla después del paso 1, la base de datos **deshace la operación** para evitar perder dinero.

## Idea clave

Todo o nada

---

# Consistencia (Consistency)

## Concepto

Una transacción debe llevar la base de datos de **un estado válido a otro estado válido**.

Las reglas y restricciones del sistema **siempre deben cumplirse**.

## Ejemplo

Si una tabla define:

saldo >= 0

El sistema no permitirá una operación que deje un saldo negativo.

## Idea clave

Los datos siempre cumplen las reglas del sistema

---

# Aislamiento (Isolation)

## Concepto

Las transacciones que se ejecutan al mismo tiempo **no deben interferir entre sí**.

Cada transacción se comporta como si fuera **la única ejecutándose**.

## Ejemplo

Dos usuarios intentan comprar el **último producto en stock**.

El sistema controla que:

- solo una transacción se complete
    
- la otra reciba un error o espere
    

## Idea clave

Las transacciones concurrentes no se mezclan

---

# Durabilidad (Durability)

## Concepto

Una vez que una transacción se confirma (**COMMIT**), los datos **no se pierden**, incluso si el sistema falla.

La información queda almacenada permanentemente.

## Ejemplo

Si realizas una compra online y recibes confirmación, el pedido **no desaparecerá aunque el servidor se reinicie**.

Esto se logra mediante:

- logs de transacciones
    
- almacenamiento en disco
    
- mecanismos de recuperación
    

## Idea clave

Una vez confirmado, el dato es permanente

---

# Resumen rápido

|Propiedad|Significado|
|---|---|
|Atomicidad|todo o nada|
|Consistencia|respeta reglas del sistema|
|Aislamiento|transacciones independientes|
|Durabilidad|datos permanentes|
