## GET /me  
  
Devuelve información del alumno.  
  
## GET /subjects  
  
Lista materias disponibles y sus comisiones.  
  
## GET /schedule  
  
Devuelve el horario actual del alumno.  
  
## POST /hold  
  
Reserva temporal de cupo.  
  
Ejemplo:  
  
{  
"student_id": 123,  
"commission_id": 45  
}  
  
## POST /confirm  
  
Confirma la inscripción.  
  
## DELETE /enrollment/{id}  
  
Cancela una inscripción.