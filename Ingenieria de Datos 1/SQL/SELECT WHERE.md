sql

```sql
USE InventarioDB;
SELECT * FROM Clientes
WHERE Correo LIKE '%example.com';
```

**Nota**:

- **WHERE**: Filtra registros según una condición
- **LIKE**: Operador de búsqueda con patrones
- `%`: Comodín que representa cualquier secuencia de caracteres
- En este caso: selecciona clientes cuyo correo termina con "example.com"

