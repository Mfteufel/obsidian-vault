# 🛠️ Brief Técnico: Smart Enrollment Dashboard
**Para:** Equipo Antigravity

## 🎨 UI & Estética
- **Style:** Clean & Focus (Moderno, minimalista).
- **Paleta:** Azul UADE (#003594) + Semáforo de estados (Verde/Ámbar/Rojo para cupos).
- **Layout:** Pantalla dividida (Split View) en 3 paneles:
	1. **Panel Izquierdo:** Árbol de materias (Plan 1121).
	2. **Panel Central:** Card-based Explorer para comisiones.
	3. **Panel Derecho:** Calendario Live (Visualización de agenda).

## ⚙️ Funcionalidad Requerida (Frontend)
- **Zero-Reload:** Filtrado dinámico mediante chips de selección.
- **Drag & Drop / Click to Add:** Interacción fluida entre lista y calendario.
- **Validación en Tiempo Real:** Bloqueo de solapamientos horarios antes del submit.

## 🔗 Requerimientos de API
- Endpoint de "Cupos en tiempo real".
- Servicio de validación de correlativas automático al cargar perfil de alumno.