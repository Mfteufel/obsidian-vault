
## @@FETCH_STATUS — valores posibles

| Valor | Significado |
|-------|-------------|
| `0` | FETCH exitoso — fila disponible |
| `-1` | No quedan más filas |
| `-2` | La fila ya no es válida (fue alterada) |


ACID 


## Niveles de aislamiento

Controlan **qué tan aislada** está la transacción de otras concurrentes.

| Nivel | Bloquea | Permite | Comando |
|-------|---------|---------|---------|
| `READ UNCOMMITTED` | Nada | Lee datos no confirmados (dirty reads) | `SET TRANSACTION ISOLATION LEVEL READ UNCOMMITTED` |
| `READ COMMITTED` | Lecturas sucias | Lecturas no repetibles | `SET TRANSACTION ISOLATION LEVEL READ COMMITTED` |
| `REPEATABLE READ` | Sucias + no repetibles | Phantom reads | `SET TRANSACTION ISOLATION LEVEL REPEATABLE READ` |
| `SERIALIZABLE` | Todo lo anterior | Ningún problema | `SET TRANSACTION ISOLATION LEVEL SERIALIZABLE` |
| `SNAPSHOT` | Usa versión de fila | Requiere habilitar en la BD | `SET TRANSACTION ISOLATION LEVEL SNAPSHOT` |


## Practica

Creación de tablas

Creación de vistas

Creación de Esquemas

Funciones


## Teoría 

Par de preguntas a desarrollar

Multiple choise

V/F con justificación

Teoria Dependecias funcionales

Normalizacion 1FN 2FN 3FN

determinar la clave candidata (final)

## preguntas:

revoque
denite 


crea un esquema finanzas, 
vistas
restriciones

subconsultas con valores limites. ejemplo Sin usar top


crear vista que solo permita ver precios 
Crear una vista y revocar select (Creo que dijo eso)


función
clientes con mas factura
Consultar tabla clientes y facturas, escribir una funcion que de vuelva el Id cliente nombre, total facuturas,
NO SE PUEDE UTILIZA SUBCONSULTAS, VISTAS en la clausula from. ni MAX o MIX
Hay que devolter consulta cliente con mas cantidad de facturas

resultado con TOP 1 y que traiga los empates si los hay

Clase 1:28 dice preguntas del parcial