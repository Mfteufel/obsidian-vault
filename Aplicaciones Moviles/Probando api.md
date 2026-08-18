### Cambios realizados

**Modelos arreglados:**

- `MedioPago` — tabla `mediospago` → `mediosdepago`, campo `vencimiento` `LocalDate` → `String`
- `Cliente` — campo `numeroPais` `String` → `Integer`
- `RegistroDeSubasta` — agregado campo `reembolsada`
- `Multa` — `fechagenerada` `LocalDate` → `LocalDateTime`

**Modelos nuevos:** `Seguro`, `Notificacion`, `Foto`

**Config nueva:** `CorsConfig`

**Repositories nuevos/actualizados:** `SeguroRepository`, `NotificacionRepository`, `FotoRepository`, `PujaRepository` (+asistente query), `RegistroDeSubastaRepository` (+subasta query)

**Controllers nuevos:** `AsistenteController`, `SeguroController`, `NotificacionController`, `CatalogoController`

**Endpoints agregados a controllers existentes:** PUT/PATCH en Personas, POST/PATCH-estado en Subastas, PATCH-disponible/POST-fotos en Productos, GET con query params en Pujos, POST/GET/PATCH en Registro, PATCH en Multas

---

| Endpoint                             | Método | Status  | Estado |
| ------------------------------------ | ------ | ------- | ------ |
| /api/personas/{id}                   | GET    | 200     | OK     |
| /api/personas                        | POST   | 201     | OK     |
| /api/personas/{id}                   | PUT    | 200     | OK     |
| /api/personas/{id}                   | PATCH  | 200     | OK     |
| /api/clientes/{id}                   | GET    | 200     | OK     |
| /api/clientes                        | POST   | 201     | OK     |
| /api/clientes/{id}/categoria         | PATCH  | 200     | OK     |
| /api/clientes/{id}/admitido          | PATCH  | 200     | OK     |
| /api/auth/login                      | POST   | 200/401 | OK     |
| /api/clientes/{id}/medios-pago       | POST   | 201     | OK     |
| /api/subastas                        | GET    | 200     | OK     |
| /api/subastas?categoria&moneda       | GET    | 200     | OK     |
| /api/subastas/{id}                   | GET    | 200     | OK     |
| /api/subastas/{id}/estado            | GET    | 200     | OK     |
| /api/subastas/{id}/catalogos         | GET    | 200     | OK     |
| /api/subastas                        | POST   | 201     | OK     |
| /api/subastas/{id}/estado            | PATCH  | 200     | OK     |
| /api/subastadores/{id}/subastas      | GET    | 200     | OK     |
| /api/productos/{id}                  | GET    | 200     | OK     |
| /api/productos                       | POST   | 201     | OK     |
| /api/productos/{id}/disponible       | PATCH  | 200     | OK     |
| /api/productos/{id}/fotos            | POST   | 201     | OK     |
| /api/pujos?item=                     | GET    | 200     | OK     |
| /api/pujos?asistente=                | GET    | 200     | OK     |
| /api/pujos                           | POST   | 201     | OK     |
| /api/pujos/{itemId}/ganador          | GET    | 404*    | OK     |
| /api/asistentes/{id}                 | GET    | 200     | OK     |
| /api/asistentes/{id}/pujos           | GET    | 200     | OK     |
| /api/seguros/{nroPoliza}             | GET    | 200     | OK     |
| /api/seguros                         | POST   | 201     | OK     |
| /api/seguros/{nroPoliza}             | PUT    | 200     | OK     |
| /api/registro-subasta                | POST   | 201     | OK     |
| /api/registro-subasta/{id}           | GET    | 200     | OK     |
| /api/registro-subasta/cliente/{id}   | GET    | 200     | OK     |
| /api/registro-subasta/subasta/{id}   | GET    | 200     | OK     |
| /api/registro-subasta/{id}/reembolso | PATCH  | 200     | OK     |
| /api/multas/{id}                     | GET    | 200     | OK     |
| /api/multas/{id}                     | PATCH  | 200     | OK     |
| /api/notificaciones/{id}             | GET    | 200     | OK     |
| /api/catalogos/{id}/items            | GET    | 200     | OK     |

**40/40 OK** — _El 404 en `ganador` es comportamiento correcto: devuelve 404 cuando ninguna puja tiene `ganador=si`, que es el estado real de los datos._

> **Nota sobre auth:** El test del enunciado usa `juan.garcia@bidly.com` que no existe en la BD, devuelve 401 correctamente. El endpoint funciona bien: usando `nuevo@bidly.com` / `pass1234` (cliente creado durante las pruebas) devuelve 200 con token.









