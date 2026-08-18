
### Estudiantes(ID_Estudiante, Nombre)
### Cursos(ID_Curso, Nombre_Curso, Categoria)
### Inscripciones(ID_Estudiante, ID_Curso, Fecha_Inscripcion)


1. Obtené los **nombres** de los cursos de categoría **'Tecnología'**
   
   π Nombre_Curso (σ Categoria='Tecnología' (Cursos))
   
   
2. Obtené los **nombres** de los estudiantes inscriptos en el curso **'Base de Datos'** 
   
   R1 = (Curso ⋈ curso.id_curso = inscripcion.id_curso Inscripcion)

	 R2 = (Estudiante ⋈ estudiante.id_estudiante = r1.id_estudiante R1)
	
	 R3 = σ(nombre_curso= 'Base de Datos'(R2))
	
	 RF = π nombre(R3)


3. Mostrá los **nombres de cursos** en los que **NO** hay ningún estudiante inscripto
   
   R1 = π id_curso(Cursos)
 
   R2 = π id_curso(Inscripciones) 
   
   R3 = R1 - R2

   R4 = Cursos ⋈ id_curso.Cursos = id_curso.R3 R3 
   
   Rf = π nombre_curso(R4)



4. Mostrá los **nombres** de estudiantes que **NO** están inscriptos en ningún curso de categoría **'Tecnología'**