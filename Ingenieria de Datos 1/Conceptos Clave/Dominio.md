## ¿Qué es un Dominio?

Un **dominio** es el conjunto de valores atómicos posibles que puede tomar un atributo. Define el **tipo de dato**, el **rango** y las **restricciones** válidas para ese atributo.

> En SQL Server: equivale al tipo de dato de una columna (`INT`, `VARCHAR`, `DATE`, etc.).

---

## Componentes de un Dominio

|Componente|Descripción|Ejemplo|
|---|---|---|
|**Nombre**|Identificador del dominio|`dom_legajo`|
|**Tipo de dato**|Estructura del valor|`INT`, `VARCHAR(50)`|
|**Restricción**|Condición adicional sobre los valores|`> 0`, `NOT NULL`|
|**Valor por defecto**|Valor asignado si no se especifica|`GETDATE()`|

---

## Tipos de Dominio

### Por tipo de dato

- **Numéricos:** `INT`, `DECIMAL`, `FLOAT`
- **Cadenas:** `CHAR`, `VARCHAR`, `NVARCHAR`
- **Temporales:** `DATE`, `DATETIME`, `TIME`
- **Booleanos:** `BIT` (en SQL Server)
- **Otros:** `UNIQUEIDENTIFIER`, `BINARY`

### Por definición

|Tipo|Descripción|
|---|---|
|**Predefinido**|Tipos estándar del motor (INT, VARCHAR…)|
|**De usuario (UDT)**|Tipo personalizado basado en uno predefinido|

---

## Dominio en SQL Server

sql

```sql
-- Definir un dominio de usuario (UDT)
CREATE TYPE dom_legajo FROM INT NOT NULL;
CREATE TYPE dom_email  FROM VARCHAR(100) NULL;

-- Usarlo en una tabla
CREATE TABLE ALUMNO (
    legajo   dom_legajo PRIMARY KEY,
    email    dom_email,
    nombre   VARCHAR(50) NOT NULL
);
```

---

## Compatibilidad de Dominios

Para que una operación entre atributos sea válida, sus dominios deben ser **compatibles**:

- En una **JOIN**: las columnas comparadas deben pertenecer al mismo dominio (o compatible).
- En una **UNION**: los atributos deben tener el mismo dominio en cada posición.
- Una FK debe tener el **mismo dominio** que la PK a la que referencia.

> ⚠️ Dos atributos pueden tener el mismo tipo de dato pero distintos dominios. Ej: `legajo` y `nro_telefono` son ambos `INT`, pero no son comparables.

---

## Restricciones de Dominio

Las restricciones limitan los valores aceptables:

sql

```sql
-- CHECK como restricción de dominio
CREATE TABLE PRODUCTO (
    precio DECIMAL(10,2) CHECK (precio > 0),
    stock  INT           CHECK (stock >= 0)
);
```

---


## Preguntas de repaso

1. ¿Cuál es la diferencia entre tipo de dato y dominio?
2. ¿Por qué dos columnas con el mismo tipo pueden pertenecer a dominios distintos?
3. ¿Qué condición deben cumplir los dominios en una operación UNION?
4. ¿Para qué sirve definir un UDT en SQL Server?