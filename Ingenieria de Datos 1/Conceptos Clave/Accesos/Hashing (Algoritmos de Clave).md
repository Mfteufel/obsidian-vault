## Definición

El **hashing** es una técnica que utiliza una **función matemática** para transformar una clave en una posición específica dentro de una tabla.

## Funcionamiento

La función hash calcula la posición donde debe almacenarse o buscarse el registro.

Ejemplo:

posición = clave % tamaño_tabla

Si la clave es **123** y la tabla tiene tamaño **10**:

123 % 10 = 3

El registro se guarda en la posición **3**.

## Colisiones

Una **colisión** ocurre cuando dos claves generan la misma posición.

Ejemplo:

123 % 10 = 3  
133 % 10 = 3

Para resolverlo se utilizan métodos como:

- área de desborde
    
- encadenamiento
    
- direccionamiento abierto    

## Ventajas

- Acceso extremadamente rápido.
    
- Ideal para búsquedas exactas.

## Desventajas

- Puede haber colisiones.
    
- No es eficiente para búsquedas por rango.