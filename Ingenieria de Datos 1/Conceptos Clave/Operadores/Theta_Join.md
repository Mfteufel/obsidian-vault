---
tags: [base-de-datos, algebra-relacional, sql-server, joins]
aliases: [Theta Join, θ-join, Join con condición]
---

# CC_Theta Join

> Join que combina dos relaciones aplicando una **condición arbitraria** (θ) sobre sus atributos.

## Concepto
El Theta Join es un Producto Cartesiano seguido de una Selección con condición θ.

**Notación formal:**
$$R \bowtie_{\theta} S = \sigma_{\theta}(R \times S)$$

## Theta Join — ⋈θ

**Tabla R**

| id  | valor |
| --- | ----- |
| 1   | 10    |
| 2   | 50    |
| 3   | 80    |

**Tabla S**

| id  | limite |
| --- | ------ |
| A   | 40     |
| B   | 70     |

**Resultado: R ⋈ R.valor > S.limite S**

| R.id | R.valor | S.id | S.limite |
| ---- | ------- | ---- | -------- |
| 2    | 50      | A    | 40       |
| 3    | 80      | A    | 40       |
| 3    | 80      | B    | 70       |



La condición θ puede usar: `=`, `≠`, `<`, `>`, `≤`, `≥`

## Equivalencia en SQL Server
```sql
SELECT *
FROM TablaA
JOIN TablaB ON condición_theta;
```

## Ejemplo práctico
Pedidos cuyo monto supera el crédito del cliente:

```sql
SELECT p.id_pedido, c.nombre, p.monto, c.credito
FROM Pedidos p
JOIN Clientes c ON p.monto > c.credito;
```

## Propiedades
- El **Equi Join** es un caso particular con θ siendo `=`.
- Puede generar resultados muy grandes si la condición no es selectiva.
- No elimina columnas duplicadas (a diferencia del Natural Join).

## Relacionado
- [[Equi_Join]] — caso especial con igualdad
- [[Producto_Cartesiano]] — base del Theta Join
- [[Selección]] — la condición θ actúa como selección
