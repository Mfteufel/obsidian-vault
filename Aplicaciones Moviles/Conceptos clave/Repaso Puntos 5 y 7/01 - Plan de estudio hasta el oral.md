---
tags: [repaso, tpo, ronda, plan-de-estudio, oral, desarrollo-aplicaciones-1]
aliases: [Plan de estudio, Cronograma oral]
---

# 01 — Plan de estudio hasta el oral

> Volver al [[00 - Índice Repaso 5 y 7]]

**Oral:** martes **1/12**.
**Hoy:** viernes 13/11.
**Días de estudio:** 12. Sábados y domingos son **libres**.

---

## Reglas del plan

- **Máximo 2 bloques por día.** Cada bloque dura 25–30 min.
- **Pausa de 5 min** entre bloques.
- **Cada día arranca** con 5 min: contate en voz alta lo de ayer, **sin mirar**.
- **Cada día termina** tildando las casillas.
- **Si un día se te complica:** pasá lo que falta al día siguiente. No lo dobles.
- **Hablá en voz alta.** El oral es hablado.

---

## Vista general

| Fecha | Día | Tema |
|---|---|---|
| 13/11 | Vie | Punto 5: entender el recorrido |
| 16/11 | Lun | Punto 5: Fragments, Navigation, ViewModel, LiveData |
| 17/11 | Mar | Punto 5: Room e hilos |
| 18/11 | Mié | Punto 5: fotos, permisos, Retrofit multipart, Hilt |
| 19/11 | Jue | Punto 5: Mis publicaciones + preguntas de defensa |
| 20/11 | Vie | **Repaso completo Punto 5** |
| 23/11 | Lun | Punto 7: entender el recorrido |
| 24/11 | Mar | Punto 7: aceptar, contraofertar, vencimiento, capa de datos |
| 25/11 | Mié | Punto 7: pantallas y componentes de UI |
| 26/11 | Jue | Punto 7: backend + preguntas de defensa |
| 27/11 | Vie | **Repaso completo Punto 7** |
| 30/11 | Lun | **Simulacro general** |
| 01/12 | Mar | **ORAL** (repaso liviano a la mañana) |

---

## Vie 13/11 — Punto 5: entender el recorrido

**Bloque 1 (30 min): la foto general**
- [ ] Leer [[Repaso Punto 5 - Publicar Artículo#1. Qué hace la funcionalidad]]
- [ ] Leer [[Repaso Punto 5 - Publicar Artículo#2. Mapa del código]]
- [ ] Dibujar en papel los 5 pasos, en orden, de memoria.

**Bloque 2 (30 min): el flujo**
- [ ] Leer [[Repaso Punto 5 - Publicar Artículo#3. Flujo paso a paso]], partes **A, B y C**.
- [ ] Abrir en Android Studio `PublicarPasoFragment` y `PublicarArticuloViewModel`.
- [ ] Decir en voz alta: *"¿cómo se comparte el estado entre los 5 pasos?"*

**Para cerrar el día, tenés que poder decir:**
- Los 5 pasos y qué se carga en cada uno.
- Qué es un borrador y dónde se guarda.

---

## Lun 16/11 — Punto 5: Fragments, Navigation, ViewModel, LiveData

**Bloque 1 (30 min): navegación y ViewModel**
- [ ] Repasar tus notas: [[Fragment]] y [[Navigation Component]].
- [ ] Leer en el Punto 5 (sección 4): *Fragment y ciclo de vida*, *Navigation Component y sub-grafo*, *ViewModel compartido*.
- [ ] Abrir `nav_graph_publicar.xml` y leer el comentario del principio.
- [ ] Explicar: *"¿por qué el scope del ViewModel es el grafo y no el Fragment?"*

**Bloque 2 (30 min): LiveData y partes C–D del flujo**
- [ ] Leer en el Punto 5 (sección 4): *LiveData / MutableLiveData / observe*.
- [ ] Releer flujo partes **B y C** del Punto 5.
- [ ] Abrir `PublicarFotosFragment.saltarAlPasoGuardadoSiCorresponde()`.
- [ ] Explicar: *"¿cómo salta directo al paso guardado?"*

> ⚠️ **Hoy no tenés nota propia de ViewModel ni LiveData.** Si querés, creá una nota breve con tus palabras.

---

## Mar 17/11 — Punto 5: Room e hilos

**Bloque 1 (30 min): Room**
- [ ] Repasar tu nota [[Room]].
- [ ] Leer en el Punto 5 (sección 4): *Room: Entity, DAO, TypeConverter*.
- [ ] Abrir `BorradorPublicacionEntity`, `BorradorPublicacionDao` y `Converters`.
- [ ] Explicar: *"¿por qué una sola fila?"* y *"¿por qué enums como String?"*

**Bloque 2 (30 min): hilos y repositorio**
- [ ] Leer en el Punto 5 (sección 4): *Hilos: ExecutorService + Handler* y *Patrón Repository + Callback*.
- [ ] Abrir `BorradorRepositoryLocal`.
- [ ] Explicar: *"¿por qué Executor y Handler?"*

---

## Mié 18/11 — Punto 5: fotos, permisos, Retrofit multipart, Hilt

**Bloque 1 (30 min): galería y permisos**
- [ ] Repasar tu nota [[ActivityResultLauncher]].
- [ ] Leer en el Punto 5 (sección 4): *ActivityResultLauncher*, *Permisos en runtime*, *Uri y ContentResolver*.
- [ ] Abrir `PublicarFotosFragment` (los dos launchers y `agregarFoto`).
- [ ] Explicar: *"¿para qué `takePersistableUriPermission`?"*

**Bloque 2 (30 min): publicar**
- [ ] Repasar tu nota [[Retrofit - Consumo de API REST]] y [[Hilt]].
- [ ] Leer en el Punto 5 (sección 4): *Retrofit y multipart* y *Hilt: @Inject, @AndroidEntryPoint y @EntryPoint*.
- [ ] Abrir `MisPublicacionesRepositoryApi.publicar` y `PublicarEntryPoint`.
- [ ] Releer flujo partes **D y E** del Punto 5.
- [ ] Explicar: *"¿por qué el ViewModel usa @EntryPoint?"*

---

## Jue 19/11 — Punto 5: Mis publicaciones + defensa

**Bloque 1 (30 min): Mis publicaciones**
- [ ] Repasar tu nota [[RecyclerView]].
- [ ] Releer flujo parte **F** del Punto 5.
- [ ] Abrir `MisPublicacionesFragment` y `MiPublicacionAdapter`.
- [ ] Explicar: *"¿cómo se pausa y reactiva una publicación?"*

**Bloque 2 (30 min): defensa y cosas raras**
- [ ] Leer [[Repaso Punto 5 - Publicar Artículo#5. Preguntas probables de defensa]] **en voz alta**, tapando la respuesta.
- [ ] Leer [[Repaso Punto 5 - Publicar Artículo#6. Dudas o cosas raras del código]].
- [ ] Anotar las 3 cosas raras que más te preocupan.

**Pendiente con tu equipo (cuando puedas):**
- [ ] ¿Qué se hace con la dirección "texto libre" vs "coordenadas"?
- [ ] ¿`MisPublicacionesRepositoryLocal` se borra o se deja?

---

## Vie 20/11 — Repaso completo Punto 5

**Bloque 1 (30 min): de memoria**
- [ ] Sin mirar nada, contá el Punto 5 **de principio a fin** (5 min).
- [ ] Anotá en qué te trabaste.
- [ ] Releé solo esas partes.

**Bloque 2 (30 min): recorrer el código**
- [ ] Abrir los archivos **en este orden** y decir qué hace cada uno:
  1. `PublicarFotosFragment`
  2. `PublicarArticuloViewModel`
  3. `BorradorRepositoryLocal`
  4. `PublicarResumenFragment`
  5. `MisPublicacionesRepositoryApi`
  6. `MisPublicacionesFragment`
- [ ] Responder 5 preguntas de defensa al azar.

**Opcional:** probar en el emulador las cosas raras (cerrar la app a mitad de carga, fallar una foto).

---

## Lun 23/11 — Punto 7: entender el recorrido

**Bloque 1 (30 min): la foto general**
- [ ] Leer [[Repaso Punto 7 - Ofertas y Negociación#1. Qué hace la funcionalidad]]
- [ ] Leer [[Repaso Punto 7 - Ofertas y Negociación#2. Mapa del código]]
- [ ] Leer `docs/ofertas-api.md` en el repo.
- [ ] Memorizar: **4 estados**, **2 turnos**, **6 h**.

**Bloque 2 (30 min): el flujo**
- [ ] Leer flujo partes **A, B y C** del Punto 7.
- [ ] Abrir `OfertasRepository` y `OfertasApi`.
- [ ] Explicar: *"¿qué pasa desde que ofertás hasta que aparece en Mis ofertas?"*

---

## Mar 24/11 — Punto 7: respuestas, vencimiento y capa de datos

**Bloque 1 (30 min): la negociación**
- [ ] Leer flujo partes **D, E y F** del Punto 7.
- [ ] Abrir `OfertaNegociacion.meTocaResponder`.
- [ ] Explicar: *"¿quién puede aceptar, rechazar o contraofertar y por qué?"*
- [ ] Explicar: *"¿cómo vence una oferta sin cron?"*

**Bloque 2 (30 min): capa de datos**
- [ ] Repasar tus notas [[Retrofit - Consumo de API REST]] e [[Inyección de Dependencias (DI)]].
- [ ] Leer en el Punto 7 (sección 4): *Repository con interfaz*, *Retrofit*, *Callback*, *DTO vs modelo*, *Hilt*.
- [ ] Abrir `OfertasRepositoryRemoto` y `RepositoryModule`.
- [ ] Explicar: *"¿diferencia entre `onResponse` con error y `onFailure`?"*

---

## Mié 25/11 — Punto 7: pantallas y componentes

**Bloque 1 (30 min): Mis ofertas**
- [ ] Leer en el Punto 7 (sección 4): *Fragment y ciclo de vida*, *generacionConsulta*, *TabLayout*, *RecyclerView*.
- [ ] Abrir `MisOfertasFragment` y `OfertaAdapter`.
- [ ] Explicar: *"¿cómo se mantienen siempre actualizadas?"*
- [ ] Explicar: *"¿qué es `generacionConsulta`?"*

**Bloque 2 (30 min): detalle de la oferta**
- [ ] Leer en el Punto 7 (sección 4): *BottomSheetDialogFragment*, *Fragment Result API*, *Diálogos de Material*.
- [ ] Abrir `DetalleOfertaBottomSheet`.
- [ ] Explicar: *"¿cómo le avisa la hoja a Mis ofertas que tiene que recargar?"*
- [ ] Explicar: *"¿por qué la dirección solo se ve si está aceptada?"*

> ⚠️ **No tenés notas propias de TabLayout, BottomSheet ni Fragment Result.** Considerá una nota corta de cada una.

---

## Jue 26/11 — Punto 7: backend + defensa

**Bloque 1 (30 min): backend**
- [ ] Abrir `backend/routers/ofertas.py` y leerlo completo.
- [ ] Abrir en `backend/database.py`: `vencer_ofertas`, `aceptar_oferta`, `contraofertar`.
- [ ] Explicar: *"¿qué valida el backend que la app ya validó?"*

**Bloque 2 (30 min): defensa y cosas raras**
- [ ] Leer [[Repaso Punto 7 - Ofertas y Negociación#5. Preguntas probables de defensa]] **en voz alta**, tapando la respuesta.
- [ ] Leer [[Repaso Punto 7 - Ofertas y Negociación#6. Dudas o cosas raras del código]].
- [ ] Anotar las 3 cosas raras que más te preocupan.

**Pendiente con tu equipo:**
- [ ] ¿Qué se hace con los botones cuando no te toca (Javadoc vs código)?
- [ ] ¿La regla del 50 % debería estar también en el backend?

---

## Vie 27/11 — Repaso completo Punto 7

**Bloque 1 (30 min): de memoria**
- [ ] Sin mirar nada, contá el Punto 7 **de principio a fin** (5 min).
- [ ] Anotá en qué te trabaste.
- [ ] Releé solo esas partes.

**Bloque 2 (30 min): recorrer el código**
- [ ] Abrir los archivos **en este orden** y decir qué hace cada uno:
  1. `DetalleFragment` (solo el diálogo de ofertar)
  2. `OfertasApi`
  3. `OfertasRepositoryRemoto`
  4. `MisOfertasFragment`
  5. `DetalleOfertaBottomSheet`
  6. `backend/routers/ofertas.py`
- [ ] Responder 5 preguntas de defensa al azar.

---

## Lun 30/11 — Simulacro general

**Bloque 1 (30 min): preguntas mezcladas**
- [ ] Elegí 10 preguntas al azar entre las dos notas (sección 5).
- [ ] Respondé **en voz alta**, con cronómetro: **máximo 1 min** cada una.
- [ ] Marcá las que dudaste.

**Bloque 2 (30 min): puntos débiles**
- [ ] Releé solo lo que marcaste.
- [ ] Releé las dos secciones 6 (cosas raras) y preparate **qué vas a decir** si te las preguntan.
- [ ] Repasá la tabla del índice: [[00 - Índice Repaso 5 y 7]].

**Cierre del día:**
- [ ] Dejar todo listo: proyecto abierto, rama `main` actualizada, emulador funcionando.
- [ ] **No estudies nada nuevo a la noche.** Descansá.

---

## Mar 1/12 — Día del oral

- [ ] 15 min a la mañana: leer **solo** la sección 1 y el flujo de cada punto.
- [ ] Leer las **5 cosas raras** del índice.
- [ ] Tener a mano: repo abierto, emulador listo.
- [ ] Respirar. Si no sabés algo, decí **qué sí sabés** y cómo lo averiguarías.

---

## Qué decir si no sabés algo (frases cortas)

- *"Eso lo resolvió mi equipo en otro punto. Mi parte es esta..."*
- *"No lo recuerdo exacto. Lo busco en el código."* (y abrirlo)
- *"Eso quedó incompleto. Se podría resolver así..."*

---

## Seguimiento

| Día | ¿Hecho? | Notas propias (qué me costó) |
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
| Lun 30/11 | ☐ | |
