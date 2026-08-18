

## Definición

El **acceso secuencial** es un método de búsqueda de datos donde los registros se leen **uno después del otro en orden** hasta encontrar el registro deseado.

## Funcionamiento

El sistema comienza desde el primer registro del archivo y compara cada elemento hasta encontrar el dato buscado.

Ejemplo:

Registro 1 → Ana  
Registro 2 → Carlos  
Registro 3 → Lucía  
Registro 4 → Pedro

Si se busca **Pedro**, el sistema revisa todos los registros anteriores.

## Características

- Método simple de implementar.
    
- Utilizado en sistemas antiguos de archivos.
    
- El tiempo de búsqueda depende de la cantidad de registros.
  

## Ventajas

- Fácil de implementar.
    
- No requiere estructuras adicionales.


## Desventajas

- Muy lento cuando el volumen de datos es grande.
    
- La búsqueda puede requerir recorrer todo el archivo.