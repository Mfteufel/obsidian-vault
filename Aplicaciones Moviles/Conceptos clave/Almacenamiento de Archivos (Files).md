---
tags: [android, files, storage, almacenamiento, desarrollo-aplicaciones-1]
aliases: [Files, Almacenamiento Interno, Almacenamiento Externo, FileWriter Android]
---

# CC_Almacenamiento de Archivos (Files)

> Para guardar archivos multimedia y documentos: imágenes, PDFs, logs, descargas. Android ofrece dos zonas de almacenamiento distintas: **interno** y **externo**.

## Cuándo usarlo

- Logs de la app
- Imágenes descargadas
- PDFs
- Archivos temporales o caché

> Para elegir entre los 4 métodos de persistencia: ver la tabla comparativa en [[Clase 04 - Inyección de Dependencias y Storage]]

---

## Interno vs Externo

| | **Almacenamiento Interno** | **Almacenamiento Externo** |
|---|---|---|
| ¿Quién accede? | Solo tu app | Otras apps y el usuario |
| ¿Sobrevive a desinstalar? | ❌ Se elimina | ✅ Persiste |
| ¿Necesita permisos? | No | Sí, `WRITE_EXTERNAL_STORAGE` |
| Ideal para | Datos privados, caché, configuración | Imágenes, PDFs, descargas |

---

## Almacenamiento Interno

```java
File file = new File(getFilesDir(), "log.txt");
FileWriter fw = new FileWriter(file);
fw.write("error: timeout");
fw.close();
```

- `getFilesDir()` apunta a la carpeta privada de tu app
- No requiere ningún permiso en el [[AndroidManifest]]
- Se borra automáticamente si el usuario desinstala la app

---

## Almacenamiento Externo

```java
File dir = Environment.getExternalStoragePublicDir(
        Environment.DIRECTORY_DOWNLOADS);
File file = new File(dir, "doc.pdf");
```

- Requiere el permiso correspondiente en el Manifest
- El archivo queda visible para el usuario (ej: en la carpeta Descargas)
- Persiste aunque se desinstale la app

```xml
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
```

> ⚠️ Desde Android 10+ (API 29) el acceso a almacenamiento externo cambió mucho con **Scoped Storage** — para escribir fuera de la carpeta propia de la app, generalmente conviene usar `MediaStore` o el selector de archivos del sistema (`ACTION_CREATE_DOCUMENT`) en vez de rutas directas.

---

## Leer un archivo

```java
File file = new File(getFilesDir(), "log.txt");
StringBuilder contenido = new StringBuilder();

try (BufferedReader br = new BufferedReader(new FileReader(file))) {
    String linea;
    while ((linea = br.readLine()) != null) {
        contenido.append(linea).append("\n");
    }
}
```

---

## Directorios comunes de `Environment`

| Constante | Contenido |
|---|---|
| `DIRECTORY_DOWNLOADS` | Descargas |
| `DIRECTORY_PICTURES` | Imágenes |
| `DIRECTORY_DOCUMENTS` | Documentos |
| `DIRECTORY_MOVIES` | Videos |

---

## Errores típicos

| Síntoma | Causa |
|---|---|
| `FileNotFoundException` | La carpeta no existe — usar `dir.mkdirs()` antes de escribir |
| `SecurityException` | Falta el permiso en el Manifest, o no se pidió en runtime (Android 6+) |
| El archivo no aparece para el usuario | Se guardó en almacenamiento **interno** en vez de externo |
| Crash en Android 10+ al escribir fuera de la carpeta de la app | Scoped Storage — usar `MediaStore` |

---

## Relacionado

- [[Clase 04 - Inyección de Dependencias y Storage]]
- [[AndroidManifest]]
- [[DataStore]]
- [[Room]]
