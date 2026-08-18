


![[IDD I_Clase01_AFB.pdf]]


## Evolución del almacenamiento  
  
1️⃣ Sistemas de archivos  
- Conjunto de archivos de datos y programas.  
  
2️⃣ Modelo jerárquico  
- Estructura de árbol.  
  
3️⃣ Modelo en red  
- Relaciones más complejas.  
  
4️⃣ Modelo relacional  
- Datos en tablas (filas y columnas).  
- Propuesto por Edgar F. Codd.  
  
## Big Data  
  
Características:  
- grandes volúmenes de datos  
- datos estructurados y no estructurados  
- escalabilidad  
- procesamiento rápido



## [[Persistencia]]



## Sistema de acceso e indices

- [[Acceso Secuencial]]
- [[Acceso Directo]]
- [[Hashing (Algoritmos de Clave)]]
- [[Archivos Indexados]]
- [[Indice de Arboles Binarios]]


## Arquitectura de un SGBD

**Que es una [[SGBD]]?**

![[Pasted image 20260310210825.png]]


Un **Sistema de Gestión de Bases de Datos (SGBD)** organiza y administra los datos mediante una arquitectura de **tres niveles**, que separa la forma en que los usuarios ven los datos de cómo estos se almacenan físicamente.

Esta arquitectura permite **independencia entre la lógica de los datos y su almacenamiento físico**.

---

# 1. Nivel Externo

El **nivel externo** representa las **vistas individuales de los usuarios**.

Cada usuario o aplicación puede ver solo la parte de la base de datos que necesita.

Ejemplos de usuarios:

- usuarios normales
    
- programadores de aplicaciones
    
- disenador BD
   
- Data Owner
    
- administradores de bases de datos (DBA)
    

Objetivo:

- simplificar el acceso a los datos
    
- mejorar la seguridad
    
- mostrar solo la información necesaria.
    

---

# 2. Nivel Conceptual

El **nivel conceptual** describe la **estructura lógica completa de la base de datos**.

Define:

- las tablas
    
- los atributos
    
- las relaciones entre los datos
    
- las restricciones
    

Es una visión **global del sistema de datos**, independiente de cómo se almacenan físicamente.

---

# 3. Nivel Interno

El **nivel interno** define **cómo los datos se almacenan físicamente en el sistema**.

Incluye:

- organización en disco
    
- índices
    
- estructuras de almacenamiento
    
- archivos de datos
    

Este nivel optimiza el **rendimiento y la eficiencia del acceso a los datos**.

---

# Independencia de datos

La arquitectura permite dos tipos de independencia:

### Independencia lógica

Permite modificar la estructura lógica de la base de datos sin afectar las aplicaciones que la utilizan.

### Independencia física

Permite cambiar la forma en que los datos se almacenan físicamente sin modificar el modelo lógico.

---

# Componentes internos del SGBD

El sistema también incluye distintos módulos que permiten procesar las consultas y administrar los datos:

- **Procesador de consultas** → interpreta y ejecuta consultas SQL
    
- **Gestor de memoria** → administra el uso de memoria
    
- **Gestor de archivos** → controla el almacenamiento en disco
    
- **Gestor de transacciones** → garantiza consistencia de los datos
    
- **Gestor de autorización** → controla permisos de acceso
    

---

# Estructuras de almacenamiento

Dentro del sistema se almacenan diferentes tipos de información:

- **Datos** → información principal de la base de datos
    
- **Índices** → estructuras para acelerar búsquedas
    
- **Diccionario de datos** → metadatos sobre la base de datos
    
- **Datos estadísticos** → información para optimizar consultas
    

---

✔ **Idea clave para recordar para el examen**

La arquitectura de un SGBD se basa en **tres niveles**:

Nivel externo → vista de los usuarios  
Nivel conceptual → estructura lógica de los datos  
Nivel interno → almacenamiento físico

Esta separación permite **independencia lógica y física de los datos**.