Entidades:
Producto : Id, nombre, precio, Id categoria(FK)

Categoria: ID, 

Sucursal: ID, nombre, direccion

Cliente: ID, nombre, direccion, Tel, mail(unico)

empleado: ID, nombre, cargo, sucursal(FK)

Inventario: (ID sucursal(FK), ID producto(FK)) -> PK, cantidad Stock

Venta: ID, fecha, total, ID cliente(FK), ID sucursal

Detalle venta: ID Venta(FK), ID Producto(FK), cantidad comprado, Precio unitario


Relacion

Producto 1-----1 detalle
Producto 1 --------- n inventario
Producto n ------ 1 categoria