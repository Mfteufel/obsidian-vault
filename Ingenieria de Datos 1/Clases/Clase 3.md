![[IDD I_Clase03_AFB.pdf]]

## Modelo Relacional 

Varrel: Relación variable dinámica 

[[Atributos]]

[[Cardinalidad]]

[[Dominio]]

 **<u>Manipulación de datos</u>**

- [[Algebra Relacional]]

- [[Calculo realacional]] 


### <font color="#ff0000">Conceptos Generales</font>

**Relación base (varrels base):**
Relación autónoma con nombre propio y datos propios.

Relación derivada:
Relación definida mediante expresiones de álgebra relacional en términos de otras
relaciones (Vista) no posee datos propios


```sql
SELECT *
FROM Clientes
WHERE Estado=‘Activo’
```

**Instantánea:**
Relación derivada no actualizable, tiene datos asociados, aunque no propios.

```sql
SELECT *
FROM Clientes
WHERE Alta BETWEEN
‘2024-01-01’ AND ‘2024-03-31’
```


**Resultados de consultas:**
Relación derivada generalmente sin nombre, existe mientras existe la sesión en la que
fue creada

```sql
SELECT *
FROM Clientes
WHERE Alta BETWEEN
‘2024-01-01’ AND ‘2024-03-31’
```


### <font color="#ff0000"><font color="#ff0000">Tipos de Relacione</font>s</font>

**Relación Externa (Remote Table):**

Involucra datos que se encuentran en una base de datos externa pero que se pueden consultar como si fueran parte de la base de datos local.

**Relación Materializada (Materialized View):**

Similar a una relación derivada, pero los resultados se almacenan físicamente en la base de datos y se actualizan periódicamente.

**Relación de Asociación**:

Una relación que conecta dos tablas a través de una tabla intermediaria, utilizada para modelar relaciones de muchos a muchos.

**Relación Cruzada (Cross Join):**

Una relación temporal que surge del producto cartesiano entre dos tablas, generando
todas las combinaciones posibles de filas entre ellas


**Relaciones y su interpretacion**

En una base de datos relacional, una relación es una tabla que contiene datos organizados en filas (tuplas) y columnas (atributos). Cada relación tiene un significado específico dentro del contexto del sistema de información. Este significado es una interpretación conceptual de lo que los datos en la tabla representan en la realidad que la base de datos modela.


**Predicado:**

Un predicado es una expresión lógica que, al evaluarse, devuelve verdadero o
falso. En el contexto de las bases de datos, un predicado se utiliza para
determinar si una tupla (una fila) puede ser parte de una relación.

```sql
INSERT INTO CLIENTES (ID, CUIT, RazonSocial, Direccion, Email)
VALUES (1, '20-12345678-9', 'JPérez S.A.', 'Calle Falsa 123', 'juan.perez@empresa.com');
```


**Preposición**

Una proposición es un predicado aplicado a una tupla específica. Cuando se
inserta o actualiza una fila en una tabla, la proposición se evalúa para determinar si la fila cumple con las reglas de la relación.

```sql
INSERT INTO CLIENTES (ID, CUIT, RazonSocial, Direccion, Email)
VALUES (2, '30123458', 'Gómez SRL', 'Avenida Viva 456', 'contacto@gomez.com');
```


**Integridad entidad**

La integridad de entidad asegura que cada relación (tabla) tenga
una **clave primaria** única, garantizando que no existan dos filas
en la tabla con la misma clave primaria. Esto asegura que cada
registro en la tabla sea identificable de manera única.

```sql
CREATE TABLE CLIENTES (
ID INT PRIMARY KEY,
CUIT CHAR(11) NOT NULL,
RazonSocial VARCHAR(255) NOT NULL,
Direccion VARCHAR(255),
Email VARCHAR(255)
);
```


**Integridad Referencial**

La integridad referencial asegura que las claves foráneas en una
tabla apunten siempre a valores válidos en otra tabla. Esto
significa que si una tabla tiene una clave foránea que referencia
la clave primaria de otra tabla, no se pueden insertar o actualizar
valores en la clave foránea que no existan en la tabla
referenciada.


```sql
CREATE TABLE PEDIDOS (
PedidoID INT PRIMARY KEY,
ClienteID INT,
FechaPedido DATE,
Monto DECIMAL(10, 2),
FOREIGN KEY (ClienteID) REFERENCES CLIENTES(ID)
);
```
d
## Diagram entidad-relación (ERD)  [[DER]]

Es una representación gráfica que muestra las entidades de un sistema de
base de datos y las relaciones entre ellas. Los ERD se utilizan principalmente
durante la fase de diseño conceptual para modelar la estructura de los datos
y entender cómo interactúan las diferentes entidades en un sistema.

![[Pasted image 20260331200706.png]]


