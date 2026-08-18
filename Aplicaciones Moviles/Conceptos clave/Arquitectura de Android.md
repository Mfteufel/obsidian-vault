---
tags: [android, arquitectura, sistema-operativo, desarrollo-aplicaciones-1]
aliases: [Arquitectura Android, Capas de Android, Android Stack, ART, HAL]
---

# CC_Arquitectura de Android

> Android está organizado en **5 capas**. Tu app vive arriba; las de abajo son el motor invisible.

## Las 5 capas

| # | Capa | Qué hace |
|---|---|---|
| **5** | **Aplicaciones** | Tu app + apps del sistema (Teléfono, Mensajes) + WhatsApp, Maps |
| **4** | **Framework de Aplicaciones** | Activities, ciclo de vida, notificaciones, permisos, APIs de cámara/GPS |
| **3** | **Android Runtime (ART) + Librerías nativas** | ART ejecuta tu app · OpenGL ES · SQLite · SSL/TLS · WebKit |
| **2** | **HAL** (Hardware Abstraction Layer) | Puente hardware ↔ software · drivers por fabricante |
| **1** | **Linux Kernel** | Base del SO: procesos, memoria, hardware, seguridad, energía |

---

## Dónde trabajás vos

```
┌──────────────────────────────┐
│ 5. Aplicaciones              │ ← construís acá
├──────────────────────────────┤
│ 4. Framework                 │ ← usás sus APIs
├──────────────────────────────┤
│ 3. ART + Librerías           │
│ 2. HAL                       │  ← segundo plano
│ 1. Linux Kernel              │
└──────────────────────────────┘
```

- **Construís** la capa 5
- **Usás** las APIs de la capa 4
- Las capas 1–3 funcionan solas, no las tocás

---

## Qué es ART

**Android Runtime** es lo que ejecuta tu app.

- Compila el código a instrucciones del dispositivo (**compilación AOT** — Ahead Of Time)
- Reemplazó a Dalvik (que usaba JIT)
- Resultado: la app arranca más rápido y consume menos batería

---

## Librerías nativas (capa 3)

| Librería | Para qué |
|---|---|
| **SQLite** | Base de datos local |
| **OpenGL ES** | Gráficos 2D/3D |
| **SSL/TLS** | Conexiones seguras |
| **WebKit** | Renderizar HTML dentro de la app |

---

## Por qué importa esto

- Explica por qué tu app **no habla directo con el hardware**: siempre pasa por el Framework
- Explica por qué necesitás **permisos**: la capa 4 es la que controla el acceso
- Explica la **compatibilidad**: el HAL absorbe las diferencias entre fabricantes

---

## Relacionado

- [[Clase 02 - Android Primeros Pasos]]
- [[Actividad]]
- [[Aplicaciones Nativas]]
- [[AndroidManifest]]
- [[Android Studio]]
