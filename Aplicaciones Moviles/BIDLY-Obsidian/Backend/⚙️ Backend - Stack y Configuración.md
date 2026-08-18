# ⚙️ Backend — Stack y Configuración

## Stack tecnológico

| Tecnología | Versión | Rol |
|---|---|---|
| Java | 21 | Lenguaje principal |
| Spring Boot | 3.5.x | Framework principal |
| Spring Data JPA | (incluido) | ORM — acceso a datos |
| Spring Security | (incluido) | Seguridad (deshabilitada en dev) |
| PostgreSQL | 16 | Base de datos |
| Lombok | (incluido) | Reduce código boilerplate |
| Maven | Wrapper incluido | Build y dependencias |

---

## Configuración de conexión

```properties
# application.properties
server.port=8083
spring.datasource.url=jdbc:postgresql://localhost:5432/bidly
spring.datasource.username=postgres
spring.datasource.password=bidly2026
```

**pgAdmin 4:** contraseña `admin123`

---

## Cómo levantar el backend

```bash
cd bidly-backend
./mvnw spring-boot:run
```

El servidor arranca en: `http://localhost:8083`

---

## Arquitectura

```
src/main/java/com/bidly/bidly_backend/
├── BidlyBackendApplication.java   ← Punto de entrada
├── config/
│   ├── SecurityConfig.java        ← Seguridad (todo permitido en dev)
│   └── CorsConfig.java            ← CORS habilitado para /api/**
├── model/                         ← Entidades JPA
├── repository/                    ← Interfaces Spring Data
└── controller/                    ← Controladores REST
```

**Flujo de una request:**
```
App → HTTP → Controller → Repository → PostgreSQL
```

---

## Credenciales de prueba

| Campo | Valor |
|---|---|
| Email | `nuevo@bidly.com` |
| Password | `pass1234` |
| Respuesta | 200 + token UUID |

> ⚠️ `juan.garcia@bidly.com` NO existe en la BD → devuelve 401 (correcto)

---

## Modelos (entidades JPA)

| Modelo | Tabla BD | Notas importantes |
|---|---|---|
| Persona | `personas` | Base de todos los usuarios |
| Cliente | `clientes` | FK a personas (mismo ID) |
| Subasta | `subastas` | `moneda`: pesos/dolares |
| Producto | `productos` | FK a duenios y seguros |
| Catalogo | `catalogos` | FK a subastas |
| ItemCatalogo | `itemscatalogo` | precioBase y comision |
| Asistente | `asistentes` | FK a clientes + subastas |
| Puja | `pujos` | `ganador`: si/no |
| RegistroDeSubasta | `registrodesubasta` | Campo `reembolsada` agregado |
| MedioPago | `mediosdepago` | `vencimiento` es String (MM/YYYY) |
| Multa | `multas` | `fechagenerada` es LocalDateTime |
| Seguro | `seguros` | `nroPoliza` como PK (String) |
| Notificacion | `notificaciones` | tipos: lider/perdiste/ganaste/multa |
| Foto | `fotos` | `foto` como byte[] |

---

## Reglas de negocio implementadas

### Pujas
- Primera puja: mínimo = `precioBase` del ítem
- Pujas siguientes: mínimo = `última puja + 1% del precioBase`
- Si no cumple → HTTP **422** con el mínimo aceptable

### Clientes
- No se crea cliente si no existe la persona (mismo ID)
- Categorías válidas: `comun`, `especial`, `plata`, `oro`, `platino`

### Subastas
- Sin filtros → solo devuelve subastas `abierta`
- Estado solo acepta: `abierta` / `cerrada`

### Auth
- Login por email + password (comparación directa, sin hash real)
- Devuelve token UUID por sesión
