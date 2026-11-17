---
tags: [react-native, expo, biometria, seguridad, autenticacion, desarrollo-aplicaciones-1]
aliases: [expo-local-authentication, Biometría React Native, LocalAuthentication]
---

# CC_Biometría en React Native (expo-local-authentication)

> Módulo del [[Expo#Expo SDK|Expo SDK]] para autenticar con huella digital, reconocimiento facial o escaneo de iris. Es el equivalente en React Native de `androidx.biometric` en Android nativo.

## Instalación y tipos soportados

```bash
npx expo install expo-local-authentication
```

| Tipo | Constante |
|---|---|
| Huella digital | `FINGERPRINT` |
| Reconocimiento facial | `FACIAL_RECOGNITION` |
| Escaneo de iris | `IRIS` |

---

## Verificando disponibilidad biométrica

Igual que en Android nativo, **antes de lanzar el prompt hay que verificar** que el dispositivo soporte biometría. Son 3 checks:

```javascript
import * as LocalAuthentication from 'expo-local-authentication';

async function checkBiometrics() {
    // 1. ¿Tiene hardware biométrico?
    const hasHardware = await LocalAuthentication.hasHardwareAsync();

    // 2. ¿El usuario tiene biometría enrolada?
    const isEnrolled = await LocalAuthentication.isEnrolledAsync();

    // 3. ¿Qué tipos soporta?
    const types = await LocalAuthentication.supportedAuthenticationTypesAsync();
    // types puede ser: [1] Fingerprint, [2] FaceID, [3] Iris

    return { hasHardware, isEnrolled, types };
}
```

| Función | Verifica |
|---|---|
| `hasHardwareAsync()` | Si el dispositivo tiene sensor biométrico físico |
| `isEnrolledAsync()` | Si el usuario registró al menos una credencial biométrica |
| `supportedAuthenticationTypesAsync()` | Qué tipos están disponibles (fingerprint, face, iris) |

> ⚠️ Siempre verificar los tres **antes** de lanzar el prompt para evitar errores en runtime.

---

## Lanzando el prompt biométrico

```javascript
async function handleBiometricLogin() {
    const { hasHardware, isEnrolled } = await checkBiometrics();

    if (!hasHardware || !isEnrolled) {
        Alert.alert('No disponible', 'Biometría no configurada en el dispositivo');
        return;
    }

    const result = await LocalAuthentication.authenticateAsync({
        promptMessage: 'Autenticá tu identidad',
        fallbackLabel: 'Usar contraseña',
        cancelLabel: 'Cancelar',
        disableDeviceFallback: false,
    });

    if (result.success) {
        // Autenticación exitosa — cargar sesión desde SecureStore
        const token = await SecureStore.getItemAsync('token');
        login(token);
    } else {
        // result.error: 'user_cancel' | 'lockout' | 'not_enrolled' ...
        Alert.alert('Error', result.error);
    }
}
```

### Posibles resultados

| Resultado | Significado |
|---|---|
| `success: true` | Autenticación exitosa |
| `user_cancel` | El usuario canceló el prompt |
| `lockout` | Demasiados intentos fallidos |
| `not_enrolled` | Sin credenciales registradas |
| `system_cancel` | El SO interrumpió la autenticación |

> A diferencia de Android nativo, acá **no hay 3 callbacks separados** (`succeeded`/`failed`/`error`) — todo llega en un único objeto `result` con `success` y, si falló, `error`. Comparar con los [[Autenticación Biométrica#Los 3 callbacks — entran en el parcial|3 callbacks de BiometricPrompt]].

---

## Comparación con Android nativo (AndroidX Biometric)

| Concepto | Android nativo | React Native (Expo) |
|---|---|---|
| Librería | `androidx.biometric` | `expo-local-authentication` |
| Verificar hardware/enrolamiento | `BiometricManager.canAuthenticate()` | `hasHardwareAsync()` + `isEnrolledAsync()` |
| Configurar el diálogo | `PromptInfo.Builder()` | Objeto de opciones en `authenticateAsync({...})` |
| Disparar y escuchar | `BiometricPrompt` + 3 callbacks | `authenticateAsync()` → una promesa con `result.success` |
| Resultado exitoso | `onAuthenticationSucceeded()` | `result.success === true` |
| Resultado fallido (reintentable) | `onAuthenticationFailed()` | No distingue — todo cae en `result.success === false` |
| Error del sistema/cancelación | `onAuthenticationError()` | `result.error` (`'user_cancel'`, `'lockout'`, etc.) |

> El mismo flujo conceptual (preguntar → configurar → disparar) se desarrolla a fondo, con los 3 factores de autenticación y la clasificación de sensores, en [[Autenticación Biométrica]].

---

## Integración con la sesión (Context + SecureStore)

El flujo típico combina los tres conceptos de la clase:

```
1. Usuario presiona "Ingresar con huella"
          ↓
2. checkBiometrics() → hasHardware + isEnrolled
          ↓
3. authenticateAsync() → prompt nativo
          ↓
4. result.success === true
          ↓
5. SecureStore.getItemAsync('token') → token guardado de un login previo
          ↓
6. login(token) → AuthContext actualiza el estado global
```

> La biometría **no reemplaza** al login con credenciales — lo complementa como acceso rápido a una sesión ya iniciada, cuyo token vive en [[Storage en React Native#SecureStore — uso básico|SecureStore]]. Ver también [[Context API (useContext)]].

---

## Errores comunes

| ❌ Qué NO hacer | Por qué | ✅ Qué hacer |
|---|---|---|
| Llamar `authenticateAsync()` directamente | Sin verificar `hasHardware`/`isEnrolled` → crash en dispositivos sin sensor | Siempre verificar los tres checks antes del prompt |
| Asumir que `success: false` siempre es cancelación | Puede ser `lockout`, `not_enrolled`, error del sistema, etc. | Leer `result.error` y mostrar un mensaje específico |
| Guardar el token biométrico por separado | Confunde el flujo de sesión | Reusar el mismo token de [[JWT]] guardado en SecureStore — la biometría solo desbloquea el acceso a él |

---

## Relacionado

- [[Clase 09 - Gestión de Sesión en React Native]]
- [[Autenticación Biométrica]]
- [[Context API (useContext)]]
- [[Storage en React Native]]
- [[Expo]]
- [[JWT]]
