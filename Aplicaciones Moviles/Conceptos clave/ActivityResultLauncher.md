---
tags: [android, activityresultlauncher, permisos, intents, desarrollo-aplicaciones-1]
aliases: [ActivityResultLauncher, ActivityResultContract, registerForActivityResult, GetContent, RequestPermission]
---

# CC_ActivityResultLauncher

> Patrón moderno para **lanzar acciones del sistema y recibir su resultado**, con un callback fuerte de tipos. Reemplaza al viejo `startActivityForResult()` + `onActivityResult()`.

## La idea mental

```
Lanzás una acción → el SO toma el control → recibís un callback con el resultado
```

---

## Las 3 piezas que siempre aparecen juntas

| # | Pieza | Qué es |
|---|---|---|
| 1 | **El contrato** (`ActivityResultContract`) | Define **QUÉ** se lanza y **qué tipo** de resultado devuelve |
| 2 | **El callback** | El código que se ejecuta cuando el SO devuelve el resultado |
| 3 | **El launcher** (`ActivityResultLauncher`) | El objeto que dispara la acción con `.launch(...)` |

---

## Ejemplo — abrir la galería paso a paso

```java
// LoginActivity.java

private ActivityResultLauncher<String> pickImageLauncher =
    registerForActivityResult(                    // ① contrato
        new ActivityResultContracts.GetContent(),
        uri -> {                                    // ② callback
            if (uri != null) {
                imageView.setImageURI(uri);
            }
        }
    );

// Para lanzarlo desde un click:
pickImageLauncher.launch("image/*");               // ③ .launch(input)
```

| Paso | Detalle |
|---|---|
| **Contrato `GetContent`** | Recibe un MIME type (`"image/*"`), devuelve una `Uri` |
| **Callback tipado** | El compilador ya sabe que el resultado va a ser una `Uri` — no hay que castear nada |
| **`.launch(input)`** | Dispara la acción cuando el usuario quiere (ej: un click de botón) |

---

## Contratos más usados

No hace falta memorizarlos todos — estos cubren el **90% de los casos reales**:

| Contrato | ¿Para qué sirve? | Input | Output |
|---|---|---|---|
| `GetContent` | Elegir un archivo (imagen, pdf, etc.) desde la galería o archivos | MIME type `"image/*"` | `Uri` del archivo |
| `RequestPermission` | Pedir un permiso en runtime | Nombre del permiso | `boolean` (concedido) |
| `RequestMultiplePermissions` | Pedir varios permisos juntos | `String[]` | `Map<String, Boolean>` |
| `StartActivityForResult` | Genérico, cuando no hay un contrato específico | `Intent` | `ActivityResult` |

> 💡 **Tip:** el launcher debe registrarse **antes de STARTED**. Siempre como campo de clase o en `onCreate()`.

### Ejemplo — pedir un permiso en runtime

```java
private ActivityResultLauncher<String> permissionLauncher =
    registerForActivityResult(
        new ActivityResultContracts.RequestPermission(),
        isGranted -> {
            if (isGranted) {
                abrirCamara();
            } else {
                Toast.makeText(this, "Permiso denegado", Toast.LENGTH_SHORT).show();
            }
        }
    );

// Lanzar
permissionLauncher.launch(Manifest.permission.CAMERA);
```

---

## Las 4 cosas para no olvidar

| # | Regla | Por qué |
|---|---|---|
| 1 | **Registrar antes de STARTED** | Siempre como campo o en `onCreate()`. Registrarlo en un `onClick` tira excepción |
| 2 | **El callback es asíncrono** | No se ejecuta en la línea siguiente al `.launch()`. Corre cuando el usuario termina |
| 3 | **Mismo patrón que `BiometricPrompt`** | Declarás el prompt y el callback → lo lanzás → el callback resuelve. Idéntico |
| 4 | El launcher es **por tipo de input** | Un `ActivityResultLauncher<String>` no sirve para lanzar un `Intent` — cada contrato define su propio tipo |

> Ver [[Autenticación Biométrica]] — usa exactamente el mismo patrón conceptual (declarar → lanzar → callback resuelve), aunque no use `ActivityResultLauncher` directamente sino `BiometricPrompt`.

---

## Comparación con el patrón viejo

| | `startActivityForResult()` (viejo) | `ActivityResultLauncher` (moderno) |
|---|---|---|
| Callback | `onActivityResult()` — un solo método gigante para toda la Activity, con `requestCode` a mano | Un callback **por launcher**, tipado |
| Tipado | `Intent` genérico, hay que castear | Fuertemente tipado según el contrato |
| Registro | En cualquier momento | Debe ser antes de STARTED |
| Legibilidad | Un `switch(requestCode)` gigante si hay varios usos | Cada launcher vive aislado, cerca de donde se usa |

---

## Errores típicos

| Síntoma | Causa |
|---|---|
| `IllegalStateException: LifecycleOwner is attempting to register...` | Se registró el launcher **después** de STARTED (ej: dentro de un `onClick`) |
| El callback nunca se ejecuta | El usuario canceló la acción, o el contrato no coincide con lo lanzado |
| `ClassCastException` | Se usó un contrato que no corresponde al tipo de dato que se intenta manejar |

---

## Relacionado

- [[Clase 06 - Biometría y Seguridad en el Dispositivo]]
- [[Autenticación Biométrica]]
- [[AndroidManifest]]
- [[Actividad]]
