---
tags: [modelado, normalizacion, formas-normales, 3fn, dependencia-transitiva]
aliases: [3FN, Tercera Forma Normal, dependencia transitiva]
---

# 3FN — Tercera Forma Normal

## Definición

> Una relación está en 3FN si está en **2FN** y todos los atributos no clave dependen **exclusivamente de la clave primaria**, sin **dependencias transitivas**.

---

## ¿Qué elimina?

Las **dependencias transitivas**: cuando un atributo no clave depende de **otro atributo no clave** en lugar de la PK.

```
PK → AtributoNoClaveA → AtributoNoClaveB   ← TRANSITIVA (mal)
```

Todos los atributos no clave deben depender **solo** de la PK.

---

## Las 2 condiciones

| Condición | Detalle |
|-----------|---------|
| **Cumplir 1FN y 2FN** | Datos atómicos + sin dependencias parciales |
| **Sin transitivas** | Ningún atributo no clave depende de otro no clave |

---

## Ejemplo del curso

Tabla en 2FN pero con transitivas:

| ID_Proyecto | NombreProyecto | Ciudad | CodPostal | JefeProyecto | Acceso | Telefono |
|------------|---------------|--------|-----------|-------------|--------|----------|
| 1 | App Móvil | Pinamar | 7167 | Mauricio Serrato | 82173912 | 37268384 |
| 2 | Web Corp | Mar del Plata | 7600 | Victor Contreras | 34123793 | 21241445 |

**Dependencias transitivas detectadas:**

```
{ID_Proyecto} → {Ciudad}         y  {Ciudad} → {CodPostal}
  ⇒ CodPostal depende de Ciudad, no de ID_Proyecto

{ID_Proyecto} → {JefeProyecto}   y  {JefeProyecto} → {Acceso, Telefono}
  ⇒ Acceso/Telefono dependen de JefeProyecto, no de ID_Proyecto
```

---

## Solución — separar las transitivas

**Tabla principal:**

| ID_Proyecto | NombreProyecto | Ciudad | ID_JefeProyecto |
|------------|---------------|--------|----------------|
| 1 | App Móvil | Pinamar | 2341 |
| 2 | Web Corp | Mar del Plata | 2342 |

**Tabla Ciudad → CodPostal:**

| Ciudad | CodPostal |
|--------|-----------|
| Pinamar | 7167 |
| Mar del Plata | 7600 |

**Tabla Jefe:**

| ID_JefeProyecto | JefeProyecto | Acceso | Telefono |
|----------------|-------------|--------|----------|
| 2341 | Mauricio Serrato | 82173912 | 37268384 |
| 2342 | Victor Contreras | 34123793 | 21241445 |

✅ Cada atributo ahora depende directamente de la clave de su tabla.

---

## Equivalente en SQL

```sql
CREATE TABLE CiudadCP (
    Ciudad    VARCHAR(50) PRIMARY KEY,
    CodPostal VARCHAR(10)
);

CREATE TABLE Jefe (
    ID_JefeProyecto INT PRIMARY KEY,
    JefeProyecto    VARCHAR(100),
    Acceso          VARCHAR(20),
    Telefono        VARCHAR(20)
);

CREATE TABLE Proyecto (
    ID_Proyecto     INT PRIMARY KEY,
    NombreProyecto  VARCHAR(100),
    Ciudad          VARCHAR(50),
    ID_JefeProyecto INT,
    FOREIGN KEY (Ciudad) REFERENCES CiudadCP(Ciudad),
    FOREIGN KEY (ID_JefeProyecto) REFERENCES Jefe(ID_JefeProyecto)
);
```

---

## 2FN vs 3FN — diferencia clave

| | Elimina | Tipo de problema |
|---|---------|-----------------|
| **2FN** | Dependencias **parciales** | Atributo depende de parte de la PK |
| **3FN** | Dependencias **transitivas** | Atributo depende de otro no clave |

---

## Relacionado

[[CC_2FN]] · [[CC_BCNF]] · [[CC_Tipos_DF]] · [[CC_1FN]] · [[Clase 12 - Normalizacion Formas Normales]]
