## Instrucciones

Como ejercitación para exámenes, se propone continuar con la base de datos **Supply**, con los esquemas **Inventario y****Producción**, para realizar operaciones de planificación de la producción, actualización de inventario, y análisis de los costos y recursos utilizados. Se trabajará con **procedimientos almacenados**, **funciones del sistema**, **funciones definidas por el usuario**, **consultas con JOINs**, **manejo de errores** y **cálculos relacionados con la producción**. 

A continuación se pide: 

**Ejercicio 7: Proyectar los productos activos** 

Escribe una consulta que proyecte (muestre) el NombreProducto y el Precio de los productos con un CantidadStock mayor a cero. 

**Ejercicio 8: Listar materiales con baja existencia** 

Escribe una consulta que liste el ID_Material, NombreMaterial y CantidadStock de los materiales cuyo CantidadStock sea menor a 50. 

  

**Ejercicio 9: Órdenes de Producción en el último mes** 

Realiza una consulta que proyecte el ID_OrdenProduccion, FechaOrden, ID_Producto, y CantidadProducida de las órdenes creadas en el último mes. 

  

**Ejercicio 10: Crear vista** **vw_ProductosConStock** 

Crea una vista que proyecte el ID_Producto, NombreProducto, Precio y CantidadStock de los productos con existencia en el inventario (CantidadStock > 0). 

  

**Ejercicio 11: Función para verificar stock suficiente** 

Define una función que reciba el ID_Producto y la Cantidad solicitada y devuelva un valor booleano (1 para true y 0 para false) indicando si hay suficiente stock de materiales para cubrir la producción solicitada. 

  

**Ejercicio 12: Listado de órdenes de producción con detalles de materiales** 

Escribe una consulta que muestre el ID_OrdenProduccion, FechaOrden, NombreProducto, ID_Material, NombreMaterial, CantidadNecesaria y CostoMaterial. Usa un JOIN entre las tablas **Producción.C11_OrdenesProduccion**, **Producción.C11_Productos**, **Producción.C11_ProduccionMateriales** y **Producción.C11_Materiales**. 

  

**Ejercicio 13: Productos sin suficiente stock de materiales** 

Crea una consulta que liste ID_Producto, NombreProducto y CantidadStock de productos que no pueden producirse debido a falta de stock en uno o más materiales necesarios. Usa una consulta anidada para verificar que el CantidadStock en **Producción.C11_Materiales** es menor que la CantidadNecesaria. 

  

**Ejercicio 14: Productos con el costo total de producción más alto** 

Escribe una consulta que muestre el ID_Producto, NombreProducto, y el costo total de producción de cada producto (sumando CantidadNecesaria * CostoMaterial). Ordena el resultado en orden descendente y muestra solo los 5 productos con el costo más alto. 

  

**Ejercicio 15: Órdenes de producción recientes con cantidad mayor al promedio** 

Realiza una consulta que muestre ID_OrdenProduccion, FechaOrden, ID_Producto y CantidadProducida de aquellas órdenes de producción cuya CantidadProducida sea mayor que el promedio de cantidad producida en todas las órdenes. Usa una consulta anidada para calcular el promedio