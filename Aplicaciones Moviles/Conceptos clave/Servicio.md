---
tags: [android, service, background, ciclo-de-vida, desarrollo-aplicaciones-1]
aliases: [Service, Servicio, Started Service, Bound Service]
---

# CC_Servicio (Service)

> Componente que realiza **operaciones de larga duración en segundo plano**, sin interfaz de usuario.

## Características

- **No tiene UI**, funciona en background
- Puede ejecutarse incluso si la app **no está activa**
- Ideal para tareas largas: sincronización de datos, reproducción de música, descargas
- Consume recursos aunque no se vea

**Ej. real:** Spotify reproduciendo música mientras usás otra app.

---

## Los 2 tipos

| | **Started Service** | **Bound Service** |
|---|---|---|
| **Se inicia con** | `startService()` | `bindService()` |
| **Para qué** | Operaciones únicas y largas (descargar un archivo) | Comunicación cliente–servidor |
| **Sobrevive** | Sigue aunque muera quien lo inició | Muere cuando todos se desvinculan |
| **Se detiene con** | `stopService()` o `stopSelf()` | Al desvincularse el último cliente |
| **Múltiples clientes** | No aplica | Sí, varios componentes pueden vincularse |

---

## Ciclo de vida

| Método | Cuándo se llama |
|---|---|
| `onCreate()` | El servicio se crea por primera vez |
| `onStartCommand()` | El servicio es **iniciado** (started) |
| `onBind()` | Un componente se **vincula** al servicio (bound) |
| `onDestroy()` | El servicio es destruido |

```
startService()  →  onCreate()  →  onStartCommand()  →  [corriendo]
                                                            ↓
                                        stopService() / stopSelf()
                                                            ↓
                                                       onDestroy()
```

---

## Ejemplo — Started Service (Java)

```java
public class MusicService extends Service {

    @Override
    public int onStartCommand(Intent intent, int flags, int startId) {
        // Lógica que corre en background
        return START_STICKY;   // si el sistema lo mata, lo reinicia
    }

    @Override
    public IBinder onBind(Intent intent) {
        return null;   // null = no es un Bound Service
    }

    @Override
    public void onDestroy() {
        super.onDestroy();
        // liberar recursos
    }
}
```

### Versión Kotlin

```kotlin
class MusicService : Service() {
    override fun onStartCommand(intent: Intent?, flags: Int, startId: Int): Int {
        // Lógica que corre en background
        return START_STICKY
    }
    override fun onBind(intent: Intent?): IBinder? = null
}
```

---

## Cómo arrancarlo y detenerlo

```java
// Iniciar (desde una Activity)
Intent intent = new Intent(this, MusicService.class);
startService(intent);

// Detener desde afuera
stopService(intent);

// Detener desde adentro del propio Service
stopSelf();
```

---

## Valores de retorno de onStartCommand()

| Constante | Si el sistema mata el servicio... |
|---|---|
| `START_STICKY` | Lo reinicia sin el Intent original (música) |
| `START_NOT_STICKY` | No lo reinicia (tareas puntuales) |
| `START_REDELIVER_INTENT` | Lo reinicia **con** el Intent original (descargas) |

---

## Declararlo en el Manifest

```xml
<service android:name=".MusicService" />
```

> Si no lo declarás, el servicio **no arranca**. Ver [[AndroidManifest]]

---

## Ojo con esto

- Un Service corre en el **hilo principal** por defecto → tarea pesada = app congelada
- Para trabajo pesado usar un hilo aparte o `IntentService` / `WorkManager`
- Desde Android 8+ hay límites fuertes a los servicios en background → considerar **Foreground Service** (con notificación visible)

---

## Relacionado

- [[Clase 02 - Android Primeros Pasos]]
- [[Actividad]]
- [[Receptor de Transmisión]]
- [[Proveedor de Contenido]]
- [[Intents]]
- [[AndroidManifest]]
