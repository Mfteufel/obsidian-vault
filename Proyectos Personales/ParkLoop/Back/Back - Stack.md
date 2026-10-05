---
tags: [parkloop, back, stack]
---

# Back - Stack

Volver a [[Resumen]]

## Resumen rápido

| Tecnología | Para qué |
|---|---|
| **Node.js** | Ejecutar JavaScript/TypeScript en el servidor |
| **TypeScript** | Tipos compartidos con la app |
| **Express** | Crear la API (rutas HTTP) |
| **Prisma** | Hablar con PostgreSQL. Ver [[BD - Stack]] |
| **Docker Compose** | Levantar la base de datos con un comando |

---

## Node.js

**Qué es:** un entorno para correr JavaScript fuera del navegador.

**Por qué:** mismo lenguaje que la app. Menos cambio de contexto.

**Alternativa descartada:** Spring Boot (Java). Marco lo conoce, pero mezcla dos lenguajes.

---

## Express

**Qué es:** el framework web más simple y usado de Node.js.

**Qué hace:** define rutas y qué pasa cuando llega un request.

```typescript
app.get('/sitios', async (req, res) => {
  const sitios = await prisma.sitio.findMany();
  res.json({ sitios });
});
```

Endpoints en [[Back - Endpoints]].

---

## TypeScript en el back

- Los tipos del dominio viven en `packages/shared`.
- La app y la API importan **los mismos tipos**.
- Prisma genera tipos automáticamente desde el schema.

---

## Herramientas complementarias sugeridas

| Necesidad | Herramienta |
|---|---|
| Validar datos de entrada | **Zod** |
| Tests | **Vitest** o Jest |
| Correr TypeScript en desarrollo | **tsx** |
| Variables de entorno | `.env` + `dotenv` |

---

## Entorno local (MVP)

**Todo corre en la máquina del desarrollador.** Sin nube.

Objetivo del doc técnico: **un solo comando** levanta todo con datos de prueba.

```bash
docker compose up -d      # PostgreSQL
npm run db:migrate        # crea las tablas
npm run db:seed           # datos de prueba
npm run dev               # API + app
```

Los sistemas externos (torre, lector de patentes, pagos) son **mocks**. Ver [[Back - Arquitectura]].
