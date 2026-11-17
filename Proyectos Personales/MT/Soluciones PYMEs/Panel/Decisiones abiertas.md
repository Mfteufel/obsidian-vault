---
tags: [mt-panel, historia, pendientes]
fuente: [README.md, plan-tarea2.md, progreso-tarea3.md, progreso-tarea4.md, progreso-tarea5.md, src/lib/time.test.ts]
---
# Decisiones abiertas

Lo que todavía tiene que decidir el dueño. Al resolver un punto, se tacha y se actualiza la nota enlazada.

## Precios y cobros
- [ ] Precio mensual de cada plan, de la emergencia y del mal uso. Hoy están sin definir a propósito. Ver [[Planes y precios]] y [[Emergencias y cobros]].
- [ ] ¿El plan Básico tiene botón de emergencia? Hoy es configurable.
- [ ] Tiempos de respuesta definitivos. Las 9, 4 y 1 horas hábiles son solo una sugerencia.
- [ ] Límite de horas del plan Alto y qué pasa al superarlo. Hoy solo se avisa, no se cobra. Ver [[Horas de funcionalidades del plan Alto]].
- [ ] Horas cargadas antes de la Tarea 2: hoy cuentan en el mes de creación del ticket. ¿Está bien?
- [ ] Corregir o borrar una carga de horas equivocada. Hoy no se puede.
- [ ] Si un cliente cambia de plan a mitad de mes, ¿el mes entero usa el límite nuevo?
- [ ] Límite 0: ¿significa "no incluye horas" o "sin límite"? Hoy 0 horas da "ok" y cualquier hora da "superado".

## Horarios
- [ ] "Lunes 17:00 + 1 h" da lunes 18:00. Hay una prueba que lo documenta como comportamiento actual. Ver [[Horas hábiles]].

## Pruebas pendientes (6 `it.todo`)
- Emergencias: precio vacío en Medio, resolver de nuevo con mal uso y doble alta.
- Guardias: alguien inactivo la semana anterior, y guardia manual en una semana futura.
- Planes: precio con punto de miles (1.000), que el formulario todavía no acepta.

## Diseño
- [ ] Paleta oficial de MT. Hoy es una interpretación de los logos; los pasos para cargarla están en el README.
- [ ] Proporción de colores: se aceptó un reparto de unos 82 % gris claro, 17 % carbón y poco bordó por el menú de 240 px. Quedan sin tomar las opciones B (menú más ancho) y C (más bordó en Inicio).
- [ ] Favicon: sin tocar.

## Ya resuelto (no reabrir)
- Un cliente inexistente responde HTTP 200, no 404. Lo aceptó el dueño en la [[Tarea 5 · Tema, logo y error 419]].
- Cuentan para el límite solo las horas de tickets de tipo funcionalidad. Confirmado.

## Relacionado
- [[Historial de tareas]]
- [[Planes y precios]]
- [[Emergencias y cobros]]
- [[Horas de funcionalidades del plan Alto]]
- [[Identidad visual y paleta]]
- [[Problemas conocidos y notas técnicas]]
- [[Panel MT]]
