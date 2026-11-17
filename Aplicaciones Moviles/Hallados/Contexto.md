---
tags: [hallado, react-native, expo, tp, contexto]
aliases: [Hallado, Contexto Hallado]
---

# Contexto: TP Hallado

> Nota para que Claude Code retome el trabajo sin repetir. Última actualización: 2026-10-08.

## Qué es
- **Hallado**: app de objetos y mascotas perdidas (TP de UADE, Desarrollo de Aplicaciones).
- Repo: `~/Documentos/UADE/2026/2C 2026/Desarrollo de app/Uade---ReactNative`
- GitHub: https://github.com/Mfteufel/Uade---ReactNative
- Equipo: Marco Federico Teufel, Walter Berrutto, Lucas Villarreal, Damian Dennin, Elliff Juan Cruz.

## Reglas del TP (obligatorias)
- **JavaScript solamente.** Nada de TypeScript (`.ts`, `.tsx`, `tsconfig`).
- React Native + Expo, 100% en **Android**.
- **Sin backend** por ahora: la app usa una **API simulada (mock)**.
- Navegación con React Navigation. Sesión con `AuthContext`.
- Las pantallas **nunca** tocan datos directo: siempre llaman a `src/api/`.

## Cómo prefiere trabajar el usuario
- Frases cortas y listas. Nada de bloques largos.
- Trabajar **por fases** y **frenar al terminar cada una**, diciendo cómo probarla.
- Código simple, es un TP (sin sobre-ingeniería).
- **Preguntar antes de borrar** archivos.
- No crear el PR hasta que lo pida (ya lo pidió y se creó).
- Los commits llevan la línea `Co-Authored-By: Claude Sonnet 5.5 <noreply@anthropic.com>`.

## Estado: puntos 1 y 2 hechos
Alcance de esta etapa: **1) Autenticación** y **2) Perfil y reputación**.

Las 7 fases están completas y el usuario probó 1 a 6 en el celular.

| Fase | Contenido | Estado |
|---|---|---|
| 1 | Setup Expo, navegación, carpetas, `config.js` | Hecha |
| 2 | Capa de API mock (`client.js`, `authApi.js`, `userApi.js`) | Hecha |
| 3 | Registro (email, OTP, contraseña), login, recupero, `AuthContext` | Hecha |
| 4 | Biometría (switch en Configuración + pantalla de bloqueo) | Hecha |
| 5 | Perfil: ver, editar, foto, privacidad del teléfono | Hecha |
| 6 | Reputación, insignias, perfil público | Hecha |
| 7 | `README.md` y `API_CONTRACT.md` | Hecha |
| Extra | Logo e ícono adaptativo de Android | Hecho |

## Git y PR
- Rama: `feature/auth-perfil` (sale de `main`).
- **PR #2 abierto**: https://github.com/Mfteufel/Uade---ReactNative/pull/2
- **Falta que el equipo lo apruebe.** No está mergeado.
- Commits de la rama:
  1. `3ed9ae4` Add auth, profile and reputation with mock API
  2. `d9a418c` Add Hallado app icon and adaptive icon
  3. `6b9a4bd` Document USB install of the native Android build
- Antes (ya mergeado): PR #1 `feature/scaffold-expo`.
- No tocar la rama mientras el PR espera revisión. Si piden cambios, se arreglan ahí.

## Estructura del proyecto
```
App.js
src/
  config.js          USE_MOCK, API_URL, REQUEST_TIMEOUT_MS
  api/
    client.js        fetch con timeout; reintentos solo en GET
    authApi.js       8 funciones de /auth/*
    userApi.js       getMe, updateMe, uploadPhoto, getPublicProfile, getReputation
    errors.js        ApiError {status, message}, getErrorMessage()
    tokenStore.js    access token en memoria
    mock/            db.js, seed.js, mockAuth.js, mockUsers.js, reputation.js
  context/AuthContext.js
  navigation/AppNavigator.js
  screens/ auth/ profile/ settings/
  components/        Button, Input, FormScreen, Avatar, ReputationCard, BadgeList
  constants/         theme, badges, sampleUsers
  hooks/useCountdown.js
  utils/             validators, biometrics, canShowPhone, devOtp, memberSince
```

## Cómo funciona el mock
- Cada función de `authApi.js` / `userApi.js` mira `USE_MOCK`: `true` va a `src/api/mock/`, `false` va a `client.js`.
- Datos guardados en **AsyncStorage** (clave `hallado:mock:db`). Simula demora de 300 a 800 ms.
- Errores con formato `{ status, message }`.
- Tokens falsos: `mock-access.<userId>.<rand>` y `mock-refresh.<userId>.<rand>`.
- Para pasar al backend real: `USE_MOCK = false`, ajustar `API_URL`, borrar `src/api/mock/` y `src/constants/sampleUsers.js`.

### OTP simulado
- 6 dígitos, vence a los **10 min**, máximo **5 intentos** fallidos, un solo uso.
- Pedir uno nuevo invalida el anterior. Mínimo **60 s** entre envíos (429 si antes).
- El código se muestra en un Alert y en consola. El campo `devCode` es **solo del mock**; el backend real no debe devolverlo.

### Usuarios de ejemplo (contraseña de todos: `hallado123`)
| Email | Devoluciones | Valoraciones |
|---|---|---|
| lucia@hallado.com | 12 | 18+ / 1− |
| martin@hallado.com | 5 | 6+ |
| carlos@hallado.com | 2 | 1+ / 3− |
| sofia@hallado.com | 0 | ninguna |

## Decisiones tomadas
- **Reputación**: `puntos = devoluciones×10 + positivas×2 − negativas×5` (mínimo 0). También `positivePercent`.
- **Insignias** en `src/constants/badges.js` (`metric` + `goal`): Primer paso (1), **Vecino solidario (5 devoluciones, obligatoria)**, Guardián del barrio (10), Muy valorado (10 positivas). Las calcula la app, no el back.
- **Sesión**: refresh token en `expo-secure-store`. Al abrir la app se renueva solo.
- **Biometría**: flag en SecureStore. Si está activa, al abrir aparece `LockScreen` y pide huella; con éxito renueva la sesión. Se borra al cerrar sesión.
- **Recupero de contraseña**: `/auth/password/reset` verifica código y cambia la clave en un solo paso (`{email, code, newPassword}`).
- **Nombre inicial** al registrarse: parte del email antes de la `@`. Se edita en el perfil.
- **Perfil público**: `getPublicProfile` NO devuelve email, teléfono ni historial.
- **Privacidad del teléfono**: `phoneVisibleOnClaim` (se cambia en Configuración). `canShowPhone(user, claim)` está lista para cuando existan los reclamos (`claim.status === 'accepted'`).
- Recupero con email inexistente responde 404 (más fácil de probar; en el back real se podría cambiar).

## Documentación en el repo
- `README.md`: cómo correrla en Android (Expo Go, emulador, USB), estructura, pasar a API real.
- `API_CONTRACT.md`: los 13 endpoints con método, ruta, body, respuesta y errores. Sirve para armar el backend.

## Entorno del usuario (Linux) y trampas
- **`npm` no está en el PATH** de zsh. Cargar antes: `source ~/.nvm/nvm.sh` (Node 22.21). Sugerido agregarlo a `~/.zshrc`.
- **Para builds nativos usar JDK 17** (el sistema tiene JDK 25 y el build falla en CMake):
  ```bash
  export JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64
  export ANDROID_HOME=$HOME/Android/Sdk
  export PATH=$JAVA_HOME/bin:$PATH:$ANDROID_HOME/platform-tools
  npx expo run:android
  ```
- **Puerto 8081 ocupado** por un Apache (Docker). Solución usada: `npx expo start --port 8082` y `adb reverse tcp:8081 tcp:8082`.
- Celular de pruebas: Pixel 10 Pro (serial `63140DLCH001V2`), por USB.
- Expo Go y la app instalada "Hallado" (`com.uade.hallado`) tienen **almacenamiento separado**: los usuarios del mock no se comparten.
- Expo Go muestra siempre su propio ícono; el ícono real solo se ve con el build nativo.
- La carpeta `android/` la genera Expo y está en el `.gitignore`. No hay código Kotlin/Java propio en el repo.
- `expo run:android` modifica los scripts de `package.json`; se revirtió para no cambiar `npm run android`.
- Íconos viejos de Expo borrados. Quedó solo `favicon.png` (web).

## Pendientes y siguientes pasos
- [ ] Que el equipo apruebe y mergee el PR #2.
- [ ] Tras el merge: `git checkout main && git pull`.
- [ ] Los **reclamos** todavía no existen (usarán `canShowPhone`).
- [ ] Backend real (implementar `API_CONTRACT.md`).
- [ ] La app no renueva el token si vence con la app abierta (solo al abrirla).
- [ ] La foto de la cámara queda en caché de Android; con backend se sube al servidor (opcional: copiar con `expo-file-system`).
- [ ] Siguientes puntos del TP (publicaciones, etc.): el usuario los va a indicar.

## Relacionado
- [[Expo]], [[React Native]], [[React Navigation]], [[JWT]], [[Git Flow - Ramas y Pull Requests]]
