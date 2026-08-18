---
tags: [git, github, workflow, tpo, desarrollo-aplicaciones-1]
aliases: [Git Flow, Feature Branch, Pull Request, PR, Branching]
---

# CC_Git Flow — Ramas y Pull Requests

> Flujo de trabajo obligatorio del TPO: cada funcionalidad vive en su propia rama y llega a `main` solo por Pull Request.

## Por qué importa en la materia

- La nota **individual** sale de las PRs mergeadas a tu nombre
- Cada integrante necesita **mínimo 1 PR mergeada por entrega**
- `main` queda siempre estable y desplegable

---

## Convención de nombres de rama

| Prefijo | Cuándo se usa | Ejemplo |
|---|---|---|
| `feature/` | Funcionalidad nueva | `feature/home` |
| `fix/` | Corrección de errores | `fix/errores-primeraentrega` |

**Tips:**

- Todo en minúsculas, con guiones
- Nombre corto pero descriptivo
- Una rama = una funcionalidad

---

## Flujo paso a paso

```
main ────●────────────────●──── (solo recibe merges por PR)
          \              /
feature/   ●──●──●──●──●   ← acá trabajás
```

### 1. Traer lo último de main

```bash
git checkout main
git pull origin main
```

### 2. Crear tu rama

```bash
git checkout -b feature/login
```

### 3. Trabajar y commitear

```bash
git add .
git commit -m "feat: pantalla de login con validación"
```

### 4. Subir la rama

```bash
git push -u origin feature/login
```

### 5. Abrir el Pull Request

- Desde GitHub → **Compare & pull request**
- Base: `main` · Compare: `feature/login`
- Describir **qué hace** y **qué probar**
- Pedir review a un compañero

### 6. Después del merge

```bash
git checkout main
git pull origin main
git branch -d feature/login
```

---

## Comandos de rescate

| Situación | Comando |
|---|---|
| Ver en qué rama estoy | `git branch` |
| Cambiar de rama | `git checkout nombre-rama` |
| Ver estado de archivos | `git status` |
| Traer cambios de main a mi rama | `git merge main` |
| Deshacer cambios sin commitear | `git restore archivo.txt` |
| Guardar cambios temporalmente | `git stash` / `git stash pop` |

---

## Mensajes de commit (convención sugerida)

```
feat:     funcionalidad nueva
fix:      corrección de bug
refactor: reescritura sin cambiar comportamiento
docs:     documentación
style:    formato, sin cambio de lógica
```

**Ejemplo:**

```bash
git commit -m "fix: corrige crash al girar la pantalla en HomeActivity"
```

---

## Errores comunes

| Error | Consecuencia |
|---|---|
| Pushear directo a `main` | Está **prohibido** en el TPO |
| Trabajar todos en la misma rama | Conflictos constantes |
| Una sola PR gigante al final | No se puede evaluar tu aporte |
| No hacer `pull` antes de arrancar | Conflictos al mergear |

---

## Resolver un conflicto

```bash
git checkout feature/login
git merge main
# Git marca los conflictos en los archivos:
# <<<<<<< HEAD
# tu código
# =======
# código de main
# >>>>>>> main
```

- Editás el archivo dejando la versión correcta
- Borrás las marcas `<<<<`, `====`, `>>>>`
- Después: `git add .` y `git commit`

---

## Relacionado

- [[Clase 01 - Presentación de la materia]]
- [[Android Studio]]
