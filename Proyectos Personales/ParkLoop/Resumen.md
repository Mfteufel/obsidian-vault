---
tags: [parkloop, resumen]
estado: MVP en planificación
actualizado: 2026-11-06
---

# ParkLoop — Resumen

> [!info] Para Claude Code
> Esta es la **nota central** del proyecto. Empezá acá.
> Cada sección linkea a una nota con el detalle.
> La especificación completa está en `ParkLoop_Documentacion_Tecnica.md` (se referencia con IDs: `UC-xx`, `HU-xx`, `RN-xx`).

## Qué es

- **Red de torres de estacionamiento rotativas** (carrusel vertical) en CABA.
- El conductor **reserva antes de salir** y tiene la plaza **garantizada**.
- Paga **solo el tiempo usado**, en bloques de **15 minutos**.
- Sin playero, sin ticket de papel.

## Cómo funciona la torre

- Un carrusel vertical de **12 a 16 plazas (bays)**, configuración **SUV**.
- El auto se deja en una **plataforma a nivel del piso**. La torre lo sube.
- **Un solo motor** mueve todo. Una sola maniobra a la vez.
- Una maniobra puede tardar hasta **175 segundos**.

## Los 3 diferenciales (no se pueden recortar)

1. **Reserva garantizada** → nunca se vende dos veces la misma plaza.
2. **Cobro cada 15 minutos** → 65 min se cobran como 75, no como 120.
3. **Banco de horas mensual** → el suscriptor tiene un saldo de horas para usar cuando quiera.

Detalle en [[Reglas de negocio/RN - Índice|Reglas de negocio]].

## Stack decidido

| Capa | Tecnología | Nota |
|---|---|---|
| App | React Native + Expo + TypeScript | [[Front - Stack]] |
| Backend | Node.js + TypeScript + Express | [[Back - Stack]] |
| Base de datos | PostgreSQL en Docker | [[BD - Stack]] |
| Conexión a BD | Prisma (ORM) | [[BD - Stack]] |

**Un solo lenguaje de punta a punta: TypeScript.**

## Mapa de notas

### 📱 Front
- [[Front - Stack]] — qué tecnologías y por qué
- [[Front - Pantallas]] — onboarding + 5 tabs
- [[Front - Escaneo DNI y licencia]] — leer el código PDF417 con la cámara
- [[Front - Reservas y mapa]] — mapa interactivo y selectores de hora

### ⚙️ Back
- [[Back - Stack]] — Node.js, Express, TypeScript
- [[Back - Arquitectura]] — monorepo, Núcleo vs Controlador de Sitio, mocks
- [[Back - Endpoints]] — la API que consume la app
- [[Back - Reintentos e idempotencia]] — cómo evitar cobros y reservas duplicadas

### 🗄️ Base de datos
- [[BD - Stack]] — PostgreSQL, Docker, Prisma
- [[BD - Modelo de datos]] — tablas y relaciones
- [[BD - Privacidad]] — qué datos son sensibles y qué no guardar

### 📏 Reglas de negocio
- [[RN - Índice]] — lista de todas las reglas
- [[RN - Tarifación 15 minutos]]
- [[RN - Banco de horas y planes]]
- [[RN - Reservas, no-show y cancelación]]
- [[RN - Compatibilidad de vehículo]]
- [[RN - Fallas de torre]]

### 💰 Finanzas
- [[Finanzas - Resumen]] — análisis económico: inversión, escenarios, planes e inversores

### 🤖 Otros
- [[Promt]] — prompt inicial para Claude Code

## Estado actual

- ✅ Documentación técnica completa.
- ✅ Prototipo HTML/JS navegable (5 tabs, onboarding, panel demo).
- ✅ Stack decidido.
- ⏳ Arrancar el MVP en código.
- ⏳ Análisis económico en curso (ver [[Finanzas - Resumen]]).

## Fuera de alcance por ahora

- Pagos reales con Mercado Pago.
- Consola de operador (`UC-12` a `UC-17`).
- Diseño final de las tabs **Mi auto** y **Banco**.
- Sección de seguros.
- Infraestructura en la nube.

## Datos que todavía NO están cerrados

- **Fabricante de la torre**: Dayang, JinGuan o AGUSTIN (cotizando).
- **Lote piloto**: 4 candidatos en CABA.
- **Protocolo con la torre (PLC)**: depende del fabricante.

> [!warning] Consecuencia
> El software **no puede depender** de un fabricante ni de un lote.
> Todo lo que cambia entre torres vive en la base de datos, no en el código.
