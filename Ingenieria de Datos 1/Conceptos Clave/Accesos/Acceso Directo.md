
## Definición

El **acceso directo** permite acceder a un registro **directamente mediante su dirección de almacenamiento**, sin necesidad de recorrer los registros anteriores.

## Funcionamiento

Cada registro tiene una **posición específica en memoria o disco** que permite localizarlo inmediatamente.

Ejemplo:

Cliente 1 → posición 100  
Cliente 2 → posición 200  
Cliente 3 → posición 300

Si se busca el cliente 3, el sistema accede directamente a la **posición 300**.

## Características

- No requiere recorrer registros intermedios.
    
- Permite búsquedas rápidas.
    

## Ventajas

- Muy rápido.
    
- Ideal para consultas frecuentes.
    

## Desventajas

- Requiere conocer la ubicación exacta del registro.