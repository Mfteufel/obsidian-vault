## ¿Qué es un Atributo?

Un **atributo** es una propiedad o característica que describe a una entidad dentro de un modelo de datos. En el modelo relacional, los atributos corresponden a las **columnas** de una tabla.

---

## Tipos de Atributos

### Por su composición

| Tipo                 | Descripción                                        | Ejemplo                             |
| -------------------- | -------------------------------------------------- | ----------------------------------- |
| **Simple (atómico)** | No se puede dividir en partes más pequeñas         | `dni`, `precio`                     |
| **Compuesto**        | Se puede descomponer en sub-atributos              | `dirección` → calle, número, ciudad |
| **Multivaluado**     | Puede tener más de un valor para una misma entidad | `teléfonos` de un cliente           |

### Por su derivación

|Tipo|Descripción|Ejemplo|
|---|---|---|
|**Almacenado**|Se guarda directamente en la BD|`fecha_nacimiento`|
|**Derivado**|Se calcula a partir de otro atributo|`edad` (calculada desde `fecha_nacimiento`)|

### Por su rol en la relación

|Tipo|Descripción|
|---|---|
|**Clave (PK)**|Identifica unívocamente a cada tupla|
|**No clave**|Describe a la entidad pero no la identifica|
|**Clave foránea (FK)**|Referencia a la PK de otra tabla|

---

## Atributos Clave

- **Superclave:** conjunto de atributos que identifica unívocamente a una tupla.
- **Clave candidata:** superclave mínima (sin atributos redundantes).
- **Clave primaria (PK):** la clave candidata elegida como identificador principal.
- **Clave alternativa:** clave candidata que no fue elegida como PK.
- **Clave Externa:** es un atributo (o conjunto de atributos) en una tabla que **referencia la clave primaria de otra tabla**. Su función es establecer y mantener el vínculo entre dos tablas.

---

## Valores de Atributos

- Cada atributo tiene un **dominio** asociado (conjunto de valores posibles).
- Un atributo puede tener valor **NULL** si el dato es desconocido o no aplica.
- En el modelo relacional, los atributos deben ser **atómicos** (1FN).

---

## Ejemplo

```
Tabla: ALUMNO
┌────────┬──────────┬───────────┬───────────────┐
│ legajo │ nombre   │ apellido  │ fecha_nac     │
├────────┼──────────┼───────────┼───────────────┤
│ 12345  │ Marco    │ García    │ 2001-05-10    │
└────────┴──────────┴───────────┴───────────────┘

- legajo     → atributo clave (PK)
- nombre     → atributo simple, almacenado
- fecha_nac  → atributo almacenado (de él se deriva "edad")
```


---

## Preguntas de repaso

1. ¿Cuál es la diferencia entre atributo simple y compuesto?
2. ¿Puede una PK contener valores NULL? ¿Por qué?
3. ¿Qué es una clave candidata? ¿En qué se diferencia de la PK?
4. Dá un ejemplo de atributo derivado en una tabla de empleados.