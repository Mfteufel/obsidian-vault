---
tags: [android, listview, adapter, ui, desarrollo-aplicaciones-1]
aliases: [ListView, Adapter, ArrayAdapter, Listas Android]
---

# CC_ListView y Adapters

> Una **ListView** muestra una colección de ítems scrolleable. El **Adapter** es el intermediario que convierte los datos en Views.

## El flujo

```
Datos            Adapter              ListView
─────            ───────              ────────
"Pizza"    →                     →    Pizza
"Sushi"    →   convierte cada    →    Sushi
"Tacos"    →   ítem en una View  →    Tacos
"Burger"   →                     →    Burger

List<String>    ArrayAdapter          pantalla
```

**En una línea:** los datos no saben dibujarse, la ListView no sabe de datos → el Adapter los conecta.

---

## Uso básico

### 1. En el XML

```xml
<ListView
    android:id="@+id/miLista"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />
```

### 2. En la Activity

```java
// Datos
List<String> comidas = Arrays.asList("Pizza", "Sushi", "Tacos", "Burger");

// Vinculamos la ListView
ListView lv = findViewById(R.id.miLista);

// Creamos el Adapter
ArrayAdapter<String> adapter = new ArrayAdapter<>(
    this,
    android.R.layout.simple_list_item_1,  // layout de cada fila
    comidas
);

// Conectamos
lv.setAdapter(adapter);
```

---

## Conceptos clave

| Concepto | Qué es |
|---|---|
| **ArrayAdapter** | El más simple: conecta un array o `List` a la ListView automáticamente |
| **getView()** | El Adapter lo llama por **cada ítem** para crear o reciclar su View |
| **setOnItemClickListener** | Como `onClick` pero para ítems: recibe la **posición** tocada |
| **BaseAdapter** | Adapter custom, cuando cada fila tiene un diseño propio |

---

## Detectar el click en un ítem

```java
lv.setOnItemClickListener((parent, view, position, id) -> {
    String seleccionado = comidas.get(position);
    Toast.makeText(this, "Elegiste: " + seleccionado, Toast.LENGTH_SHORT).show();
});
```

| Parámetro | Qué es |
|---|---|
| `parent` | La ListView completa |
| `view` | La fila tocada |
| `position` | **Índice del ítem** (el que más se usa) |
| `id` | ID de la fila |

---

## Layouts de fila predefinidos

| Layout | Muestra |
|---|---|
| `simple_list_item_1` | Una sola línea de texto |
| `simple_list_item_2` | Título + subtítulo |
| `simple_list_item_checked` | Texto con check |
| `simple_list_item_single_choice` | Radio button |

> Se usan con el prefijo `android.R.layout.` (son del sistema, no tuyos).

---

## Actualizar la lista

```java
comidas.add("Empanadas");
adapter.notifyDataSetChanged();   // avisa que los datos cambiaron
```

> Si no llamás `notifyDataSetChanged()`, la pantalla no se refresca.

---

## Adapter custom (cuando la fila tiene varios campos)

```java
public class ComidaAdapter extends BaseAdapter {
    private List<Comida> items;
    private Context context;

    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        if (convertView == null) {
            convertView = LayoutInflater.from(context)
                    .inflate(R.layout.item_comida, parent, false);
        }

        Comida c = items.get(position);
        TextView tvNombre = convertView.findViewById(R.id.tvNombre);
        TextView tvPrecio = convertView.findViewById(R.id.tvPrecio);

        tvNombre.setText(c.getNombre());
        tvPrecio.setText("$" + c.getPrecio());

        return convertView;
    }
    // getCount(), getItem(), getItemId() también son obligatorios
}
```

> El `if (convertView == null)` es **reciclado de vistas**: reutiliza las filas que salen de pantalla en vez de crear una nueva cada vez.

---

## Errores típicos

| Síntoma | Causa |
|---|---|
| Lista vacía | Faltó `setAdapter()` |
| No se actualiza | Faltó `notifyDataSetChanged()` |
| `NullPointerException` | `findViewById` antes de `setContentView()` |
| Scroll con lag | No se recicla `convertView` en `getView()` |

---

## Relacionado

- [[Clase 02 - Android Primeros Pasos]]
- [[Actividad]]
- [[Android - Estructura Básica y Pasaje de Actividades]]
- [[Intents]]
