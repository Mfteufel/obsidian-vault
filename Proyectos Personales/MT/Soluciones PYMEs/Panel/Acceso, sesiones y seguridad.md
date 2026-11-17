---
tags: [mt-panel, seguridad]
fuente:
  - "README.md"
  - "src/lib/auth.ts"
  - "src/lib/login.ts"
  - "src/lib/password.ts"
  - "src/lib/auth-actions.ts"
  - "src/proxy.ts"
  - "scripts/create-user.ts"
  - "src/app/(panel)/layout.tsx"
---

# Acceso, sesiones y seguridad

**Para qué:** que solo entre el equipo de MT y que nadie pueda adivinar una contraseña probando muchas veces.

## Quién puede entrar

No hay registro público. Los usuarios (hoy, 4 personas del equipo) se crean desde la terminal con `pnpm create-user`. Para cambiar una contraseña se usa `pnpm create-user --reset`, que además cierra sus sesiones abiertas.

## Contraseñas

- Mínimo 10 caracteres y máximo 72 bytes (el límite del algoritmo).
- Se guardan con bcrypt; nunca en texto plano.
- El email no distingue mayúsculas de minúsculas.

## Sesiones

- Duran 7 días.
- La cookie `mt_session` no es accesible desde JavaScript (`httpOnly`), no viaja en pedidos de otros sitios (`sameSite=lax`) y se marca `secure` en producción.
- La base guarda solo un hash del token. Si alguien leyera el archivo de la base, no podría usar las sesiones.
- Cerrar sesión borra la sesión de la base, así que la cookie deja de servir.

## Límite de intentos

- Cinco fallos en 15 minutos bloquean el ingreso, tanto para ese email como para esa IP.
- El mensaje es el mismo si el email no existe y si la contraseña está mal, y el tiempo de respuesta es parecido en los dos casos. Así no se puede averiguar qué emails están registrados.
- La IP se toma del encabezado que pone el proxy. Si no hay proxy que la informe, el límite funciona solo por email.

## Tres barreras

1. `proxy.ts` mira solo si existe la cookie. No toca la base.
2. El layout del panel verifica la sesión de verdad.
3. Cada lectura y cada acción del servidor vuelve a verificarla.

Así, aunque alguien saltee una pantalla, las lecturas y los cambios siguen protegidos.

## Relacionado

- [[Lecturas y acciones del servidor]]
- [[Publicación y copias de seguridad]]
- [[Base de datos SQLite]]
- [[Panel MT]]
