---
tags: [mt-panel, operacion]
fuente:
  - "README.md"
  - "src/lib/auth.ts"
  - "src/lib/db.ts"
  - "scripts/create-user.ts"
  - "data/ (solo listado de nombres)"
---

# Publicación y copias de seguridad

**Para qué:** qué cuidar si el panel sale de una computadora y se publica en internet, y cómo evitar perder los datos de los clientes.

## Antes de publicarlo

- **Usar HTTPS.** La cookie de sesión se marca `secure` en producción, y el navegador no la envía por HTTP. La excepción es `localhost`.
- **Reenviar la IP real.** Si hay un proxy delante, tiene que pasar la IP del visitante en el encabezado `x-forwarded-for`. Si no, todos los pedidos llegan con la misma IP y el límite de intentos bloquea a todo el equipo junto.
- **Usar el build de producción.** Con `pnpm build` y después `pnpm start`. `pnpm dev` no es para publicar.
- **Elegir dónde queda la base.** Con `MT_DB_PATH` se puede guardar fuera de la carpeta del código.

## Copias de seguridad

- Hay que copiar `data/mt.db` y, si existen, `mt.db-wal` y `mt.db-shm`. Como la base usa modo WAL, los cambios recientes pueden estar en el archivo `-wal` y no en `mt.db`. Conviene copiar los tres juntos y, si se puede, con el panel detenido.
- En `data/` hay copias hechas a mano antes de cambios grandes, con nombres como `respaldo-antes-<etapa>-<fecha>.db`. Ese es el criterio usado hasta ahora; no hay nada automático en el proyecto.
- Las copias tienen datos de clientes. Hay que guardarlas con el mismo cuidado que la base.

## Usuarios

Los usuarios y sus contraseñas se manejan en el servidor, con `pnpm create-user`. No hay pantalla para hacerlo. Para cambiar una contraseña se usa `--reset`, que además cierra las sesiones abiertas de esa persona.

## Relacionado

- [[Base de datos SQLite]]
- [[Acceso, sesiones y seguridad]]
- [[Cómo correr el panel]]
- [[Panel MT]]
