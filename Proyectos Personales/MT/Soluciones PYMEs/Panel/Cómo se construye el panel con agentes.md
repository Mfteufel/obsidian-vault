---
tags: [mt-panel, historia, agentes]
fuente: [CLAUDE.md, AGENTS.md, .claude/agents/planificador.md, .claude/agents/constructor.md, .claude/agents/facil.md, .claude/agents/medio.md, .claude/agents/dificil.md, .claude/settings.json, .claude/hooks/log_evento.py, .claude/statusline.py, panel.py]
---
# Cómo se construye el panel con agentes

El panel se desarrolla con Claude Code. El trabajo se reparte entre agentes según la dificultad, y cada cambio grande pasa por un plan que aprueba el dueño antes de tocar código.

## Quién hace qué
- **Planificador.** Lee el pedido y el código, y escribe `plan.md`. No escribe código. El plan nace con `Estado: PENDIENTE DE APROBACIÓN`.
- **Constructor.** Ejecuta el plan de a un paso por vez, y solo si el estado dice `APROBADO`. Cada paso deja una línea en `progreso.md`.
- **Agentes por dificultad.** `facil` para tareas mecánicas, `medio` para cambios con diseño claro y `dificil` para arquitectura o bugs de causa desconocida. Si uno responde `ESCALAR`, el trabajo sube un nivel.

## Flujo de una tarea
1. Se pide una tarea y el planificador escribe el plan.
2. El dueño revisa el plan y cambia el estado a `APROBADO` a mano. Nunca lo cambia un agente.
3. El constructor hace un paso y se detiene. El dueño da el visto bueno y recién sigue el próximo.
4. Al cerrar la tarea, el plan y el progreso se archivan como `plan-tareaN.md` y `progreso-tareaN.md`.

## Reglas fijas
- `CLAUDE.md` define la delegación por dificultad, el flujo y las reglas de diseño de la marca.
- `AGENTS.md` avisa que Next.js de este proyecto tiene cambios respecto de lo conocido. Los agentes leen su documentación dentro de `node_modules` antes de programar.

## Monitoreo
- Hooks en `.claude/settings.json` registran cada inicio y fin de subagente, y cada herramienta usada, en `.claude/logs/actividad.jsonl`.
- `.claude/statusline.py` es el script de la barra de estado de Claude Code.
- `panel.py` es un monitor de terminal que lee ese registro, el plan y el progreso. Solo lee; no modifica nada.

## Relacionado
- [[Historial de tareas]]
- [[Estructura del código]]
- [[Decisiones abiertas]]
- [[Panel MT]]
