---
tags: [android, navigation-component, jetpack, navegacion, fragment, desarrollo-aplicaciones-1]
aliases: [Navigation Component, NavController, NavGraph, NavHost, popUpTo, Single Activity Architecture]
---

# CC_Navigation Component

> Librería de Jetpack que centraliza toda la navegación de la app en un **nav graph** (XML) y maneja el back stack automáticamente. Reemplaza la navegación por [[Intents]] entre Activities cuando trabajás con [[Fragment|Fragments]].

## Las 3 piezas

| # | Pieza | Qué es | Dónde vive |
|---|---|---|---|
| 1 | **Navigation Graph** | El mapa de destinos y sus conexiones | `res/navigation/nav_graph.xml` |
| 2 | **NavHostFragment** | El contenedor que muestra el destino actual | `activity_main.xml` |
| 3 | **NavController** | El objeto que ejecuta la navegación en runtime | Se obtiene en el Fragment |

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

> 💡 `popUpTo` + `popUpToInclusive: true` en Login → Home elimina el `LoginFragment` del back stack. Cuando el usuario toca "atrás" en Home, la app **se cierra** — no vuelve al Login.

---

## Comparación con Intents

| | **Intents** (entre Activities) | **Navigation Component** (entre Fragments) |
|---|---|---|
| Unidad de pantalla | Activity | Fragment |
| Navegar | `startActivity(intent)` | `navController.navigate(actionId)` |
| Enviar datos | `intent.putExtra("key", v)` | `bundle.putString("key", v)` |
| Recibir datos | `getIntent().getStringExtra("key")` | `getArguments().getString("key")` |
| Volver atrás | Automático (sistema) | `popBackStack()` |
| Limpiar historial | `FLAG_ACTIVITY_CLEAR_TASK` | `popUpTo` + `popUpToInclusive` |
| Se configura en | [[AndroidManifest]] | `nav_graph.xml` |

---

## Paso 1 — Dependencias

> ⚠️ La versión puede variar según cuándo se armó el proyecto. El profe usó `2.8.0`, el repo de referencia trae `2.8.9` — cualquiera de las dos sirve, usá la que ya tenga tu `libs.versions.toml`.

```gradle
def nav_version = "2.8.0"
implementation "androidx.navigation:navigation-fragment:$nav_version"
implementation "androidx.navigation:navigation-ui:$nav_version"
```

O con Version Catalog (`libs.versions.toml`):

```toml
[versions]
navigation = "2.8.9"

[libraries]
navigation-fragment = { group = "androidx.navigation", name = "navigation-fragment", version.ref = "navigation" }
navigation-ui      = { group = "androidx.navigation", name = "navigation-ui",      version.ref = "navigation" }
```

```kotlin
// app/build.gradle.kts
dependencies {
    implementation(libs.navigation.fragment)
    implementation(libs.navigation.ui)
}
```

> Cómo agregar dependencias en general: [[Gradle]]

---

## Paso 2 — El NavHost en el layout

`MainActivity` deja de tener contenido propio. Solo declara el **contenedor**:

```xml
<androidx.fragment.app.FragmentContainerView
    android:id="@+id/nav_host_fragment"
    android:name="androidx.navigation.fragment.NavHostFragment"
    app:navGraph="@navigation/nav_graph"
    app:defaultNavHost="true"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />
```

| Atributo | Significado |
|---|---|
| `android:name` | Marca este contenedor como NavHostFragment |
| `app:defaultNavHost="true"` | Intercepta el botón "atrás" del sistema |
| `app:navGraph` | El XML que define los destinos |

---

## Paso 3 — Nav graphs

### Ejemplo simple (un solo graph)

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

### Graph raíz — solo incluye sub-graphs (para proyectos más grandes)

```xml
<navigation android:id="@+id/nav_graph"
    app:startDestination="@id/auth_nav_graph">

    <include app:graph="@navigation/auth_nav_graph" />
    <include app:graph="@navigation/home_nav_graph" />
</navigation>
```

### Sub-graph de ejemplo (auth)

```xml
<navigation android:id="@+id/auth_nav_graph"
    app:startDestination="@id/loginFragment">

    <fragment
        android:id="@+id/loginFragment"
        android:name="com.example.app.ui.auth.LoginFragment">

        <action
            android:id="@+id/action_auth_to_home"
            app:destination="@id/home_nav_graph"
            app:popUpTo="@id/auth_nav_graph"
            app:popUpToInclusive="true" />
    </fragment>
</navigation>
```

> Separar en varios graphs es **organizativo**: todos comparten el mismo back stack del `NavController`. No son pilas independientes.

---

## Paso 4 — Pasar datos entre Fragments (Bundle)

```java
// Enviar (ej: LoginFragment)
Bundle args = new Bundle();
args.putString("username", username);

Navigation.findNavController(view)
        .navigate(R.id.action_auth_to_home, args);
```

```java
// Recibir (ej: HomeFragment)
String username = getArguments() != null
        ? getArguments().getString("username", "")
        : "";
```

> La key (`"username"`) tiene que coincidir exacto en ambos lados — igual que con `putExtra`/`getStringExtra` en [[Intents]].

---

## Paso 5 — Navegar y volver con NavController

```java
NavController nav = Navigation.findNavController(view);

// Navegar hacia adelante
nav.navigate(R.id.action_home_to_detail);

// Volver atrás (equivalente al botón "atrás")
navController.popBackStack();

// Volver hasta un destino específico
nav.popBackStack(R.id.homeFragment, false);
//                                   ↑ false = no elimina homeFragment del stack
//                                     true  = lo elimina también
```

### NavController en MainActivity

```java
NavHostFragment navHostFragment = (NavHostFragment) getSupportFragmentManager()
        .findFragmentById(R.id.nav_host_fragment);

navController = navHostFragment.getNavController();

@Override
public boolean onSupportNavigateUp() {
    return navController.navigateUp() || super.onSupportNavigateUp();
}
```

---

## popUpTo y popUpToInclusive — controlar el back stack

| Atributo | Qué hace |
|---|---|
| `popUpTo` | Elimina destinos del stack **hasta llegar** al indicado |
| `popUpToInclusive` | Si es `true`, el destino indicado **también** se elimina |

```
Stack: [ Login | Home | Detail ]
navigate con popUpTo="loginFragment"
Resultado: [ Login | NuevoDestino ]
```

| Caso | popUpTo | inclusive | Resultado |
|---|---|---|---|
| Login exitoso | `auth_nav_graph` | `true` | Login se borra, no se puede volver con "atrás" |
| Logout | `nav_graph` | `true` | Stack completo limpio, solo queda Login |
| Ver detalle | — | — | Se apila normal, "atrás" vuelve al anterior |

### Ejemplo — Logout (limpiar todo el stack)

```java
NavOptions navOptions = new NavOptions.Builder()
        .setPopUpTo(R.id.nav_graph, true)  // elimina todo hasta la raíz
        .build();

Navigation.findNavController(view)
        .navigate(R.id.auth_nav_graph, null, navOptions);
```

---

## Resumen de conceptos

| Concepto | Para qué sirve |
|---|---|
| `NavHost` / `NavHostFragment` | Contenedor visual (`FragmentContainerView`) |
| `NavGraph` | Mapa de destinos y acciones en XML |
| `NavController` | Ejecuta las navegaciones y maneja el back stack |
| `Bundle` | Pasa datos entre Fragments |
| `popBackStack()` | Vuelve atrás manualmente (equivale al botón "atrás") |
| `popUpTo` / `popUpToInclusive` | Controla qué se borra del historial al navegar |
| `<include>` | Compone graphs chicos dentro del raíz |

---

## Relacionado

- [[Clase 03 - Fragments y Navigation Component]]
- [[Fragment]]
- [[Intents]]
- [[Actividad]]
- [[Retrofit - Consumo de API REST]]
- [[Gradle]]
