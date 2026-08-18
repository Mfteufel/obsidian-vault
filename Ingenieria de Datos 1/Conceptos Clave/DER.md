---
tags:
  - modelado
  - diseno-conceptual
  - er
  - entidades
  - atributos
  - cardinalidad
aliases:
  - DER
  - ERD
  - Modelo ER
  - Modelo Entidad-Relacion
  - Diagrama Entidad-Relacion
---
## Qué es un ERD?

Un **ERD (Entity-Relationship Diagram)** o **DER (Diagrama Entidad-Relación)** es una representación gráfica de la estructura de una base de datos. Muestra las **entidades**, sus **atributos** y las **relaciones** entre ellas, antes de pasar al modelo relacional (tablas).

> Es la etapa de **diseño conceptual**: no hay SQL todavía, solo la lógica del negocio.

---

## Componentes del ERD

### 1. Entidad — Rectángulo

Representa un objeto del mundo real sobre el que se quiere almacenar información.

```
┌──────────┐
│ CLIENTE  │
└──────────┘
```

- Cada entidad se convierte en una **tabla** en el modelo relacional.
- Ejemplo del diagrama: `CLIENTE`, `PEDIDO`, `PRODUCTO`, `PROVEEDORES`

---

### 2. Atributo — Elipse

Propiedad que describe a una entidad.

```
      ┌────┐
      │ ID │
      └────┘
         |
   ┌──────────┐
   │ CLIENTE  │
   └──────────┘
```

- Cada atributo se convierte en una **columna** de la tabla.
- Ejemplo del diagrama:
    - `CLIENTE` → ID, CUIT, RAZON SOCIAL
    - `PEDIDO` → PEDIDO (nro), CLIENTEID, FECHA PEDIDO
    - `PRODUCTO` → NOMBRE, PRECIO, CODIGO
    - `PROVEEDORES` → NOMBRE, DIRECCION

---

### 3. Relación — Rombo

Representa la asociación entre dos o más entidades.

```
┌──────────┐        ◇         ┌──────────┐
│ CLIENTE  │───── Realiza ────│  PEDIDO  │
└──────────┘                  └──────────┘
```

- Ejemplo del diagrama:
    - `Realiza` → entre CLIENTE y PEDIDO
    - `Compra` → entre CLIENTE y PRODUCTO
    - `Suministra` → entre PRODUCTO y PROVEEDORES

---

## Cardinalidad en el ERD

La cardinalidad se escribe sobre las líneas de relación e indica cuántas instancias participan.

### Del diagrama de ejemplo:

|Relación|Entidades|Cardinalidad|Interpretación|
|---|---|---|---|
|**Realiza**|CLIENTE → PEDIDO|1:N|Un cliente realiza muchos pedidos; cada pedido pertenece a un solo cliente|
|**Compra**|CLIENTE ↔ PRODUCTO|N:M|Un cliente compra muchos productos; un producto es comprado por muchos clientes|
|**Suministra**|PRODUCTO ↔ PROVEEDORES|N:M|Un producto puede ser suministrado por varios proveedores; un proveedor suministra varios productos|

---

## Cómo se traduce al Modelo Relacional

### Relación 1:N → FK en el lado N

```
CLIENTE (ID pk, CUIT, RAZON_SOCIAL)
PEDIDO  (PEDIDO pk, FECHA_PEDIDO, CLIENTEID fk → CLIENTE.ID)
```

> `CLIENTEID` en PEDIDO es la FK que apunta a CLIENTE.

---

### Relación N:M → Tabla intermedia

```
-- Relación Compra (CLIENTE N:M PRODUCTO)
CLIENTE_PRODUCTO (ID_CLIENTE fk, CODIGO_PRODUCTO fk)
                  └── PK compuesta

-- Relación Suministra (PRODUCTO N:M PROVEEDORES)
PRODUCTO_PROVEEDOR (CODIGO_PRODUCTO fk, ID_PROVEEDOR fk)
                    └── PK compuesta
```

---

## Pasos para armar un ERD

1. **Identificar entidades** — ¿Sobre qué cosas necesito guardar datos?
2. **Identificar atributos** — ¿Qué propiedades tiene cada entidad?
3. **Definir la clave primaria** — ¿Qué atributo identifica unívocamente cada instancia?
4. **Identificar relaciones** — ¿Cómo se vinculan las entidades entre sí?
5. **Determinar cardinalidades** — ¿1:1, 1:N o N:M?
6. **Verificar participación** — ¿La relación es obligatoria u opcional?

---

## Reglas de traducción al Modelo Relacional

|ERD|Modelo Relacional|
|---|---|
|Entidad|Tabla|
|Atributo|Columna|
|Atributo clave|PRIMARY KEY|
|Relación 1:N|FK en la tabla del lado N|
|Relación N:M|Tabla intermedia con FK de ambas partes|
|Relación 1:1|FK en cualquiera de las dos tablas|

---

## Ejemplo completo — del ERD al SQL

sql

```sql
CREATE TABLE CLIENTE (
    ID          INT PRIMARY KEY,
    CUIT        VARCHAR(13) NOT NULL,
    RAZON_SOCIAL VARCHAR(100) NOT NULL
);

CREATE TABLE PEDIDO (
    PEDIDO       INT PRIMARY KEY,
    FECHA_PEDIDO DATE NOT NULL,
    CLIENTEID    INT NOT NULL,
    FOREIGN KEY (CLIENTEID) REFERENCES CLIENTE(ID)
);

CREATE TABLE PRODUCTO (
    CODIGO INT PRIMARY KEY,
    NOMBRE VARCHAR(100) NOT NULL,
    PRECIO DECIMAL(10,2) NOT NULL
);

CREATE TABLE PROVEEDORES (
    ID        INT PRIMARY KEY,
    NOMBRE    VARCHAR(100) NOT NULL,
    DIRECCION VARCHAR(200)
);

-- Tabla intermedia: Compra (CLIENTE N:M PRODUCTO)
CREATE TABLE COMPRA (
    ID_CLIENTE      INT,
    CODIGO_PRODUCTO INT,
    PRIMARY KEY (ID_CLIENTE, CODIGO_PRODUCTO),
    FOREIGN KEY (ID_CLIENTE)      REFERENCES CLIENTE(ID),
    FOREIGN KEY (CODIGO_PRODUCTO) REFERENCES PRODUCTO(CODIGO)
);

-- Tabla intermedia: Suministra (PRODUCTO N:M PROVEEDORES)
CREATE TABLE SUMINISTRA (
    CODIGO_PRODUCTO INT,
    ID_PROVEEDOR    INT,
    PRIMARY KEY (CODIGO_PRODUCTO, ID_PROVEEDOR),
    FOREIGN KEY (CODIGO_PRODUCTO) REFERENCES PRODUCTO(CODIGO),
    FOREIGN KEY (ID_PROVEEDOR)    REFERENCES PROVEEDORES(ID)
);
```

---

## Preguntas de repaso

1. ¿Qué símbolo representa una entidad en el ERD? ¿Y una relación?
2. ¿Cómo se resuelve una relación N:M al pasar al modelo relacional?
3. En el diagrama de ejemplo, ¿por qué `CLIENTEID` está en la tabla PEDIDO y no al revés?
4. ¿Qué diferencia hay entre el modelo conceptual (ERD) y el modelo lógico (tablas)?
5. ¿Puede una relación tener atributos propios? ¿Dónde irían al traducir al modelo relacional?

---

## Tipos de atributos (Clase 13)

| Tipo | Definición | Ejemplo |
|------|-----------|---------|
| **Simple** | No puede dividirse | Nombre, Edad |
| **Compuesto** | Se divide en subcomponentes | Dirección → Calle, Número, Ciudad |
| **Monovaluado** | Un solo valor | DNI |
| **Multivaluado** | Múltiples valores | Teléfonos |
| **Derivado** | Calculado desde otro atributo | Edad ← Fecha de Nacimiento |

**Participación:**

- **Total** → cada entidad del conjunto debe participar en la relación
- **Parcial** → no todas las entidades participan

---

## Relacionado

[[Atributos]] · [[Cardinalidad]] · [[CC_Desnormalizacion]] · [[Restricciones_Modelo_Relacional]] · [[Clase 13 - Formas Normales Superiores y Modelo ER]]
