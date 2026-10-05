---
tags: [android, recyclerview, adapter, viewholder, ui, desarrollo-aplicaciones-1]
aliases: [RecyclerView, ViewHolder, LayoutManager, Adapter RecyclerView]
---

# CC_RecyclerView

> Muestra listas grandes de forma eficiente: **solo crea las vistas que entran en pantalla** y las **recicla** cuando salen. Es el reemplazo moderno de [[ListView y Adapters|ListView]].

## El problema que resuelve

Con 1000 ítems, crear 1000 Views en memoria mata la performance. RecyclerView solo mantiene las visibles (ej: 8) y **reutiliza** la View de la fila que sale de pantalla para la que entra.

```
[scroll hacia abajo]
BULBASAUR sale de pantalla → su View se recicla
CHARIZARD entra           → usa la MISMA View, solo cambia el texto
```

Por eso se llama **Recycler**View.

---

## Las 4 piezas

| Pieza | Qué hace |
|---|---|
| **RecyclerView** | El contenedor de UI (en el XML) |
| **LayoutManager** | Define si las filas van vertical, horizontal o en grilla |
| **Adapter** | Sabe cuántos ítems hay y cómo dibujar cada uno |
| **ViewHolder** | Guarda las referencias a las vistas de una fila, para no buscarlas de nuevo |

---

## Paso 1 — RecyclerView en el layout

```xml
<androidx.recyclerview.widget.RecyclerView
    android:id="@+id/rvProductos"
    android:layout_width="0dp"
    android:layout_height="0dp" />
```

## Paso 2 — Layout de una fila (`item_producto.xml`)

```xml
<TextView
    android:id="@+id/tvNombre"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:padding="16dp" />
```

Es solo la "plantilla" vacía de una fila — sin datos.

---

## Paso 3 — ViewHolder

```java
static class ViewHolder extends RecyclerView.ViewHolder {
    TextView tvNombre;

    ViewHolder(View itemView) {
        super(itemView);
        // Se ejecuta UNA SOLA VEZ cuando se crea la fila
        tvNombre = itemView.findViewById(R.id.tvNombre);
    }
}
```

> `findViewById` es lento. El ViewHolder lo llama **una sola vez** por fila creada, no en cada scroll.

---

## Paso 4 — El Adapter (3 métodos obligatorios)

```java
public class ProductoAdapter extends RecyclerView.Adapter<ProductoAdapter.ViewHolder> {

    private final List<Producto> items;

    // ① ¿Cuántas filas hay?
    @Override
    public int getItemCount() {
        return items.size();
    }

    // ② Crear una fila nueva (solo si no hay una para reciclar)
    @Override
    public ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
        View view = LayoutInflater.from(parent.getContext())
                .inflate(R.layout.item_producto, parent, false);
        return new ViewHolder(view);
    }

    // ③ Poner datos en una fila (se llama cada vez que una fila aparece)
    @Override
    public void onBindViewHolder(ViewHolder holder, int position) {
        holder.tvNombre.setText(items.get(position).getNombre());
    }
}
```

---

## Paso 5 — Conectar todo en el Fragment

```java
RecyclerView rvProductos = view.findViewById(R.id.rvProductos);

rvProductos.setLayoutManager(new LinearLayoutManager(requireContext()));

ProductoAdapter adapter = new ProductoAdapter(listaDeProductos);
rvProductos.setAdapter(adapter);
```

---

## El ciclo completo

```
Fragment → setAdapter(adapter)
              ↓
RecyclerView pregunta getItemCount() → 20
              ↓
"mostrame la fila 0" → onCreateViewHolder() infla item_producto.xml
              ↓
onBindViewHolder(holder, 0) → holder.tvNombre.setText("Producto A")
              ↓
[usuario hace scroll, la fila 0 sale de pantalla]
              ↓
onBindViewHolder(holder, 8) ← onCreateViewHolder NO se llama de nuevo
              ↓                  (misma View, solo cambia el texto)
```

---

## Click en un ítem — patrón con interfaz

El Adapter **no debería** saber de navegación. Se le pasa una interfaz que el Fragment implementa:

```java
// Dentro del Adapter
public interface OnItemClickListener {
    void onItemClick(String id);
}

private final OnItemClickListener listener;

public ProductoAdapter(List<Producto> items, OnItemClickListener listener) {
    this.items = items;
    this.listener = listener;
}

@Override
public void onBindViewHolder(ViewHolder holder, int position) {
    Producto p = items.get(position);
    holder.tvNombre.setText(p.getNombre());
    holder.itemView.setOnClickListener(v -> listener.onItemClick(p.getId()));
}
```

```java
// Al crear el Adapter, desde el Fragment
new ProductoAdapter(lista, id -> {
    Bundle args = new Bundle();
    args.putString("productoId", id);
    Navigation.findNavController(view)
            .navigate(R.id.action_list_to_detail, args);
});
```

> Este patrón se usa junto con [[Navigation Component]] para navegar al detalle.

---

## En una línea

> `item_producto.xml` es la plantilla vacía de una fila. El **ViewHolder** la guarda lista para usar. El **Adapter** le pone los datos. El **RecyclerView** la muestra y la recicla.

---

## RecyclerView vs ListView

| | **ListView** | **RecyclerView** |
|---|---|---|
| ViewHolder | Opcional (fácil de olvidar) | **Obligatorio** |
| LayoutManager | Fijo (solo vertical) | Configurable (vertical, horizontal, grilla) |
| Performance | Buena si usás ViewHolder | Mejor por defecto |
| Recomendado hoy | No | **Sí** |

---

## Relacionado

- [[Retrofit - Consumo de API REST]]
- [[Fragment]]
- [[Navigation Component]]
- [[ListView y Adapters]]
