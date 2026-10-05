---
tags: [clase, android, biometria, seguridad, activityresultlauncher, desarrollo-aplicaciones-1]
---

# Clase 06 — Biometría y Seguridad en el Dispositivo

**Materia:** Desarrollo de Aplicaciones 1 — 2C 2026

> *ActivityResultLauncher · Factores de autenticación · AndroidX Biometric*

---

## Hoja de ruta

| # | Bloque | Contenido |
|---|---|---|
| 01 | Repaso: [[ActivityResultLauncher]] | El patrón moderno para lanzar acciones y recibir resultados del sistema |
| 02 | Seguridad en el dispositivo | Los tres factores de autenticación, métodos de bloqueo y biometría en Android |
| 03 | [[Autenticación Biométrica\|AndroidX Biometric]] | `BiometricManager`, `PromptInfo` y `BiometricPrompt` en detalle |

---

## Bloque 01 · [[ActivityResultLauncher|El patrón: delegar al sistema y esperar la respuesta]]

**La idea mental:** lanzás una acción → el SO toma el control → recibís un callback con el resultado.

### Las 3 piezas que siempre aparecen juntas

| # | Pieza | Qué es |
|---|---|---|
| 1 | El contrato | `ActivityResultContract` — define QUÉ se lanza y qué tipo de resultado devuelve |
| 2 | El callback | El código que se ejecuta cuando el SO devuelve el resultado |
| 3 | El launcher | `ActivityResultLauncher` — el objeto que dispara la acción con `.launch(...)` |

### Ejemplo — abrir la galería

```java
// LoginActivity.java
private ActivityResultLauncher<String> pickImageLauncher =
    registerForActivityResult(                 // contrato
        new ActivityResultContracts.GetContent(),
        uri -> {                                // callback
            if (uri != null) {
                imageView.setImageURI(uri);
            }
        }
    );

// Para lanzarlo desde un click:
pickImageLauncher.launch("image/*");
```

### Contratos más usados

| Contrato | ¿Para qué sirve? | Input | Output |
|---|---|---|---|
| `GetContent` | Elegir un archivo desde la galería o archivos | MIME type `"image/*"` | `Uri` del archivo |
| `RequestPermission` | Pedir un permiso en runtime | Nombre del permiso | `boolean` (concedido) |
| `RequestMultiplePermissions` | Pedir varios permisos juntos | `String[]` | `Map<String, Boolean>` |
| `StartActivityForResult` | Genérico, cuando no hay un contrato específico | `Intent` | `ActivityResult` |

> 💡 El launcher debe registrarse **antes de STARTED**. Siempre como campo de clase o en `onCreate()`.

### Cuatro cosas para no olvidar

1. **Registrar antes de STARTED** — registrarlo en un `onClick` tira excepción
2. **El callback es asíncrono** — no corre en la línea siguiente al `.launch()`, corre cuando el usuario termina
3. **Mismo patrón que Biometric** — declarás el prompt y el callback → lo lanzás → el callback resuelve
4. Desarrollo completo con más ejemplos: ver [[ActivityResultLauncher]]

---

## Bloque 02 — Seguridad en el Dispositivo

### Los tres factores de autenticación

En cualquier sistema (no solo mobile), la autenticación se apoya en 3 factores clásicos:

| Factor | Ejemplos | Ventaja | Desventaja |
|---|---|---|---|
| **Algo que sabés** (Knowledge) | Password, PIN, patrón, respuesta de seguridad | Fácil de implementar | Se comparte, se olvida, se adivina |
| **Algo que tenés** (Possession) | Celular con SMS, pasaporte | Hay que robar el objeto físico | Se puede perder o robar |
| **Algo que sos** (Inherence) | Huella, rostro, iris, voz, tipeo | No se olvida ni se pierde | Si se filtra, no se puede "cambiar" |

> **MFA / 2FA:** combinar factores **de distinto tipo**. Dos passwords no son 2FA.

### Métodos de bloqueo y biometría en Android

| Algo que sabés | Seguridad | Algo que sos |
|---|---|---|
| PIN (mín. 4 dígitos, recomendable 6+) | Media | Huella digital — la más común y fiable |
| Patrón (vulnerable a *smudge attacks*) | Baja/Media | Reconocimiento facial — varía según fabricante |
| Password (solo si es robusta) | Alta | Escaneo de iris — poco común |

**Clasificación Android:** Class 3 (STRONG, permite cripto) · Class 2 (WEAK, solo desbloqueo) · Class 1 (no se usa en apps)

### Enrolamiento

> ⚠️ Una app **no puede usar biometría** si el usuario no tiene al menos un método enrolado en el dispositivo.

| Opción si no hay enrolamiento | Detalle |
|---|---|
| A — Deshabilitar el botón | Ocultar/grisar el botón de login con huella + mensaje explicativo |
| B — Deep link a Ajustes | `new Intent(Settings.ACTION_BIOMETRIC_ENROLL); startActivity(i);` |

> Desarrollo completo: [[Autenticación Biométrica#Enrolamiento — el prerrequisito obligatorio]]

---

## Bloque 03 · [[Autenticación Biométrica|AndroidX Biometric]]

### ¿Por qué AndroidX y no la API nativa?

| API | Versión | Estado |
|---|---|---|
| `FingerprintManager` | API 23 (Marshmallow) | Solo huella. Hoy deprecado |
| `BiometricPrompt` nativo | API 28 (Pie) | Más moderno, pero solo versiones nuevas |
| `androidx.biometric` | API 21+ | **Unifica todo. Es la recomendada** |

```gradle
implementation "androidx.biometric:biometric:1.2.0-alpha05"
```

### Los tres componentes principales

> El orden importa: **preguntar antes de construir, construir antes de lanzar.**

| Componente | Rol |
|---|---|
| `BiometricManager` | ¿Puedo usar biometría? Consulta hardware, disponibilidad y enrolamiento |
| `PromptInfo` | ¿Cómo se ve el diálogo? `Builder` con título, subtítulo, descripción y autenticadores permitidos |
| `BiometricPrompt` | Autenticá ahora. Dispara el prompt y recibe los callbacks |

### Paso 1 — Preguntar con BiometricManager

```java
BiometricManager manager = BiometricManager.from(context);
int canAuth = manager.canAuthenticate(
        Authenticators.BIOMETRIC_STRONG | Authenticators.DEVICE_CREDENTIAL);
```

| Estado | Significado |
|---|---|
| `BIOMETRIC_SUCCESS` | Todo OK, mostrar prompt |
| `ERROR_NO_HARDWARE` | Sin sensor biométrico |
| `ERROR_HW_UNAVAILABLE` | Sensor no disponible ahora |
| `ERROR_NONE_ENROLLED` | Mandar a Ajustes |

### Paso 2 — Configurar el diálogo con PromptInfo

```java
PromptInfo info = new PromptInfo.Builder()
        .setTitle("RitmoFit")
        .setSubtitle("Ingresá con tu huella")
        .setDescription("Acceso rápido a tu cuenta")
        .setAllowedAuthenticators(
                Authenticators.BIOMETRIC_STRONG | Authenticators.DEVICE_CREDENTIAL)
        .build();
```

> ⚠️ **Trampa frecuente:** con `DEVICE_CREDENTIAL` **NO** se puede llamar a `.setNegativeButtonText()` — el botón de fallback ya es el PIN/patrón del dispositivo.

### Paso 3 — Disparar y escuchar con BiometricPrompt

```java
Executor exec = ContextCompat.getMainExecutor(this);
BiometricPrompt prompt = new BiometricPrompt(this, exec, new AuthenticationCallback() {
    @Override
    public void onAuthenticationSucceeded(result) {
        irAHome();
    }
    public void onAuthenticationError(code, msg) { ... }
    public void onAuthenticationFailed() { ... }
});

prompt.authenticate(info);
```

**Los tres callbacks — entran en el parcial:**

| Callback | Cuándo se dispara |
|---|---|
| ✓ Succeeded | Biometría validada correctamente |
| ↻ Failed | Intento no coincide. El prompt sigue abierto |
| ✕ Error | Error del sistema o cancelación del usuario |

> Desarrollo completo con código y diagrama del flujo: [[Autenticación Biométrica]]

---

## Lo que nos llevamos

| # | Idea |
|---|---|
| 01 | `ActivityResultLauncher`. Contratos + callback tipado |
| 02 | Tres factores: algo que sabés, tenés, sos. MFA = combinar factores de distinto tipo |
| 03 | Sin enrolamiento no hay biometría. Siempre preguntar con `BiometricManager` antes |
| 04 | El trío: `BiometricManager` → `PromptInfo` → `BiometricPrompt`. Tres callbacks: succeeded / failed / error |

---

## Relacionado

- [[Clase 05 - JWT y LocalStorage]]
- [[ActivityResultLauncher]]
- [[Autenticación Biométrica]]
- [[EncryptedSharedPreferences]]
- [[AndroidManifest]]
