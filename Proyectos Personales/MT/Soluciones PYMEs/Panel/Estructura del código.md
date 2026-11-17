---
tags: [mt-panel, arquitectura]
fuente:
  - "README.md"
  - "src/lib/guards.test.ts"
  - "src/lib/db.ts"
  - "src/lib/login.ts"
  - "src/lib/auth.ts"
  - "src/components/ui.tsx"
  - "src/components/forms.tsx"
---

# Estructura del código

**Para qué:** saber en qué archivo está cada cosa antes de tocar algo. Todo el código de la aplicación está en `src/`; lo que está afuera son herramientas de trabajo y datos.

```
src/
├── proxy.ts              Primera barrera: sin cookie, a /login
├── app/
│   ├── (panel)/          Pantallas del panel: Inicio, clientes, tickets,
│   │                     emergencias, guardias y planes (con layout.tsx)
│   ├── (auth)/login/     Pantalla de ingreso
│   ├── layout.tsx        Raíz: metadatos y script que aplica el tema antes de pintar
│   ├── globals.css       Roles de color registrados para Tailwind
│   ├── theme.css         Paleta: el único archivo con colores HEX
│   ├── error.tsx y not-found.tsx
├── components/           Piezas de interfaz, formularios, íconos, logo, menú
├── lib/                  Lógica del negocio y acceso a la base (ver abajo)
└── test/db.ts            Ayudas para las pruebas
scripts/
├── create-user.ts        Alta de usuarios (pnpm create-user)
└── check-contrast.ts     Revisión de la paleta (pnpm check:contrast)
```

## Dos tipos de módulos en `lib/`

- **Lógica pura** (no importa Next ni la base): `hours`, `login`, `emergencies`, `shifts`, `plans`, `form`, `time` y `password`. Reciben la base como parámetro, así se prueban con una base en memoria. La prueba `guards.test.ts` impide que alguien les agregue un import de `next/*`, `./db` o `./auth`.
- **Envoltorios con sesión y base**: `queries.ts` (lecturas), `actions.ts` (cambios), `auth.ts` (sesiones) y `auth-actions.ts` (ingreso y cierre de sesión). Son los únicos que hablan con la sesión y con `getDb()`.

`db.ts` contiene el esquema de la base y `demo.ts` carga los datos de ejemplo.

## Fuera de la aplicación

- `panel.py`, `.claude/` y los archivos `plan-tarea*.md` y `progreso-tarea*.md`: herramientas y registros del trabajo con agentes. Ver [[Cómo se construye el panel con agentes]].
- `data/`: la base de datos y sus copias. No va a git. Ver [[Base de datos SQLite]].
- `README.md` y `CLAUDE.md`: instrucciones para quien trabaja en el proyecto.

## Relacionado

- [[Arquitectura y tecnologías]]
- [[Lecturas y acciones del servidor]]
- [[Pruebas automáticas]]
- [[Cómo se construye el panel con agentes]]
- [[Panel MT]]
