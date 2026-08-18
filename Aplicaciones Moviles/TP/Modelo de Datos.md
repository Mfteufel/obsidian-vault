El sistema se compone de las siguientes entidades principales.  
  
## Usuario  
  
Atributos:  
  
- idUsuario  
- nombre  
- apellido  
- documento  
- domicilio  
- pais  
- categoria  
- email  
- password  
  
Un usuario puede tener múltiples medios de pago.  
  
---  
  
## MedioPago  
  
Atributos:  
  
- idMedioPago  
- tipo  
- banco  
- numero  
- verificado  
  
Tipos posibles:  
  
- cuenta bancaria  
- tarjeta de crédito  
- cheque certificado  
  
---  
  
## Subasta  
  
Atributos:  
  
- idSubasta  
- fecha  
- hora  
- categoriaMinima  
- moneda  
- rematador  
- estado  
  
Una subasta contiene múltiples objetos.  
  
---  
  
## Item  
  
Atributos:  
  
- idItem  
- numeroPieza  
- descripcion  
- precioBase  
- dueñoActual  
- estado  
  
Un item puede tener varias imágenes.  
  
---  
  
## Puja  
  
Atributos:  
  
- idPuja  
- monto  
- fechaHora  
- usuario  
- item  
  
Cada puja pertenece a un usuario y a un item.  
  
---  
  
## Venta  
  
Atributos:  
  
- idVenta  
- item  
- comprador  
- montoFinal  
- medioPago  
- fecha