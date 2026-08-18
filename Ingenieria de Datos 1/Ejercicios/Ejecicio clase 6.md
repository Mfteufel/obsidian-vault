![[IDD I_Clase05 Ejercicios.pdf]]


### Gestion de alquiler de vehiculos

1) Identificar Entidades

	**VEHICULO** → IdVehiculo PK, Tipo VARCHAR(50)
	
	**CLIENTE**  → IdCliente PK, nombreCliente VARCHAR(50), apellidoCliente VARCHAR(50)
	
	**EMPLEADO** → IdEmpleado PK, nombreEmpleado VARCHAR(50), apellidoEmpleado VARCHAR(50)
	
	**CONTRATO** → IdContrato PK, IdVehiculo FK, IdCliente FK, IdEmpleado FK,
	precio DECIMAL, fechaInicio DATE, fechaFin DATE
	

2) Relaciones entre Entidades

	Cliente → Contrato1:NUn cliente puede tener muchos contratos
	Empleado → Contrato1:NUn empleado gestiona muchos contratos
	Vehículo → Contrato1:NUn vehículo aparece en muchos contratos (en distintos períodos)

3) Diagramas

	DBD:
	
	![[Pasted image 20260416200037.png]]

	DER
	
	![[DER Auto.jpg]]



### Sistema de reserva de hotel

1) Entidades:
	
	**CLIENTE** --> IdClientes PK , nombreCliente VARCHAR (100), apellidoCliente VARCHAR(100)
	
	**HABITACION** --> IdHabitacion PK , numeroHabitacion INT, tipo VARCHAR(150), precio DECIMAL


	**RESERVA HABITACION** --> IdReserva FK , IdHabitacion FK, 


	**RESERVA** --> IdReserva PK, fechaInicio DATE, fechaFin DATE,  IdCliente FK, IdEmpleado FK
	
	**EMPLEADO** --> IdEmpleado PK, nombreEmpleado VARCHAR(100), apellidoEmpleado VARCHAR(100)

2) Relaciones

	**Cliente → Reserva**: 1:N — un cliente puede tener muchas reservas

	**Empleado → Reserva**: 1:N — un empleado gestiona muchas reservas
	
	**Reserva → ReservaHabitacion**: 1:N — una reserva puede incluir muchas habitaciones
	
	**Habitacion → ReservaHabitacion**: 1:N — una habitación puede aparecer en muchas reservas
	
	**Reserva ↔ Habitacion**: N:M — resuelta a través de `ReservaHabitacion`

  



DBD:

![[Pasted image 20260418173401.png|697]]


DER

![[Hotel.jpg]]


### Sistema de gestión de biblioteca

1) Entidades
   
	**Socio**
	  IdSocio PK INT IDENTITY
	  nombreSocio VARCHAR(100)
	  apellidoSocio VARCHAR(100)
	
	**Empleado**
	  IdEmpleado PK INT IDENTITY
	  nombreEmpleado VARCHAR(100)
	  apellidoEmpleado VARCHAR(100)
	
	**Libro**
	  IdLibro PK INT IDENTITY
	  Titulo VARCHAR(200)
	  Autor VARCHAR(100)
	  ISBN VARCHAR(20)
	
	**Prestamo**
	  IdPrestamo PK INT IDENTITY
	  FechaInicio DATE
	  FechaDevolucion DATE
	  IdSocio FK → Socio
	  IdEmpleado FK → Empleado
	
	**PrestamoLibro**  ← tabla intermedia N:M
	  IdPrestamo FK → Prestamo   (PK compuesta)
	  IdLibro FK → Libro


2) Relaciones 
   
	- **Socio → Prestamo**: 1:N — un socio puede tener muchos préstamos
	- **Empleado → Prestamo**: 1:N — un empleado gestiona muchos préstamos
	- **Prestamo → PrestamoLibro**: 1:N — un préstamo puede incluir varios libros
	- **Libro → PrestamoLibro**: 1:N — un libro puede aparecer en varios préstamos
	- **Prestamo ↔ Libro**: N:M — resuelta a través de `PrestamoLibro`


3) Diagramas

DBD

![[Pasted image 20260418184136.png]]

DER

![[Biblioteca.jpg]]


### Ejercicio 6

-- 1. Empleados que trabajan en "Desarrollo Software"
π nombre (
  Empleados ⋈ Trabaja_en ⋈ (σ nombre_proyecto = 'Desarrollo Software' (Proyectos))
)

-- 2. Proyectos en los que trabaja "Carlos"
π nombre_proyecto (
  Proyectos ⋈ Trabaja_en ⋈ (σ nombre = 'Carlos' (Empleados))
)

-- 3. Proyectos con empleados del departamento "Ingeniería"
π nombre_proyecto (
  Proyectos ⋈ Trabaja_en ⋈ (σ departamento = 'Ingeniería' (Empleados))
)


### Ejercicio 7

-- 1. Ventas a clientes de "Buenos Aires"
π id_venta, fecha (
  Ventas ⋈ (σ ciudad = 'Buenos Aires' (Clientes))
)

-- 2. Suma total de productos vendidos
γ SUM(cantidad) → TotalProductos (DetalleVentas)

-- 3. Ventas con más de 5 productos
π id_venta (
  σ TotalProductos > 5 (
    γ id_venta, SUM(cantidad) → TotalProductos (DetalleVentas)
  )
)


### Ejercicio 8

-- 1. Cursos de alumnos de "Ingeniería Informática"
π nombre_curso (
  Cursos ⋈ Inscripciones ⋈ (σ carrera = 'Ingeniería Informática' (Alumnos))
)

-- 2. Cursos del Semestre 1
π nombre_curso (
  σ semestre = 'Semestre 1' (Cursos)
)

-- 3. Alumnos sin ningún curso inscripto
π nombre (
  Alumnos - (
    π nombre (Alumnos ⋈ Inscripciones)
  )
)


### Ejercicio 9

-- 1. Productos de proveedores de "Madrid"
π nombre_producto (
  Productos ⋈ (σ ciudad = 'Madrid' (Proveedores))
)

-- 2. Pedidos del año 2024
π id_pedido, id_producto, cantidad, fecha_pedido (
  σ YEAR(fecha_pedido) = 2024 (Pedidos)
)

-- 3. Pedidos con cantidad mayor a 50
π id_pedido, id_producto, cantidad (
  σ cantidad > 50 (Pedidos)
)


### Ejercicio 10

-- 1. Libros de bibliotecas de "Barcelona"
π titulo, autor (
  Libros ⋈ RegistroBiblioteca ⋈ (σ ciudad = 'Barcelona' (Bibliotecas))
)

-- 2. Libros de García Márquez en biblioteca "Central"
π titulo (
  (σ autor = 'Gabriel García Márquez' (Libros))
  ⋈ RegistroBiblioteca ⋈
  (σ nombre_biblioteca = 'Central' (Bibliotecas))
)

-- 3. Bibliotecas con más de 10 libros
π nombre_biblioteca (
  σ TotalLibros > 10 (
    γ id_biblioteca, COUNT(*) → TotalLibros (RegistroBiblioteca)
    ⋈ Bibliotecas
  )
)