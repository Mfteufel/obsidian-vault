## ¿Qué es la Cardinalidad?

La **cardinalidad** define la cantidad de instancias de una entidad que pueden estar asociadas con instancias de otra entidad a través de una relación. Es fundamental para diseñar el DER y traducirlo al modelo relacional.

---

## Tipos de Cardinalidad

### 1:1 — Uno a Uno

Cada instancia de A se relaciona con **a lo sumo una** instancia de B, y viceversa.

```
PERSONA ──────── PASAPORTE
   1                 1
```

> Ejemplo: una persona tiene un único pasaporte, un pasaporte pertenece a una única persona.

---

### 1:N — Uno a Muchos

Una instancia de A puede relacionarse con **varias** instancias de B, pero cada instancia de B se relaciona con **solo una** de A.

```
CLIENTE ──────── PEDIDOS
   1               N
```

> Ejemplo: un cliente puede tener muchos pedidos, pero cada pedido pertenece a un solo cliente.

---

### N:M — Muchos a Muchos

Una instancia de A puede relacionarse con **varias** instancias de B, y viceversa.

```
ALUMNO ──────── MATERIA
   N               M
```

> Ejemplo: un alumno cursa varias materias, y una materia tiene muchos alumnos.

⚠️ En el modelo relacional, una relación N:M **se resuelve con una tabla intermedia**.

---

## Notaciones

### Notación básica (Chen)

Se escribe `1`, `N` o `M` en los extremos de la línea de relación.

### Notación de pata de cuervo (Crow's Foot)

Usa símbolos gráficos para indicar mínimo y máximo:

|Símbolo|Significado|
|---|---|
|`‖`|Exactamente uno (obligatorio)|
|`O`|Cero (opcional)|
|`<`|Muchos|

---

## Cardinalidad Mínima y Máxima

- **Mínima:** si la participación es **obligatoria** (mín = 1) u **opcional** (mín = 0).
- **Máxima:** si puede haber `1` o `N` instancias relacionadas.

```
Notación (mín, máx):
ALUMNO (1,N) ──── INSCRIPCION ──── (1,1) MATERIA
```

---

## Impacto en el Modelo Relacional

|Cardinalidad|Cómo se implementa|
|---|---|
|1:1|FK en cualquiera de las dos tablas|
|1:N|FK en la tabla del lado N|
|N:M|Tabla intermedia con FK de ambas partes|

---

## Ejemplo completo — N:M resuelto

sql

```sql
-- Tablas base
ALUMNO (legajo PK, nombre)
MATERIA (cod_materia PK, nombre)

-- Tabla intermedia
INSCRIPCION (legajo FK, cod_materia FK, fecha_inscripcion)
             └── PK compuesta: (legajo, cod_materia)
```

## Preguntas de repaso

1. ¿Cómo se implementa una relación N:M en el modelo relacional?
2. ¿Cuál es la diferencia entre cardinalidad mínima y máxima?
3. Dá un ejemplo de relación 1:1 y justificá si conviene unirlas en una sola tabla.
4. ¿Dónde se coloca la FK en una relación 1:N?