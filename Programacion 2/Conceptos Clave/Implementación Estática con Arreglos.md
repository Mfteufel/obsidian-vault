---
tags: [tda, arreglos, implementacion, estatico, programacion-2]
aliases: [Implementación Estática, Implementación con Arreglos, Estático vs Dinámico]
---

# CC_Implementación Estática con Arreglos

> Guardar un [[TDA]] dentro de un **arreglo**: tamaño fijo, memoria contigua. Seguro pero rígido.

## Estático vs Dinámico

| | Estático (hoy) | Dinámico (más adelante) |
|---|---|---|
| **Estructura** | Arreglos (Arrays) | Listas enlazadas |
| **Tamaño** | Fijo, definido al crear | Crece y decrece |
| **Memoria** | Contigua | Dispersa, unida por punteros |
| **Ventaja** | Simple, acceso directo por índice | Flexible |
| **Desventaja** | Rígido, puede desperdiciar o quedarse corto | Más complejo, usa punteros |

---

## Las 3 estrategias

### Estrategia 1 — Alineación izquierda + puntero separado

```
 [4]   [1][3][5][7][ ][ ][ ][ ][ ]
puntero  1  2  3  4  5  6  7  8  9
```

- **Alineación:** izquierda
- **Puntero:** una variable `int` separada apunta a la **primera posición libre**
- **Para agregar:** ponés el dato y sumás 1 a la variable

```java
int[] datos = new int[10];
int cantidad = 0;

void agregar(int dato) {
    datos[cantidad] = dato;
    cantidad++;
}
```

✅ Barato — costo constante.

---

### Estrategia 2 — Alineación derecha (tope en índice 0)

```
 [4]   [7][5][3][1][ ][ ][ ][ ][ ]
puntero  1  2  3  4  5  6  7  8  9
```

- **Alineación:** derecha — el tope está **siempre en el índice 0**
- **Costo:** ¡pesadísimo! Al agregar hay que **desplazar TODO el arreglo** hacia la derecha → **O(n)**
- La variable apunta a la última posición ocupada

⚠️ Evitar salvo que haya una razón muy concreta.

---

### Estrategia 3 — Sin variable separada

```
 [4][1][3][5][7][ ][ ][ ][ ][ ]
  0  1  2  3  4  5  6  7  8  9
  ↑
 cantidad guardada acá
```

- **Alineación:** izquierda
- **Puntero:** no hay variable separada. Se **sacrifica el índice 0** del arreglo para guardar la cantidad de elementos

```java
int[] datos = new int[10];
datos[0] = 0;   // cantidad de elementos

void agregar(int dato) {
    datos[0]++;
    datos[datos[0]] = dato;
}
```

✅ Todo vive en una sola estructura.
⚠️ Perdés una posición y el código es menos legible.

---

## Comparación rápida

| Estrategia | Costo de agregar | Variable extra | Complejidad |
|---|---|---|---|
| 1 | O(1) | Sí | Baja |
| 2 | O(n) | Sí | Media |
| 3 | O(1) | No | Media |

## Relacionado

- [[Clase 3 - Tipos de Datos Abstractos]]
- [[Arreglos]]
- [[Pila]]
- [[Cola]]
- [[TDA]]
- [[Interfaz]]
