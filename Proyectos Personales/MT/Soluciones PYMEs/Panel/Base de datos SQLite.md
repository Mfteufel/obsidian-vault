---
tags: [mt-panel, datos]
fuente:
  - "src/lib/db.ts"
  - "README.md"
  - "data/ (solo listado de nombres)"
  - "progreso-tarea2.md"
  - "progreso-tarea3.md"
---

# Base de datos SQLite

**Para qué:** acá viven todos los datos del panel: clientes, planes, tickets, horas, emergencias, guardias, equipo y usuarios. Es un solo archivo, sin servidor de base aparte.

## Dónde está

- Archivo `data/mt.db`. Se crea solo la primera vez que se usa el panel.
- No va a git: la carpeta `data/` está ignorada.
- Para guardarla en otro lugar, se usa la variable `MT_DB_PATH`.
- Usa modo WAL: junto a `mt.db` aparecen `mt.db-wal` y `mt.db-shm`. Las claves foráneas están activadas.

## Tablas

| Tabla | Qué guarda |
|---|---|
| `plans` | Los tres planes (Básico, Medio, Alto). Los precios están vacíos a propósito |
| `settings` | Ajustes generales, hoy el extra por mal uso |
| `team_members` | El equipo que hace guardias y atiende emergencias |
| `clients` | Clientes, con su estado y su plan |
| `tickets` | Pedidos de soporte de cada cliente |
| `ticket_hours` | Cada carga de horas de un ticket, con fecha y persona |
| `emergencies` | Emergencias registradas, con su cobro |
| `users` | Personas que pueden entrar al panel |
| `sessions` | Sesiones abiertas (solo se guarda un hash del token) |
| `login_attempts` | Intentos fallidos de ingreso, para el límite |
| `shifts` | Una guardia por semana |

Relaciones principales: un cliente tiene un plan y tiene tickets; cada ticket acumula cargas de horas; cada emergencia pertenece a un cliente y puede tener una persona del equipo asignada; cada guardia apunta a una persona.

## Cómo se crea y cómo cambia

Al arrancar, se crean las tablas que falten, se cargan los tres planes si no hay ninguno y se aplican las migraciones pendientes. Las migraciones se llevan con `PRAGMA user_version`.

La migración 1 pasó las horas viejas de cada ticket a `ticket_hours`, usando la fecha de creación del ticket como fecha de carga. Esas filas quedan marcadas como migradas. Se hace una sola vez.

## Copias

Hoy no hay copia automática. En la carpeta `data/` hay copias hechas a mano antes de cambios grandes, con nombres como `respaldo-antes-<etapa>-<fecha>.db`. Los detalles para respaldar están en [[Publicación y copias de seguridad]].

## Relacionado

- [[Publicación y copias de seguridad]]
- [[Horas de funcionalidades del plan Alto]]
- [[Acceso, sesiones y seguridad]]
- [[Lecturas y acciones del servidor]]
- [[Panel MT]]
