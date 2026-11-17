---
tags: [mt-panel, historia, tarea-3]
fuente: [plan-tarea3.md, progreso-tarea3.md, git log (commits 7a3a541 a 82c060f)]
---
# Tarea 3 · Pruebas automáticas

**Objetivo.** Poner pruebas automáticas (Vitest) sobre la lógica de negocio del panel: plazos, horas, emergencias, guardias, planes, login y contraseñas.

**Qué cambió para el usuario.** Nada se ve en pantalla. El beneficio es que las reglas de cobro, plazos y guardias quedan verificadas antes de cada cambio.

**Qué cambió en el código.**
- Vitest, con los comandos `pnpm test` y `pnpm test:watch`. Las pruebas usan una base en memoria y nunca tocan `data/mt.db`.
- Lógica extraída de las acciones a módulos sin Next: `login.ts`, `emergencies.ts`, `shifts.ts` y `plans.ts`.
- `createDb(file)` en `db.ts`, para crear una base de prueba.
- Un archivo de pruebas que revisa el código: exige que cada acción y lectura verifique la sesión, y que los módulos puros no importen Next ni la base.
- Resultado final: 10 archivos, 199 pruebas que pasan y 6 pendientes (`it.todo`).

**Decisiones y desvíos.** Se usó solo Vitest con entorno Node, sin jsdom, porque la prueba es de lógica de servidor. Un hook de herramientas inválido se quitó de la configuración (commit `036b3b6`).

**Cómo se verificó.** Cada grupo de pruebas se comprobó con mutaciones temporales: por ejemplo, cambiar un límite, un redondeo o un comparador hizo fallar las pruebas esperadas, y luego se revirtió el archivo original. Al cerrar, la base real seguía intacta.

**Pendientes que dejó.** Los seis `it.todo` están listados en [[Decisiones abiertas]]. Además, el caso "lunes 17:00 + 1 h" quedó documentado como comportamiento actual, a la espera de decisión.

## Relacionado
- [[Historial de tareas]]
- [[Pruebas automáticas]]
- [[Estructura del código]]
- [[Problemas conocidos y notas técnicas]]
- [[Panel MT]]
