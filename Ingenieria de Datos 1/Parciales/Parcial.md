## **Teorico**

- Opciones verdaderas (Justificar)

- multli (Justificar)

- V/F (Justificar)

- Conceptual (Justificar)

## **Pracatica**

Modelo DER o DBD

- Algebra relacional
	Problema con 3 consultas
	tablas y diseñar una consulta 



# Parcial 2


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


Practica

Creación de tablas

Creación de vistas

creación de Esquemas


Teoria 

Multiple choise

V/F con justificacion

Teoria Dependecias funcionales