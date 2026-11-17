---
tags: [repaso, tpo, ronda, punto-5, android, java, desarrollo-aplicaciones-1]
aliases: [Repaso Punto 5, Publicar Artículo, Wizard de publicar]
---

# Repaso Punto 5 — Publicar un Artículo

> Volver al [[00 - Índice Repaso 5 y 7]]

**Código leído:** rama `main` (las ramas `feature/publicar-articulo` y `feature/mis-publicaciones` ya están mergeadas).

---

## 1. Qué hace la funcionalidad

- Carga **guiada en 5 pasos** (un Fragment por paso).
- Si salís a mitad de carga, el **borrador queda en Room** y lo retomás donde lo dejaste.
- **"Mis publicaciones"** lista tus publicaciones con su estado y permite **pausar / reactivar**.

| Paso | Qué se carga | Fragment |
|---|---|---|
| 0 | Fotos | `PublicarFotosFragment` |
| 1 | Título y descripción | `PublicarTituloDescripcionFragment` |
| 2 | Categoría y estado | `PublicarCategoriaEstadoFragment` |
| 3 | Precio, zona y dirección | `PublicarPrecioZonaFragment` |
| 4 | Resumen y publicar | `PublicarResumenFragment` |

---

## 2. Mapa del código

Ruta base: `app/src/main/java/com/example/tpo/`

### Wizard de publicar

| Archivo | Rol |
|---|---|
| `ui/publicar/PublicarPasoFragment` | Clase base. Solo busca el ViewModel compartido |
| `ui/publicar/PublicarArticuloViewModel` | **Estado compartido** de los 5 pasos |
| `ui/publicar/Publicar*Fragment` (5) | Una pantalla por paso |
| `ui/publicar/FotoSeleccionadaAdapter` | Adapter del `RecyclerView` de fotos |
| `res/navigation/nav_graph_publicar.xml` | Sub-grafo con los 5 destinos |
| `res/navigation/nav_graph.xml` | Lo incluye con `<include>` y tiene la acción `action_home_to_publicar` |
| `model/BorradorPublicacion` | Objeto **mutable** con los datos que se van cargando |

### Borrador (guardar y retomar)

| Archivo | Rol |
|---|---|
| `data/BorradorRepository` | Interfaz: `guardar`, `obtener`, `borrar` |
| `data/BorradorRepositoryLocal` | Implementación con Room. Usa hilo aparte |
| `data/local/BorradorPublicacionDao` | Queries: `guardar` (REPLACE), `obtener`, `borrar` |
| `data/local/BorradorPublicacionEntity` | Tabla `borrador_publicacion` (una sola fila, `id = 1`) |
| `data/local/Converters` | Convierte `List<Uri>` ↔ texto (JSON) para Room |

### Publicar (llamar a la API)

| Archivo | Rol |
|---|---|
| `data/MisPublicacionesRepository` | Interfaz: `publicar`, `listar`, `pausar`, `reactivar` |
| `data/MisPublicacionesRepositoryApi` | Implementación con Retrofit (multipart con fotos) |
| `di/PublicarEntryPoint` | Puerta de Hilt para el ViewModel |
| `di/RepositoryModule` | Provee los repositorios de Hilt |

### Mis publicaciones

| Archivo | Rol |
|---|---|
| `ui/mispublicaciones/MisPublicacionesFragment` | Pantalla. Estados: cargando, vacío, error, lista |
| `ui/mispublicaciones/MiPublicacionAdapter` | Fila con botón Pausar / Reactivar |
| `model/MiPublicacion`, `model/EstadoPublicacion` | Modelo y estado (ACTIVA, PAUSADA, VENDIDA) |

---

## 3. Flujo paso a paso

### A) Abrir el wizard

1. En el Home tocás el **FAB**.
2. Se ejecuta `action_home_to_publicar` y entra al sub-grafo.
3. Arranca `PublicarFotosFragment` (destino inicial).
4. Se crea el `PublicarArticuloViewModel` con scope del **grafo** `nav_graph_publicar`.
5. El ViewModel llama a `cargarBorradorGuardado()`.
6. Room responde (en otro hilo) y el resultado vuelve al **Main Thread**.
7. Si había borrador → se usa. Si no → `new BorradorPublicacion()`.

### B) Retomar el borrador

1. `PublicarFotosFragment` observa `getCargandoBorrador()`.
2. Cuando termina de cargar, llama a `saltarAlPasoGuardadoSiCorresponde()`.
3. Si `borrador.getPaso() > 0`, hace `navigate(destinoDelPaso(paso))`.
4. Una bandera (`yaEvaluoElPasoGuardado`) evita que salte dos veces.

### C) Completar cada paso

1. El Fragment escribe sus campos **directo** sobre el objeto del borrador.
2. Valida (campos obligatorios).
3. Si es válido → `viewModel.guardarPaso(n)`.
4. `guardarPaso` hace `setPaso(n)`, `borradorRepository.guardar(...)` y `borrador.setValue(...)`.
5. Después navega al paso siguiente.

| Fragment | Llama a |
|---|---|
| Fotos | `guardarPaso(1)` |
| Título y descripción | `guardarPaso(2)` |
| Categoría y estado | `guardarPaso(3)` |
| Precio y zona | `guardarPaso(4)` |

> **Ojo:** el número que se guarda es **el paso al que vas a ir**, no el actual.

### D) Elegir fotos

```java
// 1) Si ya hay permiso → abre galería. Si no → lo pide primero.
private void agregarFoto() {
    String permiso = permisoDeGaleria();
    if (ContextCompat.checkSelfPermission(requireContext(), permiso)
            == PackageManager.PERMISSION_GRANTED) {
        elegirFoto.launch("image/*");     // abre la galería
    } else {
        pedirPermisoGaleria.launch(permiso); // pide el permiso
    }
}
```

1. Se pide el permiso (`RequestPermission`).
2. Si lo conceden → `GetContent` abre la galería.
3. Vuelve una `Uri` (**una foto por vez**).
4. Se llama a `takePersistableUriPermission` para poder leerla después.
5. Se agrega a la lista del borrador y se muestra en el `RecyclerView`.

### E) Publicar

1. En el Resumen tocás **Publicar**.
2. `viewModel.publicar(callback)` revisa `estaCompleto()`.
3. `publicando = true` (muestra el progreso, deshabilita botones).
4. `MisPublicacionesRepositoryApi.publicar(...)`:
   - En un **hilo aparte** lee los bytes de cada foto con `ContentResolver`.
   - Arma las partes `multipart` y el resto de campos como texto.
   - Llama a `api.crearPublicacion(...).enqueue(...)`.
5. Si sale bien → `borradorRepository.borrar()` y vuelve al Home con `popBackStack(R.id.homeFragment, false)`.
6. Si falla → Snackbar con el error. **El usuario no pierde nada.**

### F) Mis publicaciones

1. Entrás desde el Home (`action_home_to_misPublicaciones`).
2. `onViewCreated` → `cargar()` → `repositorio.listar(...)`.
3. El adapter muestra cada fila. El botón depende del estado:

| Estado | Botón |
|---|---|
| ACTIVA | **Pausar** |
| PAUSADA | **Reactivar** |
| VENDIDA | Sin botón |

4. Al tocar el botón → `repositorio.pausar(id)` o `reactivar(id)`.
5. Si sale bien → `adapter.actualizarEstado(id, nuevoEstado)` (cambia esa fila, no recarga todo).

---

## 4. Conceptos de Java/Android usados

> Si dice **⚠️ No hay nota tuya**, el concepto no está en tus apuntes. Lo expliqué acá y no inventé ningún link.

### Fragment y ciclo de vida
- **Qué es:** una pantalla dentro de la única Activity.
- **Regla clave:** las vistas se buscan en `onViewCreated` y se ponen en `null` en `onDestroyView`.
- **Dónde:** todos los `Publicar*Fragment` y `MisPublicacionesFragment`.
- **Nota:** [[Fragment]] · [[Clase 03 - Fragments y Navigation Component]]

### Navigation Component y sub-grafo
- **Qué es:** el `NavController` navega entre destinos definidos en un XML.
- **Acá se usa:**
  - `<include>` para meter el sub-grafo en `nav_graph.xml`.
  - `navigate(R.id.action_...)` para avanzar.
  - `navigateUp()` para volver.
  - `popBackStack(R.id.homeFragment, false)` para volver al Home al terminar.
- **Dónde:** `nav_graph_publicar.xml`, `PublicarResumenFragment.publicar()`.
- **Nota:** [[Navigation Component]]
- ⚠️ **`<include>` de sub-grafos** y **`getBackStackEntry(...)`** no aparecen explicados en tu nota (solo se ve el `<include>` como ejemplo de grafo raíz).

### ViewModel compartido entre Fragments
- **Qué es:** un objeto que **guarda el estado de la pantalla** y sobrevive mientras la pantalla no se destruye.
- **Acá:** los 5 Fragments comparten **un solo** ViewModel porque piden el mismo `ViewModelStoreOwner`: la entrada del grafo.

```java
// En PublicarPasoFragment: scope = el sub-grafo, no el Fragment
NavBackStackEntry entradaDelGrafo = NavHostFragment.findNavController(this)
        .getBackStackEntry(R.id.nav_graph_publicar);
viewModel = new ViewModelProvider(entradaDelGrafo, factory)
        .get(PublicarArticuloViewModel.class);
```

- **Se destruye** cuando salís del wizard completo.
- **Dónde:** `PublicarArticuloViewModel`, `PublicarPasoFragment`.
- ⚠️ **No hay nota tuya de ViewModel.** (Solo aparece el nombre en ejemplos de DI.)

### LiveData / MutableLiveData / observe
- **Qué es:** un dato que **avisa a quien lo observa** cuando cambia.
- **Acá:** `borrador`, `cargandoBorrador`, `publicando`.
- **Se observa con** `getViewLifecycleOwner()` para que deje de observar si la vista muere.
- **`setValue`** solo desde el Main Thread.
- **Dónde:** `PublicarArticuloViewModel`; `observe(...)` en `PublicarFotosFragment` y `PublicarResumenFragment`.
- ⚠️ **No hay nota tuya de LiveData.**

### Room: Entity, DAO, TypeConverter
- **Entity:** la clase que es una tabla (`BorradorPublicacionEntity`).
- **DAO:** la interfaz con las queries (`BorradorPublicacionDao`).
- **`@Insert(onConflict = REPLACE)` + `id` fijo** → siempre hay **una sola fila**.
- **TypeConverter:** Room no sabe guardar `List<Uri>`, así que `Converters` la pasa a texto y viceversa.
- **Enums como `String`:** se guarda `name()` para que un valor viejo no rompa la carga.
- **Dónde:** `data/local/*`, `BorradorRepositoryLocal`.
- **Nota:** [[Room]]
- ⚠️ **`@TypeConverter` y `OnConflictStrategy`**: tu nota Room no los desarrolla (hay que confirmar).

### Hilos: ExecutorService + Handler
- **Problema:** Room **prohíbe** consultas en el Main Thread.
- **Solución:** `Executors.newSingleThreadExecutor()` corre la query; `Handler(Looper.getMainLooper()).post(...)` devuelve el resultado al Main Thread.

```java
executor.execute(() -> {                       // hilo aparte
    BorradorPublicacionEntity entity = dao.obtener();
    handlerPrincipal.post(() ->                // vuelve al Main Thread
            callback.onExito(desdeEntity(entity)));
});
```

- **Dónde:** `BorradorRepositoryLocal`, `MisPublicacionesRepositoryApi` (para leer las fotos).
- **Nota:** [[Room]] (sección "Room y el hilo principal").
- ⚠️ **`Handler` / `Looper`** no están explicados en tus notas.

### ActivityResultLauncher (galería + permiso)
- **Qué es:** forma moderna de lanzar algo y recibir el resultado.
- **Acá:** dos launchers.
  - `RequestPermission` → pide el permiso en runtime.
  - `GetContent` → abre la galería y devuelve una `Uri`.
- **Se declaran como campo** (antes de que el Fragment esté `STARTED`).
- **Dónde:** `PublicarFotosFragment` (`elegirFoto`, `pedirPermisoGaleria`).
- **Nota:** [[ActivityResultLauncher]] · [[Clase 06 - Biometría y Seguridad en el Dispositivo]]

### Permisos en runtime
- **Declarar en el Manifest no alcanza**: hay que pedirlo.
- **Depende de la versión de Android:**
  - API 33+ → `READ_MEDIA_IMAGES`
  - Antes → `READ_EXTERNAL_STORAGE`
- **Dónde:** `PublicarFotosFragment.permisoDeGaleria()`.
- **Nota:** [[AndroidManifest]] · [[ActivityResultLauncher]]

### Uri y ContentResolver
- **`Uri`:** dirección de un recurso (acá `content://...` de la galería).
- **`ContentResolver`:** el que te deja abrir ese recurso (`openInputStream`).
- **`takePersistableUriPermission`:** pide que el permiso sobre esa foto **dure** aunque cierres la app. Sin eso, el borrador guardaría una `Uri` que ya no se puede leer.
- **Dónde:** `PublicarFotosFragment`, `MisPublicacionesRepositoryApi.leerFotos`.
- ⚠️ **No hay nota tuya de `Uri`/`ContentResolver`.** Lo más cercano es [[Almacenamiento de Archivos (Files)]] (otro tema) y [[Proveedor de Contenido]].

### Retrofit y multipart
- **Qué es:** Retrofit convierte una interfaz Java en llamadas HTTP.
- **`enqueue`** = llamada asincrónica, la respuesta vuelve al Main Thread.
- **Multipart:** para mandar fotos + campos de texto en una sola request (`MultipartBody.Part`, `RequestBody`).
- **Dónde:** `MisPublicacionesRepositoryApi.publicar`, `PublicacionesApi.crearPublicacion`.
- **Nota:** [[Retrofit - Consumo de API REST]]
- ⚠️ **`@Multipart` / `@Part`** no figuran en tu nota (sí `@Body`, `@Path`, `enqueue`).

### Patrón Repository + Callback
- **Idea:** la UI depende de una **interfaz** (`MisPublicacionesRepository`), no de Retrofit.
- **`RepositorioCallback<T>`:** tiene `onExito` y `onError`. Imita el `Callback` de Retrofit.
- **Ventaja:** cambiar la fuente de datos sin tocar el Fragment.
- **Dónde:** `BorradorRepository`, `MisPublicacionesRepository` y sus implementaciones.
- **Notas parciales:** [[Retrofit - Consumo de API REST]] (Callback), [[Inyección de Dependencias (DI)]] (depender de abstracciones).
- ⚠️ **El patrón Repository como tal no está en tus notas.**

### Hilt: @Inject, @AndroidEntryPoint y @EntryPoint
- **`@AndroidEntryPoint` + `@Inject`:** `MisPublicacionesFragment` recibe el repositorio sin hacer `new`.
- **`@EntryPoint`:** el ViewModel **no es `@HiltViewModel`**, así que no puede usar `@Inject`. Pide el repo con `EntryPointAccessors.fromApplication(...)`.
- **Dónde:** `MisPublicacionesFragment`, `PublicarEntryPoint`, `PublicarArticuloViewModel`.
- **Notas:** [[Hilt]] · [[Inyección de Dependencias (DI)]] · [[Clase 04 - Inyección de Dependencias y Storage]]
- ⚠️ **`@EntryPoint` y `@HiltViewModel` no están desarrollados en tus notas.**

### Singleton manual
- **Qué es:** una sola instancia con `getInstancia()`.
- **Dónde:** `BorradorRepositoryLocal.getInstancia(context)`, `SesionUsuario`.
- **Nota:** [[Clase 04 - Inyección de Dependencias y Storage]] (el `RetrofitClient` manual es el mismo patrón).

### RecyclerView, Adapter y ViewHolder
- **Adapter:** 3 métodos → `onCreateViewHolder`, `onBindViewHolder`, `getItemCount`.
- **Listener por interfaz:** `MiPublicacionAdapter.OnAccionClickListener` (el Fragment lo implementa).
- **`notifyDataSetChanged()`** al reemplazar todo; **`notifyItemChanged(i)`** al cambiar una fila.
- **Dónde:** `MiPublicacionAdapter`, `FotoSeleccionadaAdapter`.
- **Nota:** [[RecyclerView]]

### Enum
- **Qué es:** un tipo con valores fijos (`Categoria`, `EstadoArticulo`, `Zona`, `EstadoPublicacion`).
- **Se usa:** `name()` para guardar/enviar y `Enum.valueOf(...)` para recuperar.
- **Dónde:** `BorradorRepositoryLocal.valorEnumOrNull`, `MisPublicacionesRepositoryApi`.
- ⚠️ **No hay nota tuya de enums (Java básico).**

### Lambdas y method references
- **Lambda:** `v -> validarYContinuar()`
- **Method reference:** `this::quitarFoto`, `dao::borrar`
- **Dónde:** `PublicarFotosFragment`, `BorradorRepositoryLocal`.
- ⚠️ **No hay nota tuya.**

### Generics
- **Ejemplo:** `<T extends Enum<T>> T valorEnumOrNull(Class<T> tipo, String nombre)` sirve para cualquier enum.
- **Dónde:** `BorradorRepositoryLocal`.
- ⚠️ **No hay nota tuya.**

### Material: Snackbar, TextInputLayout, AutoCompleteTextView
- **Snackbar:** mensaje corto abajo.
- **TextInputLayout:** campo con `setError(...)`.
- **AutoCompleteTextView + ArrayAdapter:** el dropdown de zonas.
- **Dónde:** `PublicarPrecioZonaFragment`, `PublicarResumenFragment`.
- **Relacionado:** [[ListView y Adapters]] (`ArrayAdapter`).
- ⚠️ **No hay nota tuya de Material Components.**

---

## 5. Preguntas probables de defensa

**¿Por qué usaste un ViewModel compartido y no pasaste datos por Bundle?**
Porque son 5 pantallas con los mismos datos. El ViewModel con scope al grafo vive mientras estés en el wizard y se destruye al salir.

**¿Por qué el scope es el grafo y no el Fragment?**
Si fuera del Fragment, cada paso tendría su propio ViewModel vacío.

**¿Cómo se retoma el borrador?**
Cada "Siguiente" guarda el borrador en Room con el número de paso. Al abrir el wizard, `PublicarFotosFragment` lee `getPaso()` y navega directo a ese destino.

**¿Por qué Room y no SharedPreferences o DataStore?**
El borrador tiene varios campos y una lista de fotos. Es dato estructurado. Con Room usás una Entity y un DAO.

**¿Por qué una sola fila en la tabla?**
El enunciado pide un borrador activo, no un historial. `id` fijo + `REPLACE` lo garantiza.

**¿Por qué los enums se guardan como String y no con TypeConverter?**
Si borrás o renombrás un valor del enum, un borrador viejo no rompe la carga. Ese campo queda vacío.

**¿Por qué usás un Executor y un Handler?**
Room no deja consultar en el Main Thread. El Executor corre la query y el Handler devuelve el resultado al Main Thread.

**¿Por qué hacen falta dos launchers para las fotos?**
Pedir el permiso y elegir la foto son dos acciones. El segundo solo se lanza si el primero fue concedido.

**¿Para qué `takePersistableUriPermission`?**
La `Uri` se guarda en Room. Sin permiso persistente, al reabrir la app no podrías leer la foto.

**¿Cómo se suben las fotos?**
Se leen los bytes con `ContentResolver` (en un hilo aparte) y se mandan como `multipart/form-data` junto con los demás campos.

**¿Por qué la lectura de fotos va en un Executor y el `enqueue` no?**
Leer archivos es I/O y bloquea. `enqueue` de Retrofit ya es asincrónico y responde en el Main Thread.

**¿Qué pasa si falla la publicación?**
Se muestra el error y el borrador **no se borra**. El usuario reintenta.

**¿Por qué el ViewModel usa `@EntryPoint`?**
No es `@HiltViewModel`, entonces no puede recibir `@Inject`. El `EntryPoint` es la forma de Hilt de entregar un binding a una clase que Hilt no crea.

**¿Cómo funciona pausar / reactivar?**
El adapter avisa al Fragment por una interfaz. El Fragment llama al repositorio. Si la API responde OK, actualiza esa fila en el adapter.

**¿Por qué `onDestroyView` pone todo en `null`?**
La vista se destruye pero el Fragment sigue vivo. Si guardás referencias, hay memory leak. Además los callbacks usan `null` para saber que la vista ya no existe.

---

## 6. Dudas o cosas raras del código

> Esto lo **vi leyendo**. No lo probé corriendo la app.

### Probablemente incompleto frente al enunciado

- **"Dirección exacta (coordenadas)":** hoy es **texto libre**.
  - `BorradorPublicacion` lo dice: "dirección o coordenadas pegadas de Google Maps".
  - `MapaUtils.tieneDireccion` solo chequea que no esté vacío.
  - **No hay mapa, ni selector, ni validación de coordenadas.**
- **Fotos de a una:** `GetContent` devuelve una sola `Uri`. No hay selección múltiple en una pasada.

### Posibles bugs / cosas frágiles

- **Cambios de fotos no se guardan hasta "Siguiente".**
  - Agregar o quitar una foto solo modifica el objeto en memoria.
  - Solo `guardarPaso(...)` persiste.
  - Si cerrás la app en el paso de fotos antes de tocar "Siguiente", se pierden.
- **"Atrás" no guarda.** El Javadoc del ViewModel dice "al tocar Siguiente o Atrás", pero las 4 llamadas a `guardarPaso` están solo en "Siguiente".
- **`tieneDatos()` no se usa.** Está en `BorradorPublicacion` y su comentario dice que evita guardar borradores vacíos, pero nadie la llama.
- **Hilo del error de lectura de fotos.** En `MisPublicacionesRepositoryApi.publicar`, si falla la lectura (`IOException`) se llama a `callback.onError(...)` **desde el hilo del Executor**, no desde el Main Thread.
  - El contrato de `RepositorioCallback` dice que siempre es Main Thread.
  - El ViewModel hace `publicando.setValue(false)` ahí → `setValue` desde otro hilo suele tirar excepción.
  - **Es una sospecha: conviene probarlo.**
- **Back después de retomar:** como el salto se hace con `navigate` desde Fotos, el back stack queda `Fotos → paso retomado`. "Atrás" vuelve a Fotos, no al paso anterior.
- **`vendedorId` sale del cliente.** `publicar` manda `SesionUsuario.getUsuarioId()` en el form. En ofertas el usuario sale del token. Es una inconsistencia (no revisé el backend de publicaciones).
- **Borrador global.** La fila es una sola para el dispositivo, no por usuario.
  - `PerfilFragment.cerrarSesion()` no referencia el borrador.
  - Entonces, **al cerrar sesión el borrador parece quedar en el dispositivo**.
  - Otro usuario que inicie sesión ahí podría encontrar el borrador del anterior. (Lo deduzco leyendo; no lo probé.)

### Mis publicaciones

- **La foto es siempre un placeholder.** El comentario del adapter lo dice: no hay librería de imágenes. No encontré Glide/Coil/Picasso en `build.gradle` ni en `libs.versions.toml`. (Tu nota Retrofit sí explica Glide.)
- **Carga solo al entrar** (`onViewCreated`). No recarga en `onResume`.
- **No revisé** si el backend valida las transiciones (por ejemplo, pausar una VENDIDA).
- **El adapter no usa `DiffUtil`.** Está justificado en el comentario (lista chica).

### Código sobrante / documentación desfasada

- **Existe `data/MisPublicacionesRepositoryLocal` (Room), pero nadie la usa.**
  - `RepositoryModule` provee solo `MisPublicacionesRepositoryApi`.
  - El Javadoc de la interfaz dice "la única implementación es `...Api`". Contradice al archivo `Local`.
  - Si te preguntan, es una versión vieja que quedó sin borrar. **Confirmalo con tu equipo.**
- El comentario de `MisPublicacionesFragment` habla de "mock o Retrofit", pero hoy solo se usa la implementación `Api`.
