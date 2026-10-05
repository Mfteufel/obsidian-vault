---
tags: [android, room, sqlite, base-de-datos, jetpack, desarrollo-aplicaciones-1]
aliases: [Room, Room Database, Entity, DAO, SQLite Android]
---

# CC_Room

> Capa de abstracción sobre **SQLite**. Usa anotaciones Java para definir la base de datos, sus entidades (tablas) y las consultas — sin escribir SQL a mano en la mayoría de los casos.

## Cuándo usarlo

- Grandes volúmenes de datos relacionados (notas, tareas, productos)
- Necesitás **control total** sobre las consultas SQL
- La app tiene que **funcionar sin conexión** (offline-first)

> Para elegir entre los 4 métodos de persistencia: ver la tabla comparativa en [[Clase 04 - Inyección de Dependencias y Storage]]

---

## Los 3 componentes clave

| Componente | Qué es |
|---|---|
| **Entity** | Una clase Java = una tabla de la base de datos |
| **DAO** (Data Access Object) | Interfaz con los métodos para leer/escribir esa tabla |
| **Database** | Clase que une todo y expone los DAOs |

---

## 1. Entity (tabla)

```java
@Entity(tableName = "usuarios")
public class Usuario {
    @PrimaryKey(autoGenerate = true)
    public int id;

    public String nombre;
    public String email;
}
```

| Anotación | Qué hace |
|---|---|
| `@Entity(tableName = "x")` | Marca la clase como tabla |
| `@PrimaryKey(autoGenerate = true)` | Clave primaria auto-incremental |
| `@ColumnInfo(name = "x")` | *(opcional)* renombra la columna si es distinto al nombre del campo |

---

## 2. DAO (acceso a datos)

```java
@Dao
public interface UsuarioDao {

    @Insert
    void insertar(Usuario u);

    @Query("SELECT * FROM usuarios")
    List<Usuario> obtenerTodos();

    @Query("SELECT * FROM usuarios WHERE id = :id")
    Usuario obtenerPorId(int id);

    @Update
    void actualizar(Usuario u);

    @Delete
    void eliminar(Usuario u);
}
```

| Anotación | SQL equivalente |
|---|---|
| `@Insert` | `INSERT` |
| `@Query("...")` | Cualquier `SELECT` personalizado |
| `@Update` | `UPDATE` |
| `@Delete` | `DELETE` |

> El `:id` en el `@Query` se reemplaza automáticamente por el parámetro del método.

---

## 3. Database

```java
@Database(entities = {Usuario.class}, version = 1)
public abstract class AppDatabase extends RoomDatabase {
    public abstract UsuarioDao usuarioDao();
}
```

### Crear la instancia (singleton, o mejor: vía [[Hilt]])

```java
AppDatabase db = Room.databaseBuilder(context, AppDatabase.class, "app-db")
        .build();

UsuarioDao dao = db.usuarioDao();
```

> Al igual que con `Retrofit`, conviene proveer la instancia de `AppDatabase` con un `@Module` de Hilt en vez de un singleton manual.

---

## ⚠️ Room y el hilo principal

Room, por defecto, **no permite** ejecutar consultas en el hilo principal — tira `IllegalStateException`. Las operaciones deben correr en un hilo aparte, igual que Retrofit con [[Retrofit - Consumo de API REST|enqueue()]].

```java
// Con un executor
databaseExecutor.execute(() -> {
    List<Usuario> usuarios = dao.obtenerTodos();
    // volver al hilo principal para actualizar la UI si hace falta
});
```

---

## Room vs SQLite puro

| | SQLite puro | Room |
|---|---|---|
| Verificación de queries | En runtime (recién al ejecutar falla) | **En tiempo de compilación** |
| Boilerplate | Mucho (Cursor, ContentValues) | Mínimo, con anotaciones |
| Mapeo objeto ↔ fila | Manual | Automático |

---

## Relacionado

- [[Clase 04 - Inyección de Dependencias y Storage]]
- [[Hilt]]
- [[DataStore]]
- [[Retrofit - Consumo de API REST]]
