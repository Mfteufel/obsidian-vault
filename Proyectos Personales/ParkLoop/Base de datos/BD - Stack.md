---
tags: [parkloop, base-de-datos, stack]
---

# BD - Stack

Volver a [[Resumen]]

## Resumen rápido

| Tecnología | Para qué |
|---|---|
| **PostgreSQL** | La base de datos (SQL relacional) |
| **Docker** | Correr PostgreSQL sin instalarlo en la máquina |
| **Prisma** | Conectar el backend con la base (ORM) |

---

## ¿Por qué SQL y no NoSQL?

- El modelo tiene **relaciones claras**: Usuario → Vehículo → Reserva → Sesión.
- Hay **integridad** que cuidar: no puede haber una reserva sin usuario.
- Necesitamos **transacciones** para no vender dos veces la misma plaza.
- Hay cálculos y reportes (cobros, banco de horas).

Una base NoSQL (MongoDB) complicaría todo esto.

---

## PostgreSQL

**Qué es:** base de datos relacional, gratis y open source.

**Por qué:**
- Muy buena con relaciones y transacciones.
- Soporta **JSON** (lo usamos en `snapshot_reglas` y `payload` de eventos).
- Marco ya la conoce.

---

## Docker

**Qué es:** corre programas en "contenedores" aislados.

**Por qué:** levantás PostgreSQL con un comando, igual en cualquier máquina.

```yaml
# docker-compose.yml
services:
  db:
    image: postgres:16
    environment:
      POSTGRES_USER: parkloop
      POSTGRES_PASSWORD: parkloop
      POSTGRES_DB: parkloop
    ports:
      - "5432:5432"
    volumes:
      - pgdata:/var/lib/postgresql/data
volumes:
  pgdata:
```

```bash
docker compose up -d
```

---

## Prisma

**Qué es:** un **ORM** (Object-Relational Mapper). **No es una app**: es una librería dentro del backend.

**Qué hace:** traduce entre código TypeScript y tablas SQL.

**Partes:**
- `schema.prisma` → acá se definen las tablas.
- `prisma migrate` → crea o cambia las tablas en la BD.
- `prisma generate` → genera el cliente con tipos.
- `seed.ts` → carga datos de prueba.

```prisma
model Vehiculo {
  id        String  @id @default(cuid())
  usuarioId String
  patente   String
  altoMm    Int
  largoMm   Int
  anchoMm   Int
  pesoKg    Int
  usuario   Usuario @relation(fields: [usuarioId], references: [id])
}
```

```typescript
const autos = await prisma.vehiculo.findMany({ where: { usuarioId } });
```

**Comandos:**
```bash
npm install prisma @prisma/client
npx prisma init
npx prisma migrate dev --name init
npx prisma db seed
npx prisma studio   # ver los datos en el navegador
```

---

## Datos de prueba (seed)

Según el prompt inicial:
- **3 sitios**: Palermo, Recoleta y Belgrano. **Recoleta completa**.
- Modelos de torre con medidas distintas.
- Los planes (tiers) de la banda Standard.
- Usuarios: walk-in, uno por plan, uno suspendido, uno con **40 min de saldo**.
- Vehículos **compatibles e incompatibles**.

Tablas en [[BD - Modelo de datos]].
