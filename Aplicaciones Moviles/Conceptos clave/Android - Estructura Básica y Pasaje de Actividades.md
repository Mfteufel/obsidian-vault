
## Estructura Básica de una Activity

### Método Principal: onCreate()

Toda Activity comienza su ejecución en el método `onCreate()`. Es donde inicializamos los componentes y configuramos la interfaz.

java

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
    // Aquí va el resto de la lógica
}
```

> **📌 Nota:** `onCreate()` solo se ejecuta una vez cuando se crea la Activity.

---

## Vinculación de Componentes Gráficos

### findViewById() - Conectar UI con Código

Para acceder a los componentes gráficos definidos en el XML, usamos `findViewById()`.

java

```java
// Sintaxis básica
TipoComponente objetoLocal = findViewById(R.id.componenteGrafico);

// Ejemplo práctico
Button btnJugar = findViewById(R.id.btn_play);
TextView txtScore = findViewById(R.id.txt_score);
EditText etNombre = findViewById(R.id.et_nombre);
```

**¿Qué pasa acá?**

- `R.id.componenteGrafico` → ID del componente en el XML
- `objetoLocal` → Variable local que referencia ese componente
- Ahora podemos manipularlo desde código: `btnJugar.setText("Jugar")`

---

## Event Listeners - Controlar Eventos

### setOnClickListener() - Escuchar Clics

A los componentes que necesitamos controlar (botones, vistas, etc.) les asignamos un `listener` para detectar eventos.

java

```java
// Sintaxis con clase anónima
btnJugar.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View v) {
        // Código que se ejecuta al hacer clic
        Toast.makeText(MainActivity.this, "¡Botón presionado!", Toast.LENGTH_SHORT).show();
    }
});
```

**Alternativa más limpia (Lambda - Java 8+):**

java

```java
btnJugar.setOnClickListener(v -> {
    Toast.makeText(MainActivity.this, "¡Botón presionado!", Toast.LENGTH_SHORT).show();
});
```

### Otros Listeners Comunes

|Listener|Evento|Método|
|---|---|---|
|`OnClickListener`|Click en botón|`onClick()`|
|`OnLongClickListener`|Click prolongado|`onLongClick()`|
|`OnTextChangedListener`|Cambio en EditText|`onTextChanged()`|
|`OnFocusChangeListener`|Ganancia/pérdida de foco|`onFocusChange()`|

---

## Pasaje Entre Actividades

### Intent - Vincular Actividades

Un `Intent` es un objeto que contiene la "intención" de pasar de una Activity a otra. Crea un nuevo elemento en el stack de Android.

java

```java
// Crear un Intent
Intent aux = new Intent(MainActivity.this, GameActivity.class);

// Pasar el control a la nueva Activity
startActivity(aux);
```

**¿Qué sucede internamente?**

```
Stack de Actividades:
┌──────────────┐
│ GameActivity │  ← Nueva (arriba del stack)
├──────────────┤
│ MainActivity │  ← Anterior (sigue en el stack)
└──────────────┘
```

- La nueva Activity se coloca **encima** en el stack
- Al presionar el botón **atrás** vuelves a `MainActivity`

---

### Envío de Parámetros (Key-Value)

Puedes pasar datos entre Activities usando el Intent.

#### **Método: putExtra()**

java

```java
// En MainActivity (Emisor)
Intent aux = new Intent(MainActivity.this, GameActivity.class);

// Agregar parámetros
aux.putExtra("nombreJugador", "Marco");
aux.putExtra("puntuacion", 1500);
aux.putExtra("esJugadorNuevo", true);

startActivity(aux);
```

#### **Métodos de Recuperación (Receptor)**

java

```java
// En GameActivity - obtener los parámetros
public class GameActivity extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_game);
        
        // Recuperar parámetros
        String nombre = getIntent().getStringExtra("nombreJugador");
        int puntos = getIntent().getIntExtra("puntuacion", 0);
        boolean esNuevo = getIntent().getBooleanExtra("esJugadorNuevo", false);
        
        // Usar los datos
        Toast.makeText(this, "Bienvenido " + nombre, Toast.LENGTH_SHORT).show();
    }
}
```

### Tabla de Métodos putExtra / getExtra

|Tipo de Dato|putExtra()|getExtra()|
|---|---|---|
|String|`putExtra(key, String)`|`getStringExtra(key)`|
|int|`putExtra(key, int)`|`getIntExtra(key, default)`|
|boolean|`putExtra(key, boolean)`|`getBooleanExtra(key, default)`|
|long|`putExtra(key, long)`|`getLongExtra(key, default)`|
|double|`putExtra(key, double)`|`getDoubleExtra(key, default)`|
|Bundle (objeto)|`putExtra(key, Bundle)`|`getBundleExtra(key)`|

---

## Flujo Completo: Ejemplo Práctico

### MainActivity.java

java

```java
public class MainActivity extends AppCompatActivity {
    private Button btnIniciarJuego;
    private EditText etNombre;
    
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        
        // 1. Vinculamos componentes
        btnIniciarJuego = findViewById(R.id.btn_iniciar);
        etNombre = findViewById(R.id.et_nombre);
        
        // 2. Asignamos listener
        btnIniciarJuego.setOnClickListener(v -> {
            // 3. Obtenemos datos
            String nombre = etNombre.getText().toString();
            
            // 4. Creamos Intent
            Intent intent = new Intent(MainActivity.this, GameActivity.class);
            
            // 5. Pasamos parámetros
            intent.putExtra("jugador", nombre);
            intent.putExtra("nivel", 1);
            
            // 6. Iniciamos Activity
            startActivity(intent);
        });
    }
}
```

### GameActivity.java

java

```java
public class GameActivity extends AppCompatActivity {
    private TextView txtBienvenida;
    
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_game);
        
        // Vinculamos
        txtBienvenida = findViewById(R.id.txt_bienvenida);
        
        // Recuperamos parámetros
        String jugador = getIntent().getStringExtra("jugador");
        int nivel = getIntent().getIntExtra("nivel", 1);
        
        // Usamos los datos
        txtBienvenida.setText("¡Bienvenido " + jugador + "! Nivel: " + nivel);
    }
}
```

---

## Botón Atrás (Back Button)

Android maneja automáticamente el botón **atrás** eliminando la Activity actual del stack y mostrando la anterior.

```
Usuario presiona atrás:
┌──────────────┐
│ GameActivity │  ← Se elimina del stack
├──────────────┤
│ MainActivity │  ← Vuelve al frente
└──────────────┘
```

> **💡 Tip:** No necesitas código especial para el botón atrás. Android lo maneja automáticamente.

---

## Resumen Rápido

|Concepto|Código|Propósito|
|---|---|---|
|**Inicializar Activity**|`onCreate()`|Punto de entrada|
|**Acceder a componente**|`findViewById()`|Vincular UI|
|**Escuchar evento**|`setOnClickListener()`|Detectar acciones|
|**Pasar a otra Activity**|`Intent` + `startActivity()`|Cambiar pantalla|
|**Enviar datos**|`putExtra()`|Pasar parámetros|
|**Recibir datos**|`getStringExtra()` etc.|Obtener parámetros|