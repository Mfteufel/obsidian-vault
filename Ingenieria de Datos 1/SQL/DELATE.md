sql

```sql
USE InventarioDB;
DELETE FROM Productos
WHERE ProductoID = 3;
```

**Nota**:

- Elimina **registros** de la tabla
- **WHERE**: Especifica qué registros eliminar (¡CRÍTICO! sin WHERE, elimina TODOS)
- En este caso: elimina el producto con ID 3