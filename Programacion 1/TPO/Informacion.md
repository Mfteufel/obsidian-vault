

NEGOCIO: Empresa servicios técnicos (aires, electricidad, mantenimiento)
CLIENTES: particulares + comercios
PROBLEMAS:
- Pedidos llegan por WhatsApp y teléfono (desordenado)
- Anota en cuaderno y se pierde
- No sabe estado de cobros
- No sabe si el técnico fue
- No sabe si compró el repuesto


FLUJO DE PEDIDO:
WhatsApp/llamada → cuaderno → urgente o programado → asigna técnico → técnico va

DATOS DEL PEDIDO:
- Cliente (nombre, dirección, teléfono)
- Descripción del problema
- Urgencia (sí/no)
- Técnico asignado
- Estado (pendiente / en curso / terminado?)

PROBLEMAS NUEVOS:
- Letra ilegible en cuaderno
- Info al técnico por WhatsApp → se pierde
- Confusión de direcciones
- Necesita planificar la semana



### 📁 ¿Qué hay en el Modelo de Datos?

Son **5 "tablas"** hechas con listas de diccionarios:

|#|Entidad|Para qué sirve|
|---|---|---|
|1|`clientes`|Datos de cada cliente|
|2|`tecnicos`|Carlos, El Negro, Santi|
|3|`pedidos`|⭐ El corazón del sistema|
|4|`cobros`|Quién debe, quién pagó|
|5|`repuestos`|Piezas pedidas por trabajo|

cleintes

{
    "id_cliente": 1,
    "nombre": "Doña Rosa",
    "direccion": "Av. Siempreviva 742",
    "telefono": "11-1234-5678",
    "tipo": "particular"
}
Posibles contactos

tecnicos

{
    "id_tecnico": 1,
    "nombre": "Carlos",
    "especialidad": "electrica",
    "estado": "disponible"
}


Pedidos

{
    "id_pedido": 100,
    "id_cliente": 1,
    "id_tecnico": 1,
    "descripcion": "Cortocircuito en cocina, saltó la térmica",
    "urgente": True,
    "fecha": "05/06/2026",
    "estado": "asignado",
    "precio": None
}
informacion de repuesto

Cobros

cobros = [
    {"id_cobro": 1, "id_pedido": 100, "monto": 5000, "forma_pago": "cuotas", "estado": "pagado", ...},
    {"id_cobro": 2, "id_pedido": 100, "monto": 5000, "forma_pago": "cuotas", "estado": "pagado", ...},
    {"id_cobro": 3, "id_pedido": 100, "monto": 5000, "forma_pago": "cuotas", "estado": "pendiente", ...}
]


REPUESTOS

| Campo | Tipo | Tipo SQL | Descripción |
|---|---|---|---|
| `id_repuesto` | int | PK | Identificador único autoincremental |
| `id_pedido` | int | FK → pedidos.id_pedido, NOT NULL | Pedido para el cual se necesita |
| `id_tecnico` | int / None | FK → tecnicos.id_tecnico, NULLABLE | Técnico que lo tiene encima (si ya se entregó) |
| `descripcion` | str | NOT NULL | Descripción del repuesto |
| `estado` | str | NOT NULL | Ver lista abajo |
| `precio` | float / None | NULLABLE | Precio de compra (se carga cuando se compra) |