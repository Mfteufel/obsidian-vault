---
tags: [repaso, tpo, ronda, plan-de-estudio, oral, desarrollo-aplicaciones-1]
aliases: [Plan v2, Plan de estudio nuevo]
---

# 02 — Plan de estudio v2

> Reemplaza el orden de [[01 - Plan de estudio hasta el oral]] (esa nota queda como estaba).
> Volver al [[00 - Índice Repaso 5 y 7]]

**Idea:** primero la base (clases 2 a 6), después los puntos 5 y 7.
**Oral:** martes **1/12**.
**Sábados 21 y 28:** colchón por si te atrasás.

## Reglas

- **Máximo 2 bloques por día** (25–30 min cada uno).
- **Pausa de 5 min** entre bloques.
- **Cada día arranca** contando en voz alta lo de ayer, **sin mirar**.
- **Cada día termina** tildando las casillas.
- Si un día se complica, pasá lo que falta al siguiente. No lo dobles.

## Vista general

| Fecha | Día | Tema |
|---|---|---|
| 13/11 | Vie | Clase 2: estructura, arquitectura, componentes, ciclo de vida |
| 16/11 | Lun | Clase 2 (eventos, ListView, Intents) + Clase 3 |
| 17/11 | Mar | Clase 4 (DI, Hilt, DataStore) + Clase 5 (JWT) |
| 18/11 | Mié | Clase 6 (ActivityResultLauncher, biometría) + repaso 2 a 6 |
| 19/11 | Jue | Punto 5: recorrido, ViewModel, LiveData |
| 20/11 | Vie | Punto 5: Room, hilos, fotos y permisos |
| 23/11 | Lun | Punto 5: Retrofit, Hilt y Mis publicaciones |
| 24/11 | Mar | Punto 5: defensa y repaso completo |
| 25/11 | Mié | Punto 7: recorrido, respuestas y vencimiento |
| 26/11 | Jue | Punto 7: capa de datos y pantallas |
| 27/11 | Vie | Punto 7: backend, defensa y repaso completo |
| 30/11 | Lun | **Simulacro general** |
| 01/12 | Mar | **ORAL** |

---

## Fase 1 — La base (clases 2 a 6)

### Vie 13/11 — Clase 2, parte 1
- [ ] Leer [[Clase 02 - Android Primeros Pasos]], secciones 1 a 5.
- [ ] Repasar [[Actividad]], [[Arquitectura de Android]], [[AndroidManifest]].
- [ ] Explicar: los **4 componentes** y el **ciclo de vida** de una Activity.

### Lun 16/11 — Clase 2 parte 2 + Clase 3
- [ ] Clase 02, secciones 6 a 8: eventos de UI, ListView, Intents.
- [ ] Repasar [[ListView y Adapters]] e [[Intents]].
- [ ] Leer [[Clase 03 - Fragments y Navigation Component]].
- [ ] Repasar [[Fragment]] y [[Navigation Component]].
- [ ] Explicar: Activity vs Fragment, y para qué sirve un Intent.

### Mar 17/11 — Clase 4 + Clase 5
- [ ] Leer [[Clase 04 - Inyección de Dependencias y Storage]].
- [ ] Repasar [[Inyección de Dependencias (DI)]], [[Hilt]], [[DataStore]], [[Retrofit - Consumo de API REST]].
- [ ] Leer [[Clase 05 - JWT y LocalStorage]] y [[JWT]].
- [ ] Explicar: qué problema resuelve Hilt y cómo viaja el token.

### Mié 18/11 — Clase 6 + repaso
- [ ] Leer [[Clase 06 - Biometría y Seguridad en el Dispositivo]].
- [ ] Repasar [[ActivityResultLauncher]] y [[Autenticación Biométrica]].
- [ ] Contar de memoria las clases 2 a 6 y releer lo que falle.
- [ ] Explicar: las 3 piezas de ActivityResultLauncher.

---

## Fase 2 — Punto 5

### Jue 19/11 — Recorrido, ViewModel, LiveData
- [ ] Secciones 1 y 2 del [[Repaso Punto 5 - Publicar Artículo]].
- [ ] Dibujar los 5 pasos de memoria.
- [ ] Flujo A a C. Abrir `PublicarPasoFragment` y `PublicarArticuloViewModel`.
- [ ] Explicar: cómo se comparte el estado entre los 5 pasos.

### Vie 20/11 — Room, hilos, fotos y permisos
- [ ] Room: Entity, DAO, Converters. Executor + Handler. `BorradorRepositoryLocal`.
- [ ] `PublicarFotosFragment`, permisos, Uri y ContentResolver (flujo D).
- [ ] Explicar: por qué una sola fila y para qué `takePersistableUriPermission`.

### Lun 23/11 — Retrofit, Hilt y Mis publicaciones
- [ ] Retrofit multipart, Hilt y `@EntryPoint` (flujo E).
- [ ] `MisPublicacionesFragment`, `MiPublicacionAdapter`, flujo F.
- [ ] Explicar: por qué el ViewModel usa `@EntryPoint`.

### Mar 24/11 — Defensa y repaso completo
- [ ] Preguntas de defensa (sección 5) en voz alta.
- [ ] Cosas raras (sección 6).
- [ ] Contar el Punto 5 de memoria y recorrer los 6 archivos clave.

---

## Fase 3 — Punto 7

### Mié 25/11 — Recorrido, respuestas y vencimiento
- [ ] Secciones 1 y 2 del [[Repaso Punto 7 - Ofertas y Negociación]] + `docs/ofertas-api.md`.
- [ ] Memorizar: **4 estados, 2 turnos, 6 h**.
- [ ] Flujo A a F. `OfertaNegociacion.meTocaResponder`.
- [ ] Explicar: quién puede aceptar, rechazar o contraofertar, y cómo vence sin cron.

### Jue 26/11 — Capa de datos y pantallas
- [ ] `OfertasRepository`, `OfertasApi`, `OfertasRepositoryRemoto`, `RepositoryModule`.
- [ ] `MisOfertasFragment`, `generacionConsulta`, `TabLayout`, `DetalleOfertaBottomSheet`.
- [ ] Explicar: cómo se mantienen actualizadas y por qué la dirección solo se ve si está aceptada.

### Vie 27/11 — Backend, defensa y repaso completo
- [ ] `backend/routers/ofertas.py` y `database.py`.
- [ ] Preguntas de defensa y cosas raras.
- [ ] Contar el Punto 7 de memoria y recorrer los 6 archivos clave.

---

## Lun 30/11 — Simulacro general
- [ ] 10 preguntas al azar de ambos puntos, 1 min cada una, en voz alta.
- [ ] Releer lo dudoso.
- [ ] Dejar todo listo: proyecto abierto, `main` actualizada, emulador funcionando.
- [ ] No estudiar nada nuevo a la noche.

## Mar 1/12 — Oral
- [ ] 15 min a la mañana: sección 1 y flujo de cada punto + las 5 cosas raras del índice.
- [ ] Repo abierto y emulador listo.

---

## Seguimiento

| Día | ¿Hecho? | Qué me costó |
|---|---|---|
| Vie 13/11 | ☐ | |
| Lun 16/11 | ☐ | |
| Mar 17/11 | ☐ | |
| Mié 18/11 | ☐ | |
| Jue 19/11 | ☐ | |
| Vie 20/11 | ☐ | |
| Lun 23/11 | ☐ | |
| Mar 24/11 | ☐ | |
| Mié 25/11 | ☐ | |
| Jue 26/11 | ☐ | |
| Vie 27/11 | ☐ | |
