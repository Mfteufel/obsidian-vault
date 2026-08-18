Redis se utiliza como sistema de cache y control de concurrencia.  
  
## Usos principales  
  
Cachear:  
  
- materias  
- comisiones  
- cupos  
  
## Hold de cupos  
  
Cuando un alumno selecciona una comisión se crea un hold temporal.  
  
Ejemplo:  
  
hold:student123 = commission45  
  
Expira automáticamente si no confirma.