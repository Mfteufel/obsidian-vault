---
tags: [android, intent, navegacion, activity, desarrollo-aplicaciones-1]
aliases: [Intent, Intents, Intent Explícito, Intent Implícito, Navegación Android]
---

# CC_Intents

> Un **Intent** es un mensaje o solicitud que permite comunicar componentes de una app, o incluso apps distintas.

## Para qué se usa

- Abrir una Activity dentro de la misma app
- Enviar datos entre Activities
- Iniciar servicios en segundo plano ([[Servicio]])
- Lanzar componentes del sistema (cámara, navegador, compartir)

---

## Los 2 tipos

| Tipo | Cuándo | Decís... |
|---|---|---|
| **Explícito** | Abrir una Activity **específica de tu app** | "Abrí *esta* pantalla" |
| **Implícito** | Pedirle al sistema que haga una acción | "Que alguien abra este link" |

---

## 1. Intent Explícito

### Navegar a otra Activity

```java
Intent intent = new Intent(this, DetailActivity.class);
startActivity(intent);
```

### Enviar datos con putExtra()

```java
// MainActivity.java
Intent intent = new Intent(this, DetailActivity.class);
intent.putExtra("USERNAME", "Juan Pérez");   // String
intent.putExtra("EDAD", 25);                  // int
startActivity(intent);
```

### Recibir datos

```java
// DetailActivity.java
Intent intent = getIntent();
String username = intent.getStringExtra("USERNAME");
int edad = intent.getIntExtra("EDAD", 0);   // 0 = valor por defecto
```

### Tabla putExtra / getExtra

| Tipo | Enviar | Recibir |
|---|---|---|
| String | `putExtra(key, String)` | `getStringExtra(key)` |
| int | `putExtra(key, int)` | `getIntExtra(key, default)` |
| boolean | `putExtra(key, boolean)` | `getBooleanExtra(key, default)` |
| double | `putExtra(key, double)` | `getDoubleExtra(key, default)` |
| Bundle | `putExtra(key, Bundle)` | `getBundleExtra(key)` |

> ⚠️ La **key** tiene que ser idéntica en las dos Activities. Conviene usar constantes.

---

## 2. Intent Implícito

### Abrir una web

```java
Intent intent = new Intent(Intent.ACTION_VIEW);
intent.setData(Uri.parse("https://www.google.com"));
startActivity(intent);
```

### Abrir una ubicación en el mapa

```java
Intent intent = new Intent(Intent.ACTION_VIEW);
intent.setData(Uri.parse("geo:37.7749,-122.4194"));
startActivity(intent);
```

### Acciones más usadas

| Acción | Qué hace |
|---|---|
| `ACTION_VIEW` | Abre contenido: web, imágenes, ubicaciones, archivos |
| `ACTION_SEND` | Comparte texto o archivos con otras apps |
| `ACTION_CALL` / `ACTION_DIAL` | Llama / abre el marcador con el número |
| `ACTION_SETTINGS` | Abre la configuración del dispositivo |
| `ACTION_IMAGE_CAPTURE` | Abre la cámara para tomar una foto |
| `ACTION_UNINSTALL_PACKAGE` | Permite desinstalar una app |

### Ejemplo: compartir texto

```java
Intent intent = new Intent(Intent.ACTION_SEND);
intent.setType("text/plain");
intent.putExtra(Intent.EXTRA_TEXT, "Mirá esta app!");
startActivity(Intent.createChooser(intent, "Compartir con..."));
```

---

## El stack de Activities

```
┌──────────────────┐
│ DetailActivity   │  ← nueva (arriba del stack)
├──────────────────┤
│ MainActivity     │  ← sigue existiendo abajo
└──────────────────┘
```

- `startActivity()` **apila** una pantalla nueva
- El botón **atrás** la desapila automáticamente — no necesitás código
- `finish()` cierra la Activity actual a mano

---

## Diferencia clave

| | Explícito | Implícito |
|---|---|---|
| **Destino** | Clase concreta (`.class`) | Una acción (`ACTION_*`) |
| **Quién resuelve** | Tu app | El sistema operativo |
| **Riesgo** | Ninguno | Puede no haber app que lo maneje |

```java
// Verificar antes de lanzar un implícito
if (intent.resolveActivity(getPackageManager()) != null) {
    startActivity(intent);
}
```

---

## Relacionado

- [[Clase 02 - Android Primeros Pasos]]
- [[Actividad]]
- [[Android - Estructura Básica y Pasaje de Actividades]]
- [[Servicio]]
- [[Receptor de Transmisión]]
- [[AndroidManifest]]
