## Definición

Un **árbol binario de búsqueda (Binary Search Tree)** es una estructura de datos que organiza los registros de forma jerárquica para facilitar las búsquedas.

## Tipos de arboles:

- [[Pre Order]]
- [[Post Order]]
- [[In Order]]
  
|Recorrido|Orden|
|---|---|
|Preorder|Raíz → Izquierda → Derecha|
|Inorder|Izquierda → Raíz → Derecha|
|Postorder|Izquierda → Derecha → Raíz|

## Propiedad principal

Subárbol izquierdo → valores menores  
Subárbol derecho → valores mayores

## Ejemplo

        10  
       /  \  
      5    15  
     / \   / \  
    3   7 14 17

Si buscamos **7**:

1. 7 < 10 → ir a la izquierda
    
2. 7 > 5 → ir a la derecha
    
3. encontrado
    

## Ventajas

- Búsquedas más rápidas que el acceso secuencial.
    
- Permite organizar grandes volúmenes de datos.
    

## Desventajas

- Si el árbol se desbalancea puede volverse ineficiente.


