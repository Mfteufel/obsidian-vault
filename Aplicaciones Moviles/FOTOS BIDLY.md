En model/Producto.java hay una relación con Foto que usa LAZY loading
y rompe al serializar. El error es:
"failed to lazily initialize a collection of role: Producto.fotos"

Arreglá esto de una de estas dos formas (elegí la más simple):

Opción A: Agregá @JsonIgnore en el campo fotos de Producto.java
para que Jackson no intente serializar la lista de fotos.

Opción B: Cambiá fetch = FetchType.LAZY por fetch = FetchType.EAGER
en la relación @OneToMany de fotos en Producto.java.

Usá la Opción A si el frontend no necesita las fotos en este endpoint.
Usá la Opción B si el frontend sí necesita las fotos.

Después de arreglar, compilá con ./mvnw clean compile para confirmar
que no hay errores.



---

## 21 de Junio


Notificaciones debería hacer algo


Poner cronometro para saber cuando termina una subasta, Ver cuando estas ganando la subasta. 


Que te aparezca en notificaciones cuando perdes el liderato del la puja.


Cuando se vende una subasta tuya


Marcar favoritas la subastas
(Sacar esta funcionalidad)


Sacar que se pueda modificar la comisión 


La barra de búsquedas tampoco anda
(front)


publico un producto y no aparece en mis subasta
(front)


En pestaña mis producto apare el producto pero no se puede hacer nada.(front)


No puedo cargar nuevos medios de pago(front)


No puedo elegir con que moneda quiero que sea la subasta (front)


