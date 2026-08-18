

![[IDD I_Clase10_AFB.pdf]]


# Clase 10 — SQL Dinámico

**Materia:** Ingeniería de Datos  
**Institución:** UADE  
**Tema central:** SQL avanzado — cursores, transacciones, triggers, esquemas, sinónimos y SQL dinámico

---

## 🎯 Objetivo de la clase

Explorar herramientas avanzadas de SQL Server para:

- Control fila a fila con **cursores**
- Manejo de errores y atomicidad con **transacciones**
- Automatización de reglas con **triggers**
- Organización y permisos con **esquemas y sinónimos**
- Consultas generadas en tiempo de ejecución con **SQL dinámico**

---

## 📋 Repaso — Procedimientos y Funciones

### Procedimientos almacenados (repaso)

| # | Nombre | Qué hace |
|---|--------|----------|
| 1 | `sp_AnalizarPedidosCliente` | Cuenta pedidos de un cliente y emite mensaje |
| 2 | `sp_TotalPedido` | Calcula total de un pedido y avisa si supera $1000 |

### Funciones (repaso)

| # | Tipo | Nombre | Qué hace |
|---|------|--------|----------|
| 3 | Escalar | `fn_CategoriaProducto` | Clasifica producto por precio (Premium / Medio / Económico) |
| 4 | iTVF | `fn_PedidosPorAnio` | Devuelve pedidos de un año (inline, 1 sola SELECT) |
| 5 | mTVF | `fn_ClientesPromedio` | Devuelve clientes con promedio y categoría (multisentencia) |

---

## 📚 Temas nuevos de la clase

### 1. CTE — Common Table Expression
→ Ver [[CC_CTE]]

- Consulta temporal nombrada, existe **solo durante la siguiente sentencia**
- Sintaxis: `WITH nombre AS ( SELECT ... )`
- Uso frecuente: subconsultas legibles, top N por grupo

---

### 2. Cursores
→ Ver [[CC_Cursores]]

- Recorren resultados **fila por fila**
- Ciclo de vida: `DECLARE → OPEN → FETCH → WHILE → CLOSE → DEALLOCATE`
- `@@FETCH_STATUS = 0` → hay fila disponible
- Opciones clave: movimiento (`FORWARD_ONLY` / `SCROLL`), acceso (`STATIC` / `DYNAMIC`), concurrencia (`READ_ONLY` / `SCROLL_LOCKS`)

---

### 3. Transacciones
→ Ver [[CC_Transacciones]]

- Unidad atómica: **todo o nada** (ACID)
- Comandos: `BEGIN TRANSACTION`, `COMMIT`, `ROLLBACK`
- Usar con `TRY...CATCH`
- Tipos: **autocommit**, **implícita**, **explícita**
- Niveles de aislamiento: `READ UNCOMMITTED` → `SERIALIZABLE` → `SNAPSHOT`

---

### 4. Triggers
→ Ver [[CC_Triggers]]

- Se ejecutan **automáticamente** ante `INSERT`, `UPDATE` o `DELETE`
- Tipos: `AFTER` (post-evento) e `INSTEAD OF` (reemplaza evento)
- Tablas virtuales disponibles: `INSERTED` y `DELETED`

---

### 5. Esquemas
→ Ver [[CC_Esquemas]]

- Contenedores lógicos para agrupar objetos de la BD
- Beneficios: organización, seguridad, flexibilidad, despliegue controlado
- Sintaxis: `CREATE SCHEMA nombre` / `esquema.tabla`

---

### 6. Sinónimos
→ Ver [[CC_Sinonimos]]

- Alias para objetos (tablas, vistas, procedimientos, funciones)
- Permite cambiar la ubicación del objeto sin modificar las consultas

---

### 7. SQL Dinámico
→ Ver [[CC_SQL_Dinamico]]

- Construye consultas como texto en tiempo de ejecución
- Dos formas de ejecutar: `EXEC(@sql)` o `sp_executesql` (más seguro, evita inyección)

---

## 🔗 Relacionado

[[CC_CTE]] · [[CC_Cursores]] · [[CC_Transacciones]] · [[CC_Triggers]] · [[CC_Esquemas]] · [[CC_Sinonimos]] · [[CC_SQL_Dinamico]]

---

**Próxima clase (11):** Normalización y Modelo ER — Dependencias funcionales
