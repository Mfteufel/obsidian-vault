## ¿Qué es el Cálculo Relacional?

El **cálculo relacional** es un lenguaje formal y **declarativo** para manipular relaciones. A diferencia del álgebra relacional, el usuario especifica **qué datos quiere** sin indicar cómo obtenerlos (el motor decide el proceso).

> Tiene el mismo poder expresivo que el álgebra relacional (son equivalentes en expresividad).

---

## Variantes

|Variante|Variable de|Equivale a|
|---|---|---|
|**Cálculo Relacional de Tuplas (CRT)**|Tuplas (filas)|Base de SQL|
|**Cálculo Relacional de Dominio (CRD)**|Valores de atributos|Base de QBE|

---

## Cálculo Relacional de Tuplas (CRT)

Usa **variables de tupla** que recorren las filas de una relación.

### Sintaxis general

```
{ t | condición(t) }
```

> "El conjunto de tuplas `t` tal que se cumple la condición sobre `t`"

### Ejemplo

```
{ t | EMPLEADO(t) ∧ t.salario > 50000 }
```

→ "Todas las tuplas de EMPLEADO donde el salario es mayor a 50000"

**SQL equivalente:**

sql

```sql
SELECT * FROM EMPLEADO WHERE salario > 50000;
```

---

### Proyección en CRT

Para seleccionar solo algunos atributos:

```
{ t.nombre, t.apellido | EMPLEADO(t) ∧ t.depto = 'IT' }
```

**SQL equivalente:**

sql

```sql
SELECT nombre, apellido FROM EMPLEADO WHERE depto = 'IT';
```

---

### Cuantificadores

|Cuantificador|Símbolo|Significado|
|---|---|---|
|**Existencial**|∃|"Existe al menos uno"|
|**Universal**|∀|"Para todos"|

#### Ejemplo con ∃ (existencial)

```
{ t.nombre | EMPLEADO(t) ∧ ∃s (DEPARTAMENTO(s) ∧ s.nombre = 'IT' ∧ t.id_depto = s.id) }
```

→ "Nombres de empleados que trabajan en el departamento IT"

#### Ejemplo con ∀ (universal)

```
{ t | ALUMNO(t) ∧ ∀m (MATERIA(m) → INSCRIPTO(t.legajo, m.cod)) }
```

→ "Alumnos inscriptos en TODAS las materias" (equivale a la división ÷)

---

## Cálculo Relacional de Dominio (CRD)

Usa **variables de dominio** que toman valores individuales de atributos (no filas completas).

### Sintaxis general

```
{ <x1, x2, ...> | condición(x1, x2, ...) }
```

### Ejemplo

```
{ <n, a> | ∃s ∃d (EMPLEADO(n, a, s, d) ∧ s > 50000) }
```

→ "Pares (nombre, apellido) de empleados con salario > 50000"

---

## Comparativa: Álgebra vs Cálculo

|Aspecto|Álgebra Relacional|Cálculo Relacional|
|---|---|---|
|**Tipo**|Procedural|Declarativo|
|**Especifica**|Qué + Cómo|Solo Qué|
|**Operadores**|σ, π, ∪, ×, ⋈…|∃, ∀, ∧, ∨, ¬|
|**Poder expresivo**|Equivalente|Equivalente|
|**Base de**|—|SQL, QBE|

---

## Seguridad de expresiones

Una expresión de cálculo es **segura** si garantiza producir un resultado finito. Las expresiones con ∀ mal formuladas pueden generar resultados infinitos → hay que asegurarse de acotar las variables.

---

## Preguntas de repaso

1. ¿Cuál es la diferencia principal entre álgebra relacional y cálculo relacional?
2. ¿Qué diferencia hay entre CRT y CRD?
3. ¿Para qué se usa el cuantificador universal ∀? Dá un ejemplo.
4. ¿Qué significa que una expresión de cálculo sea "segura"?
5. Escribí en CRT: "nombres de alumnos con promedio mayor a 7".