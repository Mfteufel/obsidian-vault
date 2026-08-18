# 📡 Endpoints — Tabla Completa (40/40 ✅)

**URL base:** `http://localhost:8083/api`  
**Auth:** Bearer Token (obtenido del login)

---

## ✅ Resultado de pruebas

| Endpoint | Método | Status | Estado |
|---|---|---|---|
| `/api/personas/{id}` | GET | 200 | ✅ OK |
| `/api/personas` | POST | 201 | ✅ OK |
| `/api/personas/{id}` | PUT | 200 | ✅ OK |
| `/api/personas/{id}` | PATCH | 200 | ✅ OK |
| `/api/clientes/{id}` | GET | 200 | ✅ OK |
| `/api/clientes` | POST | 201 | ✅ OK |
| `/api/clientes/{id}/categoria` | PATCH | 200 | ✅ OK |
| `/api/clientes/{id}/admitido` | PATCH | 200 | ✅ OK |
| `/api/auth/login` | POST | 200/401 | ✅ OK |
| `/api/clientes/{id}/medios-pago` | POST | 201 | ✅ OK |
| `/api/subastas` | GET | 200 | ✅ OK |
| `/api/subastas?categoria&moneda` | GET | 200 | ✅ OK |
| `/api/subastas/{id}` | GET | 200 | ✅ OK |
| `/api/subastas/{id}/estado` | GET | 200 | ✅ OK |
| `/api/subastas/{id}/catalogos` | GET | 200 | ✅ OK |
| `/api/subastas` | POST | 201 | ✅ OK |
| `/api/subastas/{id}/estado` | PATCH | 200 | ✅ OK |
| `/api/subastadores/{id}/subastas` | GET | 200 | ✅ OK |
| `/api/productos/{id}` | GET | 200 | ✅ OK |
| `/api/productos` | POST | 201 | ✅ OK |
| `/api/productos/{id}/disponible` | PATCH | 200 | ✅ OK |
| `/api/productos/{id}/fotos` | POST | 201 | ✅ OK |
| `/api/pujos?item=` | GET | 200 | ✅ OK |
| `/api/pujos?asistente=` | GET | 200 | ✅ OK |
| `/api/pujos` | POST | 201 | ✅ OK |
| `/api/pujos/{itemId}/ganador` | GET | 404* | ✅ OK |
| `/api/asistentes/{id}` | GET | 200 | ✅ OK |
| `/api/asistentes/{id}/pujos` | GET | 200 | ✅ OK |
| `/api/seguros/{nroPoliza}` | GET | 200 | ✅ OK |
| `/api/seguros` | POST | 201 | ✅ OK |
| `/api/seguros/{nroPoliza}` | PUT | 200 | ✅ OK |
| `/api/registro-subasta` | POST | 201 | ✅ OK |
| `/api/registro-subasta/{id}` | GET | 200 | ✅ OK |
| `/api/registro-subasta/cliente/{id}` | GET | 200 | ✅ OK |
| `/api/registro-subasta/subasta/{id}` | GET | 200 | ✅ OK |
| `/api/registro-subasta/{id}/reembolso` | PATCH | 200 | ✅ OK |
| `/api/multas/{id}` | GET | 200 | ✅ OK |
| `/api/multas/{id}` | PATCH | 200 | ✅ OK |
| `/api/notificaciones/{id}` | GET | 200 | ✅ OK |
| `/api/catalogos/{id}/items` | GET | 200 | ✅ OK |

> *El 404 en `/api/pujos/{itemId}/ganador` es **comportamiento correcto**: devuelve 404 cuando ninguna puja tiene `ganador=si`, que es el estado real de la BD vacía.

---

## Detalle por sección

### Auth
```
POST /api/auth/login
Body: { "email": "nuevo@bidly.com", "password": "pass1234" }
Response 200: { "token": "uuid..." }
Response 401: credenciales incorrectas
```

### Personas y Clientes
```
GET    /api/personas/{id}
POST   /api/personas         Body: { documento, nombre, direccion, estado }
PUT    /api/personas/{id}
PATCH  /api/personas/{id}

GET    /api/clientes/{id}
POST   /api/clientes
PATCH  /api/clientes/{id}/categoria   Body: { "categoria": "oro" }
PATCH  /api/clientes/{id}/admitido    Body: { "admitido": "si" }
POST   /api/clientes/{id}/medios-pago Body: { tipo, numeroTarjeta, vencimiento... }
```

### Subastas
```
GET    /api/subastas                          → solo abiertas
GET    /api/subastas?categoria=comun&moneda=pesos
GET    /api/subastas/{id}
GET    /api/subastas/{id}/estado
GET    /api/subastas/{id}/catalogos
POST   /api/subastas                          Body: { fecha, hora, estado, subastador, ubicacion, categoria, moneda }
PATCH  /api/subastas/{id}/estado              Body: { "estado": "cerrada" }
GET    /api/subastadores/{id}/subastas
```

### Productos
```
GET    /api/productos/{id}
POST   /api/productos                         Body: { fecha, disponible, descripcionCompleta, revisor, duenio }
PATCH  /api/productos/{id}/disponible         → alterna si↔no
POST   /api/productos/{id}/fotos              Body: { fotos: ["url1", "url2"...] }
```

### Pujas
```
GET    /api/pujos?item={id}
GET    /api/pujos?asistente={id}
POST   /api/pujos                             Body: { asistente: {identificador}, item: {identificador}, importe }
GET    /api/pujos/{itemId}/ganador
GET    /api/asistentes/{id}
GET    /api/asistentes/{id}/pujos
```

### Seguros, Registro, Multas, Notificaciones
```
GET    /api/seguros/{nroPoliza}
POST   /api/seguros                           Body: { nroPoliza, compania, polizaCombinada, importe }
PUT    /api/seguros/{nroPoliza}

POST   /api/registro-subasta
GET    /api/registro-subasta/{id}
GET    /api/registro-subasta/cliente/{id}
GET    /api/registro-subasta/subasta/{id}
PATCH  /api/registro-subasta/{id}/reembolso   Body: { "reembolsada": "si" }

GET    /api/multas/{id}
PATCH  /api/multas/{id}                       Body: { "pagada": "si" }

GET    /api/notificaciones/{id}
GET    /api/catalogos/{id}/items
```
