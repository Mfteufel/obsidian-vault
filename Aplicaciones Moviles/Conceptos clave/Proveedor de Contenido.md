**Compartir datos** entre apps de forma segura. Es como una API interna de Android.

kotlin

````kotlin
class MyContentProvider : ContentProvider() {
    override fun query(uri: Uri, projection: Array<String>?, 
                       selection: String?, selectionArgs: Array<String>?, 
                       sortOrder: String?): Cursor? {
        // Retorna datos de forma controlada
        return null
    }
}
```

**Características:**
- Otras apps pueden acceder a tus datos sin permiso total
- Controlas qué datos se exponen
- Se usa para contactos, calendario, fotos, etc.

**Ej real:** Cuando una app de mensajería accede a tus contactos de forma segura.

---

## **Ciclo de Vida de una Activity**

Es importante entender cómo vive y muere una Activity:
```
onCreate() 
    ↓
onStart()
    ↓
onResume() ← App visible e interactuable
    ↓
(Usuario hace algo: minimiza, llama, etc.)
    ↓
onPause() ← App parcialmente visible
    ↓
onStop() ← App no visible
    ↓
(Usuario vuelve o cierra)
    ↓
onDestroy() ← Activity destruida
````

**Cuándo se llama cada uno:**

- **onCreate()**: Se crea la Activity (inicializaciones)
- **onStart()**: La Activity se vuelve visible
- **onResume()**: La Activity está en foreground (el usuario la ve)
- **onPause()**: Se pierde el focus (otra app o diálogo encima)
- **onStop()**: La Activity no es visible
- **onDestroy()**: Se destruye (liberar recursos)