---
tags: [clase, android, activity, intents, gradle, desarrollo-aplicaciones-1]
---
![[Android - Primeros pasos.pdf]]
# Clase 02 — Android: Primeros pasos

**Materia:** Desarrollo de Aplicaciones 1 — 2C 2026

> Estructura de un proyecto Android, los 4 componentes principales y cómo navegar entre pantallas.

---

## Agenda

1. Estructura del proyecto y sus componentes
2. Conceptos principales → [[Arquitectura de Android]] · Activity · Service · Broadcast Receiver
3. Navegación → [[Intents]]

---

## 1. Estructura del proyecto (Scaffold)

| Zona | Qué es |
|---|---|
| `AndroidManifest.xml` | La "cédula" de la app → [[AndroidManifest]] |
| `app/` | El corazón: tu código + recursos |
| **Root files** | Configuración de build → [[Gradle]] |

### Dentro de `app/`

| Carpeta           | Para qué sirve                                          |
| ----------------- | ------------------------------------------------------- |
| `java/com.tuapp/` | Clases principales: `MainActivity.java`, modelos, utils |
| `res/drawable/`   | Imágenes y gráficos vectoriales                         |
| `res/layout/`     | XML que definen la interfaz                             |
| `res/values/`     | Colores, estilos, strings                               |
| `res/mipmap/`     | Íconos de la app en varias resoluciones                 |
| `res/xml/`        | Configuraciones avanzadas (preferencias, seguridad)     |

---

## 2. Arquitectura de Android (5 capas)

| # | Capa | Qué hay |
|---|---|---|
| 5 | **Aplicaciones** | Tu app, WhatsApp, Maps ← **construís acá** |
| 4 | **Framework** | Activities, ciclo de vida, permisos, APIs ← **trabajás acá** |
| 3 | **ART + Librerías** | Ejecuta tu app · SQLite · OpenGL |
| 2 | **HAL** | Puente hardware ↔ software |
| 1 | **Linux Kernel** | Procesos, memoria, seguridad |

> Las capas 1–3 trabajan en segundo plano. Detalle en [[Arquitectura de Android]]

---

## 3. Los 4 componentes principales

| Componente | Tiene UI | Para qué |
|---|---|---|
| [[Actividad]] | ✅ Sí | Una pantalla con la que el usuario interactúa |
| [[Servicio]] | ❌ No | Tareas largas en segundo plano |
| [[Receptor de Transmisión]] | ❌ No | Reacciona a eventos del sistema |
| [[Proveedor de Contenido]] | ❌ No | Comparte datos entre apps |

> Los 4 se declaran en el [[AndroidManifest]].

---

## 4. Cómo funciona una pantalla

Dos protagonistas que trabajan juntos:

| Pieza | Rol | Define |
|---|---|---|
| **Activity** (Java) | Controlador | Lógica, ciclo de vida, navegación |
| **Layout XML** | Vista | Qué se muestra, posición, colores |

**El puente entre ambos es el ID:**

```java
// activity_main.xml → <TextView android:id="@+id/tvTitle" .../>
TextView tv = findViewById(R.id.tvTitle);
```

**¿Qué es el inflate?**
El sistema lee el XML y lo convierte en objetos `View` en memoria. Recién ahí se pueden manipular desde Java.

---

## 5. Ciclo de vida — cuándo se renderiza

| Método | Qué pasa |
|---|---|
| `onCreate()` | La Activity se crea → acá va `setContentView()` (**inflate**) |
| `onStart()` | Visible, pero aún no interactuable |
| `onResume()` | **El usuario ve e interactúa con la pantalla** |

> ⚠️ **No accedas a ninguna View antes de `setContentView()`** — todavía no existen en memoria.

---

## 6. Eventos de UI

```java
// Forma corta (lambda)
Button btn = findViewById(R.id.miBoton);
btn.setOnClickListener(v -> {
    // tu lógica acá
});
```

Otros: `setOnLongClickListener` · `setOnCheckedChangeListener` · `setOnItemSelectedListener`

---

## 7. ListView

Muestra una colección de ítems usando un **Adapter** como intermediario.

```
Datos (List/Array) → Adapter → Views → ListView
```

> Desarrollo completo en [[ListView y Adapters]]

---

## 8. Navegación — Intents

Un **Intent** es un mensaje que comunica componentes.

| Tipo | Para qué | Ejemplo |
|---|---|---|
| **Explícito** | Abrir una Activity **tuya** | `new Intent(this, DetailActivity.class)` |
| **Implícito** | Pedirle al sistema que haga algo | Abrir el navegador, la cámara, compartir |

```java
// Explícito
Intent intent = new Intent(this, DetailActivity.class);
intent.putExtra("USERNAME", "Juan Pérez");
startActivity(intent);
```

> Todos los tipos y acciones en [[Intents]]

---

## Relacionado

- [[Clase 01 - Presentación de la materia]]
- [[Arquitectura de Android]]
- [[AndroidManifest]]
- [[Gradle]]
- [[Actividad]]
- [[Servicio]]
- [[Receptor de Transmisión]]
- [[Intents]]
- [[ListView y Adapters]]
- [[Android Studio]]
