#### Características:

- **Representación de Información mediante Objetos**: Atributos, Métodos
- Sistema que **almacena y recupera objetos** con una base de gestión en un lenguaje de programación orientado a objetos
- Características de **Persistencia de objetos** y **Transparencia**
- Los objetos creados y utilizados dentro del código pueden **almacenarse y recuperarse sin necesidad de convertirlos a un formato relacional tradicional** (tablas, filas, columnas)

Clase LIBRO
  Atributos:
    - Título (String)
    - Autor (String)
    - ISBN (String)
    - AñoPublicación (Integer)
    - Ejemplares (List<Ejemplar>)
  Métodos:
    - Prestar()
    - Devolver()

Clase USUARIO
  Atributos:
    - Nombre (String)
    - IDUsuario (String)
    - PrestamosActuales (List<Ejemplar>)
  Métodos:
    - SolicitarPrestamo(Ejemplar)
    - DevolverPrestamo(Ejemplar)