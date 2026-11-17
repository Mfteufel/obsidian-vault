---
tags: [mt-panel, pruebas]
fuente:
  - "vitest.config.mts"
  - "src/test/db.ts"
  - "src/lib/guards.test.ts"
  - "src/lib/*.test.ts (nombres de los bloques de prueba)"
  - "progreso-tarea3.md"
---

# Pruebas automáticas

**Para qué:** detectar a tiempo si un cambio rompe algo que importa, como el cálculo de plazos, los cobros, las guardias, el ingreso o la protección de las pantallas.

## Cómo correrlas

- `pnpm test` las corre una vez.
- `pnpm test:watch` las vuelve a correr cada vez que se guarda un archivo.

Al momento de escribir esta nota: 10 archivos, 199 pruebas que pasan y 6 pendientes (`it.todo`).

## Qué cubre cada archivo

- `time.test.ts`: horas hábiles, lunes de la semana, inicio de mes y montos.
- `hours.test.ts`: consumo de horas del plan Alto contra el límite.
- `emergencies.test.ts`: cobro de emergencias, guardia asignada y cierre con mal uso.
- `shifts.test.ts`: rotación de guardias.
- `plans.test.ts`: validación y guardado de planes.
- `login.test.ts`: límite de intentos e ingreso.
- `password.test.ts`: reglas de contraseña y email.
- `form.test.ts`: validación de formularios.
- `db.test.ts`: creación de la base y migración de horas.
- `guards.test.ts`: que las acciones y lecturas sigan protegidas.

## Cómo están armadas

- Corren en entorno Node, con una base en memoria (`MT_DB_PATH=":memory:"`).
- La zona horaria se fuerza a Asia/Tokyo. Así, un cálculo que use la hora de la máquina en lugar de la de Buenos Aires falla enseguida.
- `src/test/db.ts` tiene funciones para crear clientes, tickets, cargas de horas, personas y guardias de prueba.
- `guards.test.ts` lee el código fuente. Comprueba que cada acción empiece con `requireUser()`, que cada lectura empiece con `connection()`, que solo `login` y `logout` sean públicas y que los módulos de lógica no importen Next.

## Cómo se comprobó que las pruebas sirven

Se hicieron cambios a propósito en el código (por ejemplo, cambiar el cierre del horario de 18 a 17, o el umbral del 80 % a 90 %, o quitar `requireUser()` de una acción). Las pruebas fallaron en cada caso, y después se revirtió cada cambio.

Los montos de prueba, como 1111 y 222, son ficticios.

## Relacionado

- [[Estructura del código]]
- [[Lecturas y acciones del servidor]]
- [[Tarea 3 · Pruebas automáticas]]
- [[Problemas conocidos y notas técnicas]]
- [[Panel MT]]
