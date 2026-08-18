## Definición

La **redundancia de datos** ocurre cuando la **misma información se almacena varias veces dentro de una base de datos**.

Esto suele suceder cuando los datos no están bien organizados o el diseño de la base de datos no es adecuado.

## Ejemplo

|ClienteID|Nombre|Ciudad|
|---|---|---|
|1|Juan|Buenos Aires|
|2|Ana|Buenos Aires|
|3|Pedro|Buenos Aires|

La ciudad **“Buenos Aires”** se repite varias veces.

## Problemas que genera

- Mayor uso de espacio de almacenamiento
    
- Posibles **inconsistencias de datos**
    
- Dificultad para actualizar información
    

## Solución

La redundancia se reduce mediante **normalización**, organizando los datos en **tablas relacionadas**.

## Idea clave

Menos redundancia → mayor integridad y eficiencia de la base de datos