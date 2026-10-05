---
tags: [tpo, tp, ronda, android, react-native, api-rest, desarrollo-aplicaciones-1]
aliases: [TPO Ronda, TP Ronda, Ronda, Compra y Venta entre Personas]
---
![[TPO_Ronda_Android.pdf]]
# TPO — Ronda (Compra y Venta entre Personas)

**Materia:** Desarrollo de Aplicaciones 1 — 2C 2026
**Profesor:** Horacio Brizuela
**Grupo:** 5 personas

---

## El contexto en 4 líneas

- **Ronda** ya tiene un sistema interno funcionando (usuarios, categorías, moderación, reputación)
- Nos piden una **app móvil que lo complemente**
- Objetivo del usuario: publicar lo que no usa, negociar precio y coordinar entrega en mano
- La app se **sincroniza** con el sistema interno para moderación y confianza

---

## Qué hay que construir

| Parte | Detalle |
|---|---|
| **API REST** | Backend con toda la información y funcionalidades que consume la app |
| **App móvil** | Android Nativo (Java) **y** React Native con Expo |

---

## Funcionalidades

### 1. Autenticación y Registro

- Pedir **email** → enviar código de validación (**OTP**) → confirmar → crear sesión
- **Recupero de acceso:** reenviar el código si no llegó o si expiró
- **Login** con usuario y contraseña, con OTP como alternativa

### 3. Explorar Publicaciones (Home)

| Feature | Detalle |
|---|---|
| **Listado paginado** | Título · precio · estado del artículo · zona del vendedor |
| *(Opcional)* | Foto del artículo |
| **Buscador** | Texto libre sobre **título y descripción** |
| **Filtros combinados** | Categoría · rango de precio · estado · cercanía a la zona del usuario |
| **Ordenamiento** | Más recientes · menor precio · mayor precio |

**Estados del artículo:** nuevo · como nuevo · usado

> ⚠️ En el PDF el punto **2 no aparece** (salta del 1 al 3). Falta consultarlo con el profesor.

---

## Entregables

| Entrega | Tecnología | Alcance |
|---|---|---|
| **1ra** | **Android Nativo (Java)** | Backend de los puntos indicados + app 100% funcional |
| **2da** | **React Native con Expo** (Android) | Backend de los puntos indicados + app 100% funcional |
| **Final** | **Una de las dos** (a elección) | 100% de los casos de uso descritos |

> ⚠️ El PDF dice "los puntos **X**" en 1ra y 2da entrega — el alcance concreto se informa por cronograma.

---

## Cómo se evalúa

| Criterio | Detalle |
|---|---|
| **Funcionamiento** | Casos de uso completos según cronograma |
| **Aporte individual** | Cantidad y calidad de **PRs mergeadas** por integrante |
| **1ra y 2da entrega** | Video de **15 min máximo** |
| **Entrega final** | **Coloquio oral** (grupal + individual) |

**Reglas de Git obligatorias:**

- Ramas con prefijo `feature/` o `fix/`
- A `main` **solo por Pull Request** — nada de push directo
- **Mínimo 1 PR mergeada por integrante por entrega**

> Detalle en [[Git Flow - Ramas y Pull Requests]]

---

## Notas mínimas

| Instancia | Nota necesaria |
|---|---|
| Entregas parciales | 4 o más (cada una) |
| Final para aprobar | 4 a 7 |
| Final para promocionar | 8 o más |

---

## Stack a definir

| Capa | Opciones |
|---|---|
| **App (1ra entrega)** | Android Studio + Java |
| **App (2da entrega)** | React Native + Expo |
| **API REST** | A definir por el grupo |
| **Auth** | OTP por email + usuario/contraseña |
| **Repo** | GitHub con flujo de ramas y PRs |

---

## Próximos pasos

- [x] Armar el grupo de 5
- [ ] Crear el repo con reglas de protección en `main`
- [ ] Consultar el punto 2 faltante y el alcance de "puntos X"
- [ ] Definir stack del backend y modelo de datos
- [ ] Diseñar endpoints de la API REST
- [ ] Repartir funcionalidades por integrante (1 rama = 1 feature)

---

## Endpoints tentativos

```
POST   /auth/register          → email → dispara OTP
POST   /auth/verify-otp        → confirma código y crea sesión
POST   /auth/resend-otp        → reenvía el código
POST   /auth/login             → usuario + contraseña

GET    /publicaciones          → listado paginado
       ?page=1&size=20
       &q=texto
       &categoria=X
       &precioMin=0&precioMax=5000
       &estado=nuevo
       &orden=recientes|precioAsc|precioDesc
GET    /publicaciones/{id}     → detalle
POST   /publicaciones          → crear publicación
```

> Borrador propio, no está en la consigna. Ajustar cuando se defina el backend.

---

## Relacionado

- [[Clase 01 - Presentación de la materia]]
- [[Clase 02 - Android Primeros Pasos]]
- [[Git Flow - Ramas y Pull Requests]]
- [[Android Studio]]
- [[Actividad]]
- [[Intents]]
- [[ListView y Adapters]]
- [[Aplicaciones Nativas]]
- [[Aplicaciones Hibridas]]
