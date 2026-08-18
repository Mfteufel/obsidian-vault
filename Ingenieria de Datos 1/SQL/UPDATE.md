### 4. UPDATE - Actualizar Datos

sql

```sql
USE InventarioDB;
UPDATE Productos
SET Precio = 25.00
WHERE ProductoID = 2;
```

**Nota**:

- **SET**: Especifica qué columna se modifica y su nuevo valor
- **WHERE**: Especifica qué registros se actualizan (¡IMPORTANTE! sin WHERE, actualiza TODOS)
- En este caso: cambia el precio del producto con ID 2 a 25.00