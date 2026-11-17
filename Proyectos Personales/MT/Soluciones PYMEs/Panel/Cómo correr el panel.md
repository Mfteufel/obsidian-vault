---
tags: [mt-panel, operacion]
fuente:
  - "README.md"
  - "package.json"
  - "scripts/create-user.ts"
  - "src/lib/demo.ts"
---

# Cómo correr el panel

**Para qué:** levantar el panel en una computadora para probarlo, revisar cambios o mostrarlo al equipo.

## Lo que hace falta

- **Node** y **pnpm**. La versión de pnpm está fijada en `package.json`, así que conviene usar corepack. El repo no fija una versión de Node.
- La base se crea sola. No hace falta instalar ningún servidor de base de datos.

## Pasos

1. Instalar las dependencias con `pnpm install`.
2. Crear un usuario con `pnpm create-user`. Pide el email y la contraseña dos veces. Para automatizarlo se pueden usar las variables `MT_USER_EMAIL` y `MT_USER_PASSWORD`.
3. Arrancar el panel con `pnpm dev`. Queda en http://localhost:3000.
4. Entrar a `/login` con ese usuario.
5. En Inicio, si la base no tiene clientes, aparece un botón para cargar datos de ejemplo. Si ya hay clientes, no hace nada.
6. En Guardias, cargar al equipo y completar la rotación.

## Comandos

| Comando | Qué hace |
|---|---|
| `pnpm dev` | Modo desarrollo, en http://localhost:3000 |
| `pnpm build` | Genera la versión de producción |
| `pnpm start` | Corre la versión de producción |
| `pnpm lint` | Revisa el estilo del código |
| `pnpm test` | Corre las pruebas una vez |
| `pnpm test:watch` | Corre las pruebas cada vez que se guarda un archivo |
| `pnpm create-user` | Crea un usuario. Con `--reset` cambia la contraseña de uno existente |
| `pnpm check:contrast` | Verifica la paleta y los contrastes |

## Dónde quedan los datos

En `data/mt.db`, creada automáticamente. Las pruebas usan una base en memoria y nunca tocan ese archivo.

## Relacionado

- [[Pruebas automáticas]]
- [[Acceso, sesiones y seguridad]]
- [[Tablero de Inicio]]
- [[Publicación y copias de seguridad]]
- [[Panel MT]]
