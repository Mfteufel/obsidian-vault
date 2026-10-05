---
tags: [git, github, workflow, repo-profesor, desarrollo-aplicaciones-1]
aliases: [Repo del profe, Traer rama nueva, Sync repo profesor, practices-android-uade]
---

# CC_Traer una rama nueva del repo del profe

> Cada clase, Horacio sube una rama nueva con el código visto en clase al repo [practices-android-uade](https://github.com/Horix89/practices-android-uade). Esto es lo que hay que hacer para traerla a tu compu.

## Dónde está guardado

```
Documentos/UADE/2C 2026/Desarrollo de app/Repos clases/practices-android-uade
```

---

## Paso a paso

### 1. Entrar a la carpeta del repo

```bash
cd "Documentos/UADE/2C 2026/Desarrollo de app/Repos clases/practices-android-uade"
```

### 2. Traer la info actualizada de GitHub

```bash
git fetch origin
```

> `fetch` **no cambia nada** en tu carpeta. Solo le avisa a Git qué ramas nuevas existen en GitHub.

### 3. Ver qué ramas hay disponibles

```bash
git branch -r
```

Vas a ver algo así — la rama nueva aparece en la lista:

```
  origin/feature/navegacion-intent
  origin/feature/navigation-component
  origin/feature/primeros-pasos
  origin/feature/retrofit
  origin/feature/room-database   ← la nueva, por ejemplo
```

### 4. Traerla a tu compu

```bash
git checkout -b feature/room-database origin/feature/room-database
```

> Cambiá `feature/room-database` por el nombre real que veas en el paso 3.

### 5. Volver a main (opcional)

```bash
git checkout main
```

---

## Traer varias ramas nuevas de una

Si aparecieron varias y querés traerlas todas juntas:

```bash
for b in feature/rama-1 feature/rama-2 feature/rama-3; do
  git checkout -b "$b" "origin/$b"
done
git checkout main
```

> Reemplazá `feature/rama-1`, etc. por los nombres reales que salieron en `git branch -r`.

---

## Cómo confirmar que la tenés

```bash
git branch
```

La rama nueva tiene que aparecer en la lista **sin** el prefijo `origin/` — eso significa que ya está local.

---

## Para ver el código en Android Studio

```bash
git checkout feature/room-database
```

Y abrís la carpeta `practices-android-uade` desde Android Studio. Sincroniza Gradle solo.

---

## Ojo con esto

- ⚠️ Este repo es **solo de referencia** — nunca mezclarlo con el repo del grupo del TPO
- Si Android Studio ya tenía el proyecto abierto en otra rama, puede pedir **Sync Now** al cambiar — dejalo sincronizar antes de tocar código
- Si `git checkout -b` tira error de que la rama **ya existe**, usá `git checkout feature/nombre` sin el `-b` — ya la tenías de antes

---

## Comandos de rescate

| Situación | Comando |
|---|---|
| Ver en qué rama estoy | `git branch` |
| Ver todas las remotas | `git branch -r` |
| Volver a main | `git checkout main` |
| Traer cambios nuevos de una rama que ya tenías | `git pull origin nombre-rama` |

---

## Relacionado

- [[Git Flow - Ramas y Pull Requests]]
- [[Android Studio]]
