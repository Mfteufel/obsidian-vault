---
tags: [clase, android, fragment, navigation-component, desarrollo-aplicaciones-1]
---
![[Fragments_NavigationComponent.pdf]]
# Clase 03 — Fragments y Navigation Component

**Materia:** Desarrollo de Aplicaciones 1 — 2C 2026

> ⚠️ El PDF que subió el profe dice "Clase 4" en la portada, pero el contenido corresponde a la **Clase 3** (viene antes de Retrofit). Nota renombrada para que el orden cronológico sea correcto.

---

## Agenda

1. **Fragments** — concepto, ciclo de vida y componentes
2. **Navigation Component** — NavGraph · NavHost · NavController

---

## 1. Fragments

Un Fragment es una **parte modular de la UI** que vive dentro de una Activity. Es como un "mini-Activity" con layout, lógica y ciclo de vida propios — pero no puede existir sin una Activity que lo contenga.

| | UI propia (XML + Java) | Se agregan/reemplazan dinámicamente |
|---|---|---|
| | Se comunican con la Activity y entre sí | Gestionados por `FragmentManager` |

### Activity vs Fragment

| Característica | Activity | Fragment |
|---|---|---|
| Ciclo de vida | Independiente | Depende de la Activity |
| Navegación | Con `Intent` | Con `NavController` |

> Desarrollo completo en [[Fragment]]

### Ciclo de vida (los 4 callbacks del día a día)

| # | Método | Qué pasa |
|---|---|---|
| 1 | `onCreate()` | Se crea el fragment. Inicializás variables y argumentos (`Bundle`). Todavía no hay vistas |
| 2 | `onCreateView()` | Se infla el layout. Retornás la View. **No toques vistas acá** |
| 3 | `onViewCreated()` | El layout ya existe. Acá van `findViewById()`, listeners, lógica de UI |
| 4 | `onDestroyView()` | Se destruye la vista. Limpiás referencias para evitar memory leaks |

### Los 5 componentes de un Fragment

| Componente | Qué es |
|---|---|
| **Clase Fragment** | Define la lógica. Similar a Activity, pero más liviana |
| **Layout XML** | Interfaz que se infla dentro del Fragment (`fragment_home.xml`) |
| **FragmentManager** | Gestiona los Fragments dentro de la Activity: add, replace, remove |
| **FragmentTransaction** | Operaciones: `add()`, `replace()`, `remove()`, `commit()` |
| **FragmentContainerView** | Contenedor en el layout de la Activity donde se insertan los Fragments |

---

## 2. Navigation Component

Librería de Jetpack que **centraliza toda la navegación** en un Navigation Graph y maneja el back stack automáticamente.

| # | Pieza | Es |
|---|---|---|
| 1 | **Navigation Graph** (`nav_graph.xml`) | XML con todos los destinos y sus conexiones |
| 2 | **NavHostFragment** | Contenedor que muestra el destino actual |
| 3 | **NavController** | Objeto que ejecuta la navegación en runtime |

> Desarrollo completo en [[Navigation Component]]

### Setup — los 4 pasos

**1. Dependencias**

```gradle
def nav_version = "2.8.0"
implementation "androidx.navigation:navigation-fragment:$nav_version"
implementation "androidx.navigation:navigation-ui:$nav_version"
```

**2. Crear el Navigation Graph**

```xml
<navigation app:startDestination="@id/homeFragment">
    <fragment android:id="@+id/homeFragment"
        android:name="com.example.ui.HomeFragment">
        <action android:id="@+id/action_home_to_detail"
            app:destination="@id/detailFragment"/>
    </fragment>
    <fragment android:id="@+id/detailFragment"
        android:name="com.example.ui.DetailFragment">
        <argument android:name="itemId" app:argType="string"/>
    </fragment>
</navigation>
```

**3. NavHostFragment en el layout**

```xml
<androidx.fragment.app.FragmentContainerView
    android:id="@+id/nav_host_fragment"
    android:name="androidx.navigation.fragment.NavHostFragment"
    app:navGraph="@navigation/nav_graph"
    app:defaultNavHost="true"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />
```

**4. Navegar con NavController**

```java
// Navegar
NavController nav = Navigation.findNavController(view);
nav.navigate(R.id.action_home_to_detail);

// Volver atrás
navController.popBackStack();

// Volver hasta un destino específico
nav.popBackStack(R.id.homeFragment, false);
```

---

## Single Activity Architecture

En vez de una Activity por pantalla, hay **una sola `MainActivity`** que contiene el `NavHostFragment`. Cada pantalla de la app es un Fragment navegado por el `NavController`.

```
MainActivity (única Activity)
    └── NavHostFragment
            ├── LoginFragment
            ├── HomeFragment    ← activo
            ├── DetailFragment
            └── ProfileFragment
```

> 💡 **`popUpTo` + `popUpToInclusive: true`** en Login → Home elimina el `LoginFragment` del back stack. Cuando el usuario toca "atrás" en Home, la app se cierra — no vuelve al Login.

---

## Relacionado

- [[Clase 02 - Android Primeros Pasos]]
- [[Fragment]]
- [[Navigation Component]]
- [[Intents]]
- [[Actividad]]
