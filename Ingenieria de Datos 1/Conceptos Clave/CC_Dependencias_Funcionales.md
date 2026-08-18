---
tags: [modelado, normalizacion, dependencias-funcionales, diseño-bd]
aliases: [DF, Dependencia Funcional, dependencias funcionales, determinante, dependiente]
---

# Dependencias Funcionales (DF)

## ¿Qué es?

Una **dependencia funcional** existe entre dos atributos X e Y cuando:

> Cada valor **único** de X determina un valor **único** de Y.

**Notación:** `X → Y`

- **X** = determinante (lado izquierdo)
- **Y** = dependiente (lado derecho)

---

## Regla fundamental

Para que `X → Y` sea válida:

- Un mismo X **nunca puede** tener dos valores distintos de Y
- La relación es **unidireccional**: `X → Y` no implica `Y → X`

---

## Ejemplo válido ✅

| ID_Empleado | Nombre | Departamento | Teléfono |
|------------|--------|-------------|---------|
| 101 | Ana García | Finanzas | 555-1234 |
| 102 | Juan Pérez | Marketing | 555-5678 |
| 103 | Luis Ramírez | Finanzas | 555-8765 |

`ID_Empleado → Nombre` ✅  
`ID_Empleado → Departamento` ✅  
`ID_Empleado → Teléfono` ✅  

El 101 siempre da "Ana García". Ningún ID tendrá dos nombres.

---

## Ejemplo inválido ❌

| ID_Empleado | Nombre | Departamento |
|------------|--------|-------------|
| 101 | Ana García | Finanzas |
| 101 | Ana García | Marketing |

`ID_Empleado → Departamento` ❌ — el 101 tiene DOS departamentos.  
Esto es una **dependencia multivalor** (`X ↠ Y`), no funcional.

---

## Ejemplo del curso — tabla PEDIDOS

Tabla con todos los atributos mezclados:
```
PEDIDOS: ID_Cliente, Nombre, Apellido, Dirección, Telefono,
         NoPedido, FechaPedido, ID_Producto, NombreProducto, Precio, Estado
```

Las DF que **se esperan**:

```
ID_Cliente   → Nombre, Apellido, Dirección, Telefono
NoPedido     → ID_Cliente, ID_Producto, FechaPedido, Estado
ID_Producto  → NombreProducto, Precio
```

Cada uno de estos determinantes "controla" su grupo de dependientes.

---

## Tipos de llaves

| Tipo | Descripción |
|------|-------------|
| **Candidata** | Conjunto **mínimo** de atributos que identifica una tupla de forma única |
| **Primaria (PK)** | La llave candidata elegida como identificador oficial de la tabla |
| **Foránea (FK)** | Atributo que referencia la PK de otra tabla — garantiza integridad referencial |
| **Superclave** | Cualquier conjunto que **incluya** a una candidata (puede tener atributos extra) |

### Relación clave con DF

> Si X es llave de R → X determina **cualquier** subconjunto de atributos de R.

---

## Propiedades importantes

- `X → Y` no implica `Y → X`
- Una DF es una **restricción semántica** — no depende de los datos actuales sino del dominio del problema
- Los datos que cumplen todas las DF del esquema se llaman **extensiones legales** (o estados legales)

---

## Relacionado

[[CC_Tipos_DF]] · [[CC_Axiomas_Armstrong]] · [[CC_Cerradura_Atributos]] · [[CC_Anomalias_BD]] · [[Clase 11 - Normalizacion y Modelo ER]]
