---
tags: [android, activity, ciclo-de-vida, ui, desarrollo-aplicaciones-1]
aliases: [Activity, Actividad, Ciclo de vida Activity, onCreate]
---

# CC_Actividad (Activity)

> Una **Activity** representa una pantalla con la que el usuario puede interactuar. Es el componente base de cualquier app con interfaz gráfica.

## Características

- Cada app tiene al menos **una actividad principal**
- Gestiona la UI mediante archivos **XML + código Java** (no siempre)
- Tiene un **ciclo de vida** que el sistema gestiona automáticamente
- Se declara sí o sí en el [[AndroidManifest]]

```java
public class MainActivity extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);  // enlaza el diseño XML
    }
}
```

---

## Dos protagonistas: Activity + Layout XML

| Pieza | Rol | Define |
|---|---|---|
| **Activity** (Java) | Controlador | Lógica de negocio, ciclo de vida, navegación |
| **Layout XML** | Vista | Qué se muestra, posición, tamaño, colores |

El sistema combina ambos y renderiza la pantalla.

### El ID es el puente

```
activity_main.xml              MainActivity.java
─────────────────              ─────────────────
<TextView                      TextView tv =
  android:id="@+id/tvTitle"      findViewById(R.id.tvTitle);

<Button                        Button btn =
  android:id="@+id/btnLogin"     findViewById(R.id.btnLogin);
```

**¿Qué es el inflate?**
El sistema lee el XML y lo convierte en objetos `View` **en memoria**. Recién ahí se puede manipular desde Java.

---

## Ciclo de vida

| Método | Qué pasa | ¿El usuario ve? |
|---|---|---|
| `onCreate()` | La Activity se crea en memoria → acá va `setContentView()` (**el inflate**) | ❌ |
| `onStart()` | Activity **visible**, pero aún no interactuable | 👀 apenas |
| `onResume()` | El usuario **ve e interactúa** con la pantalla | ✅ |
| `onPause()` | Otra Activity pasa al frente | — |
| `onStop()` | Ya no es visible | — |
| `onRestart()` | El usuario vuelve a la Activity | — |
| `onDestroy()` | La Activity se destruye | — |

```
Activity launched
      ↓
   onCreate()  ← setContentView() acá
      ↓
   onStart()  ←──────── onRestart()
      ↓                      ↑
   onResume()  ←── el usuario vuelve
      ↓
 [Activity running]
      ↓
   onPause()  →  onStop()  →  onDestroy()
```

> ⚠️ **Nunca accedas a una View antes de `setContentView()`** — todavía no existe en memoria. Es la causa #1 de `NullPointerException`.

---

## Suscribirse a eventos de UI

**Flujo:** usuario toca el botón → se dispara el evento → el listener llama a `onClick()` → corre tu lógica.

### Forma 1 — Clase anónima (la más común)

```java
Button btn = findViewById(R.id.miBoton);
btn.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View v) {
        // tu lógica acá
    }
});
```

### Forma 2 — Lambda (Java 8+, más corta)

```java
Button btn = findViewById(R.id.miBoton);
btn.setOnClickListener(v -> {
    // tu lógica acá
});
```

> Mismo resultado, menos código.

### Otros listeners

| Listener | Se dispara con |
|---|---|
| `setOnClickListener` | Click |
| `setOnLongClickListener` | Click prolongado |
| `setOnCheckedChangeListener` | Cambio en CheckBox / Switch |
| `setOnItemSelectedListener` | Selección en Spinner |
| `setOnItemClickListener` | Click en ítem de lista → [[ListView y Adapters]] |

---

## Navegar a otra Activity

```java
Intent intent = new Intent(this, DetailActivity.class);
startActivity(intent);
```

> Desarrollo completo en [[Intents]]

---

## Resumen rápido

| Necesito... | Uso |
|---|---|
| Arrancar la pantalla | `onCreate()` |
| Cargar el diseño | `setContentView(R.layout.x)` |
| Acceder a un componente | `findViewById(R.id.x)` |
| Escuchar un click | `setOnClickListener()` |
| Cambiar de pantalla | `Intent` + `startActivity()` |
| Cerrar la pantalla actual | `finish()` |

---

## Relacionado

- [[Clase 02 - Android Primeros Pasos]]
- [[Android - Estructura Básica y Pasaje de Actividades]]
- [[Intents]]
- [[ListView y Adapters]]
- [[AndroidManifest]]
- [[Arquitectura de Android]]
- [[Servicio]]
