
# SQL básico

## Crear base de datos

CREATE DATABASE InventarioDB;

## Insertar datos

INSERT INTO Productos (ProductoID, Nombre, Precio, Cantidad)
VALUES (1, 'Laptop', 1000.00, 10);

## Actualizar

UPDATE Productos
SET Precio = 25
WHERE ProductoID = 2;

## Eliminar

DELETE FROM Productos
WHERE ProductoID = 3;

## Consultar

SELECT * FROM Productos;