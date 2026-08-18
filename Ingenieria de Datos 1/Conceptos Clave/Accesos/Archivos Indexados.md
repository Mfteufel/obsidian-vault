## Definición

Los **archivos indexados** utilizan una estructura adicional llamada **índice** que permite localizar registros de manera más rápida.

El índice contiene una **clave y la dirección del registro**.

## Funcionamiento

Primero se consulta el índice y luego se accede directamente al registro.

Ejemplo de índice:

|Nombre|Posición|
|---|---|
|Adriana|2|
|Estela|3|
|Octavio|1|

Si buscamos **Estela**:

1. Se consulta el índice.
    
2. Se obtiene la posición del registro.
    
3. Se accede directamente al dato.

## Ventajas

- Búsquedas más rápidas.
    
- Reduce el tiempo de acceso a los registros.
    

## Desventajas

- Requiere espacio adicional para almacenar el índice.
    
- Debe actualizarse cuando cambian los datos.