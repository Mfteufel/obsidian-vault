## ¿Qué es el Álgebra Relacional?

El **álgebra relacional** es un lenguaje formal y **procedural** para manipular relaciones (tablas). Es procedural porque el usuario especifica **qué datos quiere Y cómo obtenerlos** (el orden de las operaciones importa).

> Base teórica de SQL. Toda consulta SQL se puede expresar en álgebra relacional.

---

## Operaciones Fundamentales

### 1. Selección — σ (sigma)

Filtra **filas** que cumplen una condición. Equivale al `WHERE` de SQL.

```
σ condición (R)

Ejemplo:
σ salario > 50000 (EMPLEADO)
→ Devuelve todas las filas de EMPLEADO donde salario > 50000
```

---

### 2. Proyección — π (pi)

Selecciona **columnas** específicas. Equivale al `SELECT` de SQL. Elimina duplicados.

```
π atributos (R)

Ejemplo:
π nombre, apellido (EMPLEADO)
→ Devuelve solo las columnas nombre y apellido
```

---

### 3. Unión — ∪

Combina los resultados de dos relaciones **compatibles**. Elimina duplicados.

```
R ∪ S

Condición: R y S deben tener el mismo número de atributos y dominios compatibles.
```

---

### 4. Diferencia — −

Devuelve las tuplas que están en R pero **no** en S.

```
R − S
```

---

### 5. Producto Cartesiano — ×

Combina **cada** tupla de R con **cada** tupla de S.

```
R × S

Si R tiene n tuplas y S tiene m tuplas → resultado tiene n×m tuplas.
⚠️ Rara vez se usa solo; generalmente se combina con σ para hacer un JOIN.
```

---

## Operaciones Derivadas

### 6. Intersección — ∩

Devuelve tuplas que están en **ambas** relaciones.

```
R ∩ S   ≡   R − (R − S)
```

---

### 7. JOIN — ⋈

Combina dos relaciones según una condición. El más común es el **natural join**.

```
R ⋈ condición S      → Theta Join
R ⋈ S                → Natural Join (une por atributos con el mismo nombre)
```

**En SQL:**

sql

```sql
SELECT * FROM EMPLEADO e JOIN DEPARTAMENTO d ON e.id_depto = d.id_depto
```

---

### 8. División — ÷

Devuelve las tuplas de R que están relacionadas con **todas** las tuplas de S.

```
R ÷ S

Ejemplo clásico: "alumnos inscriptos en TODAS las materias del listado S"
```

---

## Combinando Operaciones

```
Ejemplo: Nombres de empleados del departamento 'IT' con salario > 60000

π nombre (σ depto='IT' ∧ salario>60000 (EMPLEADO))
```

**SQL equivalente:**

sql

```sql
SELECT nombre
FROM EMPLEADO
WHERE depto = 'IT' AND salario > 60000;
```

---

## Resumen de Operadores

|Operador|Símbolo|Tipo|SQL equivalente|
|---|---|---|---|
|Selección|σ|Fundamental|WHERE|
|Proyección|π|Fundamental|SELECT|
|Unión|∪|Fundamental|UNION|
|Diferencia|−|Fundamental|EXCEPT|
|Producto Cart.|×|Fundamental|CROSS JOIN|
|Intersección|∩|Derivada|INTERSECT|
|JOIN|⋈|Derivada|JOIN|
|División|÷|Derivada|—|


---

## Preguntas de repaso

1. ¿Cuál es la diferencia entre selección y proyección?
2. ¿Qué condición deben cumplir dos relaciones para poder hacer una UNIÓN?
3. ¿En qué se diferencia el álgebra relacional del cálculo relacional?
4. Expresá en álgebra relacional: "apellidos de clientes de Buenos Aires".
5. ¿Qué devuelve R ÷ S? Dá un ejemplo.