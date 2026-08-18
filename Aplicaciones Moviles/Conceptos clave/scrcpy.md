---
tags: [scrcpy, adb, herramientas, testing, desarrollo-aplicaciones-1]
aliases: [scrcpy, Screen Copy, Espejar Android]
---

# CC_scrcpy

> Herramienta gratuita y open source que **espeja y controla** un celular Android desde la computadora, por USB o WiFi.

## Por qué usarlo

- Probás la app en un **dispositivo real** (más rápido y fiel que el emulador)
- Sirve para **grabar el video del TPO** mostrando la app funcionando
- No requiere root ni instalar nada en el celular
- Consume mucha menos RAM que un emulador

---

## Requisitos

| Requisito | Detalle |
|---|---|
| **Depuración USB** | Activarla en el celular |
| **ADB** | Viene con el SDK de [[Android Studio]] |
| **Cable USB** | O red WiFi compartida (modo inalámbrico) |

### Activar Depuración USB

1. Ajustes → Acerca del teléfono
2. Tocar **7 veces** "Número de compilación" → se activan las Opciones de desarrollador
3. Ajustes → Opciones de desarrollador → **Depuración por USB** (ON)

---

## Instalación

```bash
# Windows (con Chocolatey)
choco install scrcpy

# macOS (con Homebrew)
brew install scrcpy

# Linux (Debian/Ubuntu)
sudo apt install scrcpy
```

> En Windows también podés bajar el `.zip` desde GitHub y ejecutar `scrcpy.exe` directo.

---

## Uso básico

```bash
# Verificar que el celular esté conectado
adb devices

# Espejar la pantalla
scrcpy
```

---

## Opciones útiles

| Comando | Qué hace |
|---|---|
| `scrcpy --max-size 1024` | Baja la resolución (más fluido) |
| `scrcpy --max-fps 30` | Limita los FPS |
| `scrcpy --record demo.mp4` | **Graba** la sesión en video |
| `scrcpy --turn-screen-off` | Apaga la pantalla del celu (ahorra batería) |
| `scrcpy --stay-awake` | Evita que se bloquee la pantalla |
| `scrcpy --no-audio` | Sin audio |

### Combo recomendado para grabar el video del TPO

```bash
scrcpy --record entrega1.mp4 --max-size 1080 --stay-awake
```

---

## Modo inalámbrico (WiFi)

```bash
# 1. Con el cable conectado
adb tcpip 5555

# 2. Averiguar la IP del celular (Ajustes → WiFi → red actual)
adb connect 192.168.0.15:5555

# 3. Desconectar el cable y ejecutar
scrcpy
```

> El celular y la compu tienen que estar en la **misma red WiFi**.

---

## Atajos dentro de scrcpy

| Atajo | Acción |
|---|---|
| `Ctrl + C` | Copiar del celular a la compu |
| `Ctrl + V` | Pegar de la compu al celular |
| `Ctrl + O` | Apagar pantalla del dispositivo |
| `Ctrl + Shift + O` | Encender pantalla |
| `Ctrl + F` | Pantalla completa |

---

## Problemas típicos

| Síntoma | Solución |
|---|---|
| `adb devices` no lista nada | Cable sin datos, o falta aceptar el permiso en el celu |
| "unauthorized" | Aceptar el popup de confianza en la pantalla del celular |
| Va con lag | Usar `--max-size 800 --max-fps 30` |
| No conecta por WiFi | Revisar que ambos estén en la misma red |

---

## Relacionado

- [[Clase 01 - Presentación de la materia]]
- [[Android Studio]]
- [[Aplicaciones Nativas]]
