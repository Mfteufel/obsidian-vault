### US-01: Visualización del Plan de Estudios

**Como** alumno, **Quiero** ver mis materias pendientes organizadas por año y cuatrimestre, **Para** identificar rápidamente qué me corresponde cursar sin abrir archivos externos.

- **Criterio de Aceptación 1:** El sistema debe cargar el Plan 1121 automáticamente basado en el legajo.
    
- **Criterio de Aceptación 2:** Las materias que no cumplen correlativas deben aparecer en estado "deshabilitado" (grisado) con un _tooltip_ que explique por qué.
    
- **Criterio de Aceptación 3:** Debe existir un buscador de texto para filtrar materias por nombre o código dentro del plan.
    

---

### US-02: Explorador de Comisiones y Cupos

**Como** alumno en período de inscripción, **Quiero** filtrar las comisiones por turno y ver la disponibilidad de vacantes en tiempo real, **Para** no perder tiempo intentando anotarme en clases llenas.

- **Criterio de Aceptación 1:** Al seleccionar una materia, se deben desplegar las comisiones con: Nombre del docente, Aula, y una **Barra de Cupo** (Visual: Verde > 10, Naranja < 5, Rojo = 0).
    
- **Criterio de Aceptación 2:** Los filtros de "Turno" (Mañana/Tarde/Noche) deben actualizar la lista de comisiones instantáneamente (sin recargar página).
    
- **Criterio de Aceptación 3:** El sistema debe permitir "pre-visualizar" el horario en el calendario antes de agregar al carrito.
    

---

### US-03: Detección Automática de Conflictos

**Como** alumno que arma su agenda, **Quiero** que el sistema me avise si dos materias se solapan en el mismo horario, **Para** evitar errores de inscripción que luego deba corregir manualmente.

- **Criterio de Aceptación 1:** Si una comisión seleccionada coincide en día/hora con otra ya presente en el "calendario fantasma", el bloque debe parpadear en rojo.
    
- **Criterio de Aceptación 2:** El botón "Agregar al Carrito" debe deshabilitarse si existe un conflicto horario.
    
- **Criterio de Aceptación 3:** Se debe mostrar un mensaje claro: "Conflicto detectado con [Nombre de Materia]".
    

---

### US-04: Carrito y Confirmación Final

**Como** alumno listo para cursar, **Quiero** tener un resumen persistente de mis materias seleccionadas y un botón de acción claro, **Para** finalizar el proceso de inscripción de manera rápida y segura.

- **Criterio de Aceptación 1:** Un panel lateral o flotante debe mostrar el contador de materias (ej: `Materias: 3/6`).
    
- **Criterio de Aceptación 2:** El botón "Inscribirse" solo se activa si el carrito tiene al menos 1 materia y 0 conflictos.
    
- **Criterio de Aceptación 3:** Al finalizar, se debe generar un comprobante digital (PDF o e-mail) y mostrar un mensaje de éxito con confeti (opcional, pero ayuda a la experiencia positiva).
    

---

## 🛠️ Reglas de Negocio (Para el Backend)

Añade este bloque al final de tu nota para que Antigravity sepa las restricciones:

> [!IMPORTANT] Rules of Engagement
> 
> 1. **Persistencia:** Si el usuario cierra la pestaña por accidente, el carrito debe persistir (usar LocalStorage o SessionState).
>     
> 2. **Performance:** El tiempo de respuesta entre el clic en "Materia" y el despliegue de "Comisiones" debe ser menor a 300ms.
>     
> 3. **Mobile First:** El calendario debe transformarse en una "Lista de Horarios" en dispositivos móviles para mantener la legibilidad.

