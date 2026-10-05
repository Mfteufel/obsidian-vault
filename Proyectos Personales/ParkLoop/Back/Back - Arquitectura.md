---
tags: [parkloop, back, arquitectura]
---

# Back - Arquitectura

Volver a [[Resumen]] · Stack en [[Back - Stack]]

## Estructura del repo (monorepo)

```
parkloop/
├── apps/
│   ├── mobile/        ← React Native + Expo
│   └── api/           ← Node.js + Express
├── packages/
│   └── shared/        ← tipos TypeScript compartidos
├── docs/              ← documentación del proyecto
├── docker-compose.yml ← PostgreSQL
└── CLAUDE.md          ← contexto para Claude Code
```

**Monorepo** = varios proyectos en un solo repositorio. Se maneja con **npm workspaces** (o pnpm).

---

## Los dos mundos del sistema

El doc técnico separa el sistema en dos partes **incompatibles**:

| | Núcleo de Negocio | Controlador de Sitio |
|---|---|---|
| Hace | Usuarios, reservas, cobros | Maneja la torre física |
| Tiempo de respuesta | Milisegundos | Segundos a minutos |
| Peor falla | Pantalla vacía | Máquina de toneladas con un auto |
| Fuente de verdad de | Usuarios, reservas, dinero | Qué auto está en qué bay |

**Regla:** nunca mezclarlos. Una maniobra de 175 s **no puede bloquear** un request HTTP.

**Comunicación:** asíncrona, con **comandos** y **eventos**.
- Comando: `SolicitarDeposito`, `SolicitarRetiro`.
- Evento: `ManiobraCompletada`, `ManiobraFallida`.

> [!note] MVP
> En el MVP ambos pueden correr en el mismo proceso, pero **separados en módulos**. El mecanismo del bus de eventos está abierto (supuesto S-11).

---

## Interfaz de torre + adaptadores

El fabricante no está elegido. Por eso:

```
Cola de maniobras
      ↓
Interfaz de Torre  (contrato único)
   ├── Adaptador Simulador   ← el que usamos en el MVP
   ├── Adaptador Dayang      ← futuro (Modbus TCP)
   ├── Adaptador JinGuan     ← futuro
   └── Adaptador AGUSTIN     ← futuro
```

**Operaciones del contrato:** `conectar()`, `leerEstado()`, `solicitarDeposito(bay)`, `solicitarRetiro(bay)`, `homing()`, `paradaEmergencia()`, `abortarManiobra()`.

**Cambiar de fabricante = escribir un adaptador nuevo.** Nada más.

---

## Mocks del MVP

| Sistema real | Mock |
|---|---|
| PLC de la torre | **Simulador** con tiempos y fallas configurables |
| Lector de patentes (LPR) | Devuelve la patente que le pidas |
| Mercado Pago | Aprueba, rechaza o demora a pedido |
| Facturación (ARCA) | Comprobantes ficticios |
| Notificaciones | Escribe en consola |

> [!important] El simulador no es un mock simple
> Tiene que simular **fallas**: motor caído, pérdida de conexión, persona en el recinto, maniobras que se cuelgan. Un simulador que siempre responde bien no sirve.

---

## Reglas de arquitectura (no negociables)

1. **Multi-sitio y multi-torre** desde el día uno.
2. **Nada hardcodeado**: medidas, tiempos, tarifas y planes son **datos** en la BD.
3. **El PLC decide.** El software solicita. Nunca fuerza una maniobra de seguridad.
4. **Plata en centavos** (enteros).
5. **Idempotencia** en todo lo que crea o cobra. Ver [[Back - Reintentos e idempotencia]].
6. Las reglas de negocio viven en un **módulo de dominio** separado, testeable solo. Ver [[RN - Índice]].
