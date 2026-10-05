---
tags: [android, fragment, ui, ciclo-de-vida, desarrollo-aplicaciones-1]
aliases: [Fragment, Fragments, onCreateView, onViewCreated]
---

# CC_Fragment

> Un **Fragment** es una porción reutilizable de interfaz que vive **dentro** de una Activity. A diferencia de una Activity, un Fragment **no puede existir solo** — siempre necesita una Activity que lo contenga.

## Por qué existen

Con [[Actividad|Activities]] puras, cada pantalla es una Activity independiente con su propio ciclo de vida y su entrada en el [[AndroidManifest]]. Funciona, pero:

- No podés mostrar dos pantallas "a la vez" (ej: lista + detalle en tablet)
- Cada pantalla nueva = una Activity nueva declarada en el Manifest
- Reutilizar una pantalla en otro contexto es incómodo

**Fragments** resuelven esto: son piezas de UI que **una sola Activity** puede mostrar, cambiar o combinar.

```
Activity (MainActivity)
    │
    └── FragmentContainerView  ← "el contenedor"
            │
            ├── LoginFragment     ← pantalla de login
            ├── HomeFragment      ← pantalla de bienvenida
            └── DetailFragment    ← pantalla de detalle
```

> Google recomienda **Single Activity Architecture** desde 2019: una sola Activity, todas las pantallas son Fragments.

---

## Ciclo de vida (los 4 callbacks del día a día)

| # | Método | Qué pasa | Qué podés hacer |
|---|---|---|---|
| 1 | `onCreate()` | Se crea el fragment | Inicializar variables y argumentos (`Bundle`). **Todavía no hay vistas** |
| 2 | `onCreateView()` | Se infla el layout | Solo devolver la View — **no** buscar vistas todavía |
| 3 | `onViewCreated()` | El layout ya existe | Acá sí: `findViewById()`, `setOnClickListener()`, lógica de UI |
| 4 | `onDestroyView()` | Se destruye la vista | Limpiar referencias a vistas para evitar **memory leaks** |

```java
@Override
public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    // Inicializar variables y argumentos recibidos
    if (getArguments() != null) {
        String userId = getArguments().getString("userId");
    }
}

@Override
public View onCreateView(LayoutInflater inflater, ViewGroup container,
                         Bundle savedInstanceState) {
    // Solo inflar — no buscar vistas
    return inflater.inflate(R.layout.fragment_login, container, false);
}

@Override
public void onViewCreated(View view, Bundle savedInstanceState) {
    super.onViewCreated(view, savedInstanceState);
    // Acá sí podés usar view.findViewById(...)
    Button btnIngresar = view.findViewById(R.id.btnIngresar);
}

@Override
public void onDestroyView() {
    super.onDestroyView();
    // Limpiar referencias a vistas (evita memory leaks)
}
```

> Es el equivalente al `onCreate()` de una Activity, pero partido en varios pasos.

---

## Los 5 componentes de un Fragment

| Componente | Qué es |
|---|---|
| **Clase Fragment** | Define la lógica del componente. Similar a Activity, pero más liviana |
| **Layout XML** | Archivo de interfaz que se infla dentro del Fragment (ej: `fragment_home.xml`) |
| **FragmentManager** | Objeto que gestiona los Fragments dentro de la Activity: `add`, `replace`, `remove` |
| **FragmentTransaction** | Operaciones sobre los fragments: `add()`, `replace()`, `remove()`, `commit()` |
| **FragmentContainerView** | Contenedor en el layout de la Activity donde se insertan los Fragments |

> Con [[Navigation Component]] no manejás `FragmentManager`/`FragmentTransaction` a mano — el `NavController` lo hace por vos.

---

## Comparación con Activity

| | **Activity** | **Fragment** |
|---|---|---|
| ¿Existe solo? | Sí | No, necesita una Activity contenedora |
| Ciclo de vida | Independiente | Depende de la Activity |
| Se declara en | [[AndroidManifest]] | `nav_graph.xml` |
| Inicializar vistas | `onCreate()` | `onViewCreated()` |
| Navegar | `startActivity()` | `NavController.navigate()` |

> Detalle completo de navegación entre Fragments en [[Navigation Component]]

---

## Ejemplo real: LoginFragment

```java
public class LoginFragment extends Fragment {

    @Nullable
    @Override
    public View onCreateView(@NonNull LayoutInflater inflater,
                             @Nullable ViewGroup container,
                             @Nullable Bundle savedInstanceState) {
        return inflater.inflate(R.layout.fragment_login, container, false);
    }

    @Override
    public void onViewCreated(@NonNull View view, @Nullable Bundle savedInstanceState) {
        super.onViewCreated(view, savedInstanceState);

        EditText etNombre = view.findViewById(R.id.etNombre);
        Button btnIngresar = view.findViewById(R.id.btnIngresar);

        btnIngresar.setOnClickListener(v -> {
            String username = etNombre.getText().toString().trim();

            Bundle args = new Bundle();
            args.putString("username", username);

            Navigation.findNavController(view)
                    .navigate(R.id.action_auth_to_home, args);
        });
    }
}
```

---

## Errores típicos

| Síntoma | Causa |
|---|---|
| `NullPointerException` en la vista | Buscaste la vista en `onCreateView()` en vez de `onViewCreated()` |
| Crash al girar pantalla | Fragment sin contenedor válido |
| El Fragment no aparece | Falta registrarlo en el `nav_graph.xml` |
| Memory leak | No limpiaste referencias a vistas en `onDestroyView()` |

---

## Relacionado

- [[Clase 03 - Fragments y Navigation Component]]
- [[Navigation Component]]
- [[Actividad]]
- [[RecyclerView]]
- [[Retrofit - Consumo de API REST]]
