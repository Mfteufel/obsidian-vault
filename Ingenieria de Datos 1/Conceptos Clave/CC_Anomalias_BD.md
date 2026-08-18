---
tags: [modelado, normalizacion, anomalias, diseño-bd]
aliases: [Anomalias, Anomalías de BD, anomalia de insercion, anomalia de modificacion, anomalia de eliminacion]
---

# Anomalías en Bases de Datos

## ¿Qué son?

Problemas que surgen cuando los datos están **mal estructurados** en una tabla.

Son la razón principal por la que se normaliza una base de datos.

---

## Los 3 tipos

### 🔴 Anomalía de Inserción

> No se puede agregar un dato **sin que falte otro**.

**Ejemplo banco:**

| Sucursal | Gerente | Cliente | CodPrestamo | Importe |
|----------|---------|---------|-------------|---------|
| Centro | Ramos | Santos | P-17 | 200.000 |

- ¿Puedo registrar una sucursal nueva sin que tenga clientes todavía? ❌
- La tabla mezcla datos de sucursales con datos de préstamos.

---

### 🟡 Anomalía de Modificación

> Hay que hacer el **mismo cambio en múltiples filas**.

**Ejemplo banco:**

Si el gerente de la sucursal "Centro" cambia, hay que actualizar **todas las filas** donde aparece "Centro".

| Sucursal | Gerente |
|----------|---------|
| Centro | Ramos |
| Centro | Ramos |
| Centro | Ramos |

→ Si Ramos se va, hay que actualizar 3 filas (o más).

---

### 🔵 Anomalía de Eliminación

> Borrar un dato implica **perder otro** que no queríamos eliminar.

**Ejemplo banco:**

Si el cliente "Vázquez" cancela su préstamo y borramos la fila, perdemos los datos de la sucursal "Ituzaingó" si era el único cliente.

---

## ¿Cómo se resuelven?

Con **normalización** → separar los datos en tablas correctas según sus dependencias funcionales.

```
Tabla original (mal):
Sucursal, Gerente, ActivoM, Cliente, CodPrestamo, Importe

Tablas normalizadas (bien):
Sucursales(Sucursal, Gerente, ActivoM)
Clientes(Cliente, ...)
Prestamos(CodPrestamo, Cliente, Sucursal, Importe)
```

---

## Relacionado

[[CC_Dependencias_Funcionales]] · [[CC_Tipos_DF]] · [[Clase 11 - Normalizacion y Modelo ER]]
