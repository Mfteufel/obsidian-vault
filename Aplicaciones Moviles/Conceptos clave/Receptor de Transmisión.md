---
tags: [android, broadcast-receiver, eventos, sistema, desarrollo-aplicaciones-1]
aliases: [BroadcastReceiver, Broadcast Receiver, Receptor de Transmisión, onReceive]
---

# CC_Receptor de Transmisión (BroadcastReceiver)

> Permite que una app **responda a eventos del sistema o de otras apps**: cambios de red, batería baja, una alarma activada.

## Características

- Recibe notificaciones del sistema o de otras apps
- Se usa para programar eventos o notificar cambios importantes
- **No tiene interfaz gráfica**
- **Vive poco**: solo mientras procesa el evento

**Ej. real:** una app de clima que se actualiza sola al conectarse a WiFi.

---

## Ejemplo básico (Java)

```java
public class MiReceptor extends BroadcastReceiver {
    @Override
    public void onReceive(Context context, Intent intent) {
        Toast.makeText(context, "Evento recibido", Toast.LENGTH_SHORT).show();
    }
}
```

### Reaccionar a varios eventos

```java
public class MiReceptor extends BroadcastReceiver {
    @Override
    public void onReceive(Context context, Intent intent) {
        String accion = intent.getAction();

        if (Intent.ACTION_BATTERY_LOW.equals(accion)) {
            // Batería baja
        } else if (Intent.ACTION_BOOT_COMPLETED.equals(accion)) {
            // El teléfono se encendió
        }
    }
}
```

### Versión Kotlin

```kotlin
class MyReceiver : BroadcastReceiver() {
    override fun onReceive(context: Context?, intent: Intent?) {
        when (intent?.action) {
            Intent.ACTION_BATTERY_LOW -> { /* batería baja */ }
            Intent.ACTION_BOOT_COMPLETED -> { /* el teléfono arrancó */ }
        }
    }
}
```

---

## Las 2 formas de registrarlo

### 1. Declarativo — en el Manifest

```xml
<receiver android:name=".MiReceptor">
    <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
    </intent-filter>
</receiver>
```

- Funciona **aunque la app esté cerrada**
- Se usa para eventos como el arranque del sistema

### 2. Programático — en código

```java
// En onCreate() / onResume()
MiReceptor receptor = new MiReceptor();
IntentFilter filtro = new IntentFilter(Intent.ACTION_BATTERY_LOW);
registerReceiver(receptor, filtro);

// En onDestroy() / onPause() — IMPORTANTE
unregisterReceiver(receptor);
```

- Solo funciona **mientras la app corre**
- Si no lo desregistrás → **memory leak**

| | Declarativo | Programático |
|---|---|---|
| Dónde | Manifest | Código |
| App cerrada | ✅ Funciona | ❌ No |
| Hay que desregistrar | No | **Sí** |

---

## Eventos comunes del sistema

| Acción | Se dispara cuando |
|---|---|
| `ACTION_BOOT_COMPLETED` | El teléfono terminó de encender |
| `ACTION_BATTERY_LOW` | La batería está baja |
| `ACTION_POWER_CONNECTED` | Se enchufa el cargador |
| `ACTION_AIRPLANE_MODE_CHANGED` | Cambia el modo avión |
| `CONNECTIVITY_ACTION` | Cambia la conexión de red |
| `SMS_RECEIVED` | Llega un SMS |

---

## Reglas importantes

- `onReceive()` corre en el **hilo principal** y tiene ~10 segundos → si tarda más, ANR
- Para trabajo pesado: disparar un [[Servicio]] o `WorkManager` desde el receiver
- Desde Android 8+ **muchos broadcasts implícitos ya no funcionan** desde el Manifest → registrarlos en código

```java
// Delegar el trabajo pesado
@Override
public void onReceive(Context context, Intent intent) {
    Intent servicio = new Intent(context, MiServicio.class);
    context.startService(servicio);
}
```

---

## Relacionado

- [[Clase 02 - Android Primeros Pasos]]
- [[Servicio]]
- [[Actividad]]
- [[Proveedor de Contenido]]
- [[Intents]]
- [[AndroidManifest]]
