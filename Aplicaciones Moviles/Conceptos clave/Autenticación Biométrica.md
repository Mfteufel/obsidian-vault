---
tags: [android, biometria, seguridad, autenticacion, androidx, desarrollo-aplicaciones-1]
aliases: [Biometría, AndroidX Biometric, BiometricPrompt, BiometricManager, PromptInfo, Huella digital]
---

# CC_Autenticación Biométrica (AndroidX Biometric)

> Librería única (`androidx.biometric`) que unifica el acceso a huella digital, reconocimiento facial y escaneo de iris desde **API 21+**, con una sola API consistente.

## Los 3 factores de autenticación

En cualquier sistema (no solo mobile), la autenticación se apoya en 3 factores clásicos:

| Factor | Tipo | Ejemplos | Ventaja | Desventaja |
|---|---|---|---|---|
| **Algo que sabés** | Knowledge factor | Password, PIN, patrón, respuesta de seguridad | Fácil de implementar | Se comparte, se olvida, se adivina |
| **Algo que tenés** | Possession factor | Celular con SMS, pasaporte | Hay que robar el objeto físico | Se puede perder o robar |
| **Algo que sos** | Inherence factor | Huella, rostro, iris, voz, tipeo | No se olvida ni se pierde | Si se filtra, no se puede "cambiar" |

> **MFA / 2FA:** combinar factores de **distinto tipo**. Dos passwords **no** son 2FA.

---

## Métodos de bloqueo y biometría en Android

### Algo que sabés — Métodos de bloqueo

| Método | Detalle | Seguridad |
|---|---|---|
| **PIN** | Mín. 4 dígitos, recomendable 6+ | Media |
| **Patrón** | Vulnerable a *smudge attacks* (rastro de dedos en la pantalla) | Baja/Media |
| **Password** | Solo si es robusta | Alta |

### Algo que sos — Biometría

| Método | Detalle |
|---|---|
| **Huella digital** | La más común y fiable en Android |
| **Reconocimiento facial** | Varía mucho según fabricante |
| **Escaneo de iris** | Algunos Samsung, poco común |

### Clasificación Android de sensores biométricos

| Clase | Nivel | Uso permitido |
|---|---|---|
| **Class 3** (antes *Strong*) | Más seguro | Permite operaciones criptográficas — es el que se usa para autenticar en apps |
| **Class 2** (antes *Weak*) | Medio | Solo desbloqueo de pantalla |
| **Class 1** (antes *Convenience*) | Bajo | **No se usa en apps** |

---

## Enrolamiento — el prerrequisito obligatorio

> ⚠️ Una app **no puede usar biometría** si el usuario no tiene **al menos un método enrolado** en el dispositivo (huella, cara, etc. configurados en Ajustes).

### ¿Qué hace la app si no hay enrolamiento?

| Opción | Detalle |
|---|---|
| **A — Deshabilitar el botón** | Ocultar o grisar el botón de "login con huella" y mostrar un mensaje explicativo |
| **B — Deep link a Ajustes** | Mandar al usuario directamente a enrolar |

```java
// Opción B
Intent i = new Intent(Settings.ACTION_BIOMETRIC_ENROLL);
startActivity(i);
```

---

## ¿Por qué AndroidX y no la API nativa?

La API biométrica de Android cambió varias veces con el tiempo:

| API | Versión | Estado |
|---|---|---|
| `FingerprintManager` | API 23 (Marshmallow) | Solo huella. **Hoy deprecado** |
| `BiometricPrompt` nativo | API 28 (Pie) | Más moderno, pero solo versiones nuevas |
| `androidx.biometric` | API 21+ | **Unifica todo. Es la recomendada** |

```gradle
// build.gradle
implementation "androidx.biometric:biometric:1.2.0-alpha05"
```

---

## Los 3 componentes principales

> El orden importa: **preguntar antes de construir, construir antes de lanzar.**

```
BiometricManager  →  PromptInfo  →  BiometricPrompt
"¿puedo usar          "¿cómo se ve       "autenticá
 biometría?"            el diálogo?"       ahora"
```

| Componente | Rol |
|---|---|
| **BiometricManager** | Consulta al sistema si hay hardware, si está disponible y si hay credenciales enroladas |
| **PromptInfo** | `Builder` con título, subtítulo, descripción y qué autenticadores permitir |
| **BiometricPrompt** | Dispara el prompt y recibe los callbacks (`succeeded` / `failed` / `error`) |

---

## Paso 1 — BiometricManager: preguntar primero

```java
BiometricManager manager = BiometricManager.from(context);

int canAuth = manager.canAuthenticate(
        Authenticators.BIOMETRIC_STRONG | Authenticators.DEVICE_CREDENTIAL);

switch (canAuth) {
    case BIOMETRIC_SUCCESS:
        // Todo OK, mostrar prompt
        break;
    case BiometricManager.BIOMETRIC_ERROR_NO_HARDWARE:
        // Sin sensor biométrico
        break;
    case BiometricManager.BIOMETRIC_ERROR_HW_UNAVAILABLE:
        // Sensor no disponible ahora
        break;
    case BiometricManager.BIOMETRIC_ERROR_NONE_ENROLLED:
        // Mandar a Ajustes (ver sección Enrolamiento)
        break;
}
```

| Estado | Significado |
|---|---|
| `BIOMETRIC_SUCCESS` | Todo OK, mostrar el prompt |
| `ERROR_NO_HARDWARE` | El dispositivo no tiene sensor biométrico |
| `ERROR_HW_UNAVAILABLE` | El sensor existe pero no está disponible ahora |
| `ERROR_NONE_ENROLLED` | Hay sensor, pero el usuario no enroló ningún método → ver [[#Enrolamiento — el prerrequisito obligatorio]] |

---

## Paso 2 — PromptInfo: configurar el diálogo

```java
PromptInfo info = new PromptInfo.Builder()
        .setTitle("RitmoFit")
        .setSubtitle("Ingresá con tu huella")
        .setDescription("Acceso rápido a tu cuenta")
        .setAllowedAuthenticators(
                Authenticators.BIOMETRIC_STRONG | Authenticators.DEVICE_CREDENTIAL)
        .build();
```

> ⚠️ **Trampa frecuente:** si usás `DEVICE_CREDENTIAL` (PIN/patrón como fallback), **NO** podés llamar a `.setNegativeButtonText()`. El botón de fallback ya es el PIN/patrón del dispositivo.
>
> Si es **solo biometría** (sin `DEVICE_CREDENTIAL`), ahí sí:
> ```java
> .setNegativeButtonText("Cancelar")
> ```

---

## Paso 3 — BiometricPrompt: disparar y escuchar

```java
Executor exec = ContextCompat.getMainExecutor(this);

BiometricPrompt prompt = new BiometricPrompt(this, exec,
    new BiometricPrompt.AuthenticationCallback() {

        @Override
        public void onAuthenticationSucceeded(BiometricPrompt.AuthenticationResult result) {
            irAHome();
        }

        @Override
        public void onAuthenticationError(int errorCode, CharSequence errString) {
            // Error del sistema o cancelación del usuario
        }

        @Override
        public void onAuthenticationFailed() {
            // Intento no coincide. El prompt sigue abierto
        }
    });

prompt.authenticate(info);
```

### Los 3 callbacks — entran en el parcial

| Callback | Cuándo se dispara |
|---|---|
| **`onAuthenticationSucceeded`** ✓ | Biometría validada correctamente |
| **`onAuthenticationFailed`** ↻ | El intento no coincide (ej: dedo equivocado). El prompt **sigue abierto** — el usuario puede reintentar |
| **`onAuthenticationError`** ✕ | Error del sistema **o** cancelación del usuario. El prompt se cierra |

> La diferencia clave entre `Failed` y `Error`: `Failed` es un intento fallido pero recuperable (dedo mal puesto); `Error` termina el flujo (usuario canceló, hardware falló, demasiados intentos).

---

## Mismo patrón que ActivityResultLauncher

```
Declarás el prompt y el callback → lo lanzás → el callback resuelve
```

Es el mismo patrón mental que [[ActivityResultLauncher]]: registrar antes, lanzar cuando corresponde, resolver en el callback de forma asíncrona.

---

## Flujo completo end-to-end

```
1. BiometricManager.canAuthenticate()
        │
        ├── ERROR_NONE_ENROLLED ──► Deep link a Settings.ACTION_BIOMETRIC_ENROLL
        │
        └── BIOMETRIC_SUCCESS
                │
                ▼
2. Construir PromptInfo (título, subtítulo, autenticadores permitidos)
                │
                ▼
3. new BiometricPrompt(...).authenticate(info)
                │
        ┌───────┼────────┐
        ▼       ▼        ▼
   Succeeded  Failed   Error
    (login)  (reintentar) (cancelar/mostrar error)
```

---

## Lo que hay que llevarse (resumen de la clase)

| # | Idea clave |
|---|---|
| 1 | `ActivityResultLauncher` — contratos + callback tipado |
| 2 | Tres factores: algo que sabés, tenés, sos. MFA = combinar factores de **distinto tipo** |
| 3 | **Sin enrolamiento no hay biometría.** Siempre preguntar con `BiometricManager` antes |
| 4 | El trío: `BiometricManager` → `PromptInfo` → `BiometricPrompt`. Tres callbacks: `succeeded` / `failed` / `error` |

---

## Relacionado

- [[Clase 06 - Biometría y Seguridad en el Dispositivo]]
- [[ActivityResultLauncher]]
- [[EncryptedSharedPreferences]]
- [[JWT]]
- [[AndroidManifest]]
