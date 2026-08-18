Te hago el balance final del día:

---

### 🔴 Pendientes importantes

- **R5 — Medio de pago verificado**: sin medio verificado el usuario solo ve, no puja. No está implementado.
- **Notificaciones**: nunca llegan. La consigna las pide (perdés liderazgo, ganás, se vende lo tuyo).
- **Barra de búsqueda**: no anda.
- **Mis Productos**: botones no hacen nada.

---

### 🟡 Pendientes menores

- **2 NOT NULL** que mencionó Walter (verificar contra el SQL del profe).
- **Admin Dashboard** con stats en `—`.

---

### ✅ Lo que cerramos hoy

- HTTPS en BASE_URL
- Medios de pago funcionando
- Moneda en subasta funcionando
- Mis Subastas muestra subastas
- R4 control de categoría (front + back)
- R1 puja mínima fix
- R2 puja máxima
- R3 excepción oro/platino
- Bloqueo pujar propia subasta
- Railway conectado a rama Marco

---

### ⚠️ Antes de entregar mañana

- Merge de rama `Marco` a `master`
- Confirmar que Railway tomó todo
  
  
  
  
  ---
### ✅ Resuelto hoy

**Infraestructura**

- Diagnosticamos que Railway estaba caído para deploys
- Configuramos todo el entorno local (backend + DB + front)
- Creamos usuario de prueba en DB local (`juan.garcia`, `ana.lopez`, `pedro.ramirez`)
- Conectamos Railway a la rama `Marco` al final del día

**Bugs de front**

- `http://` → `https://` en `BASE_URL` (rompía todos los POST)
- Tipo `credito` → `tarjeta` en medios de pago (violaba constraint de la DB)
- Confirmamos que moneda en crear subasta ya funcionaba

**Reglas de negocio (las que prueba el profe con números)**

- **R1** — Fix del fallback de puja mínima (ya no usa `minIncremento=1` arbitrario)
- **R2** — Techo de puja: última + 20% del precio base (no existía)
- **R3** — Excepción oro/platino: sin límites de monto (no existía)
- **R4** — Control de categoría: un común no puede entrar a platino (front + back 403)
- Bloqueo de pujar tu propia subasta (front + back 403)


--- 

Buscador de subastas

Que funcionen las notificaciones 

Eliminar producto