
## Instrucciones

**Ejercicio 1: Cálculo de la Cerradura de Atributos** 

Dada la relación R={A,B,C,D,E} y el conjunto de dependencias funcionales: F={A→B,B→C,C→D} 

1. Calcula la **cerradura de A** (A+) con respecto a F. 
2. Determina si A es una **superllave** de R. 

**Ejercicio 2: Deducción de una Dependencia Funcional** 

Dada la relación R={A,B,C,D,E} y el conjunto de dependencias funcionales: F={A→B,B→C,C→D,D→E} 

1. Demuestra que la dependencia funcional A→E **se puede deducir** del conjunto F. 
2. Calcula la **cerradura de A** para verificar el resultado. 

**Ejercicio 3: Identificación de Claves Candidatas** 

Dada la relación R={A,B,C,D} y el conjunto de dependencias funcionales: F={A→B,B→C,C→D} 

1. Identifica la **clave candidata** de R utilizando el conjunto de dependencias funcionales en F. 
2. Explica por qué el conjunto {A} es suficiente (o no) para identificar de manera única todas las tuplas en R. 

**Ejercicio 4: Clasificación de Dependencias Funcionales (Completa o Parcial)** 

Dada la relación R={A,B,C,D} y el conjunto de dependencias funcionales: 

F={AB→C,B→D} 

1. Determina si la dependencia AB→C es **completa** o **parcial**. 
2. Explica tu respuesta y describe qué significa una dependencia funcional completa. 

**Ejercicio 5: Aplicación de las Reglas de Armstrong** 

Dada la relación R={A,B,C,D} y el conjunto de dependencias funcionales: F={A→B,B→C} 

1. Usa las **reglas de Armstrong** para demostrar que A→C se puede deducir del conjunto F. 
2. Explica el uso de cada regla aplicada. 

**Ejercicio 6: Verificación de una Dependencia con la Cerradura** 

Dada la relación R={A,B,C,D,E} y el conjunto de dependencias funcionales: F={A→B,B→C,CD→E} 

1. Calcula la **cerradura de A**. 
2. Determina si A→E es una **dependencia deducida** de F y explica tu razonamiento. 

**Ejercicio 7: Identificación de Superllaves y Llaves Candidatas** 

Dada la relación R={A,B,C,D} y el conjunto de dependencias funcionales: F={AB→C,C→D} 

1. Encuentra la **superllave** mínima de R. 
2. Determina si AB es una **llave candidata**. 

**Ejercicio 8: Cálculo de la Cerradura para un Conjunto de Atributos Más Complejo** 

Dada la relación R={A,B,C,D,E,F} y el conjunto de dependencias funcionales: F={A→B,B→C,C→D,A→E,E→F} 

1. Calcula la **cerradura de A** y verifica si A es una superllave. 
2. Determina si hay otros conjuntos más pequeños que podrían ser claves candidatas. 

**Ejercicio 9 – Cálculo de la Cerradura y Verificación de Superllave** 

**Dada la relación**  R={A,B,C,D,E,F,G}

**Conjunto de Dependencias Funcionales:** F={A→BC,"  " B→E,"  " C→D,"  " DE→F,"  " F→G,"  " G→E}

**Consignas:** 

1. Calcula la **cerradura de A** paso a paso, mostrando qué dependencias aplicas en cada etapa. 

- Indica el conjunto resultante en cada paso. 
- Concluye si **A** es o no una **superllave** de la relación. 

3. Analiza si existen **otros conjuntos de atributos más pequeños o alternativos** que puedan funcionar como **claves candidatas** de la relación . 

- Justifica tu respuesta indicando las dependencias que utilizas para probarlo. 

5. Explica brevemente **qué significan** los resultados obtenidos en términos de **dependencias** y **determinación de atributos** dentro de la relación. 

**Ejercicio 10 – Determinación de Claves Candidatas Mínimas** 

**Relación:**  R={A,B,C,D,E}

**Conjunto de Dependencias Funcionales:**  F={AB→C,"  " C→A,"  " B→D,"  " D→E}

**Consignas:** 

1. Calcula la **cerradura de B** y analiza si B puede considerarse una **superllave**. 

- Si no lo es, explica qué atributos faltan para determinar toda la relación. 

3. Determina **todas las claves candidatas mínimas** posibles para la relación : 

- Identifica los atributos que **deben** estar presentes en toda clave candidata (por ejemplo, los que no aparecen nunca en el lado derecho de ninguna dependencia). 
- Calcula las cerraduras necesarias para confirmar cuáles conjuntos de atributos son **claves candidatas**. 

5. Justifica brevemente por qué cada una de las claves encontradas es **mínima** (ningún subconjunto suyo determina todos los atributos de ).