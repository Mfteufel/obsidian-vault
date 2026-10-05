# Proyecto: ParkLoop

Sos el desarrollador principal de ParkLoop. Arrancamos el MVP desde cero.

Repo: https://github.com/Mfteufel/parkloop

## Qué es
App para conductores que usan torres de estacionamiento rotativas (carrusel vertical, 16 bays, configuración SUV).
El usuario reserva antes de llegar y paga solo el tiempo usado, en bloques de 15 minutos.

## Documentación (leela antes de codear)
Toda la documentación vive en la carpeta docs/ de este repo. Si docs/ no existe todavía o está vacía, avisame antes de seguir — necesito subir los archivos primero.
- docs/ParkLoop_Documentacion_Tecnica.md → especificación completa: casos de uso (UC), historias (HU), reglas (RN), modelo de datos (sección 14). Es largo: buscá por identificador con grep, no lo leas entero.
- docs/Notas_parkloop.pdf → ajustes pedidos para el prototipo.
- docs/Layouts_ParkLoop.pdf → mockups visuales. Son JPEG dentro de una estructura ZIP: extraelos con unzip (pypdf falla).
- docs/parkloop-prototipo.html → prototipo HTML/JS navegable. Usalo como referencia de pantallas y lógica.

Nota: tengo notas organizadas en Obsidian con este mismo contenido resumido por tema (front, back, base de datos, reglas de negocio), pero no asumas que podés leerlas. Si tenés herramientas de Obsidian disponibles, decímelo y las uso como índice rápido; si no, trabajá solo con docs/.

## Stack (decidido)
- App: React Native con Expo + TypeScript. Tiene que correr en iOS, Android y web.
- Backend: Node.js + TypeScript + Express.
- Base de datos: PostgreSQL en Docker (docker-compose).
- ORM: Prisma.
- Monorepo:
  - apps/mobile
  - apps/api
  - packages/shared → tipos compartidos entre app y API

## Reglas de arquitectura
1. Multi-sitio y multi-torre desde el día uno, aunque arranquemos con un solo sitio.
2. Nada del fabricante hardcodeado. Medidas máximas, peso y tiempos de ciclo viven en la tabla MODELO_TORRE.
3. La torre (PLC) y el lector de patentes (LPR) van detrás de una interfaz. En el MVP usamos mocks.
4. Plata siempre en centavos (enteros). Nunca float.
5. Reintentos automáticos SOLO en lecturas (GET). Crear reservas o cobros tiene que ser idempotente (clave de idempotencia). Nunca duplicar un POST.
6. Nunca guardar el número completo de tarjeta. DNI, patente y ubicación son datos personales (Ley 25.326).

## Modelo de datos
Implementá las entidades de la sección 14 del doc técnico. Agregados según las Notas:
- USUARIO: sumar dni, fecha_nacimiento, domicilio y datos de licencia de conducir.
- VEHICULO: patente, marca, modelo, alto_mm, largo_mm, ancho_mm, peso_kg (dimensiones obligatorias) y cédula.

## Reglas de negocio clave
- Cobro en bloques de 15 min, con 10 min de tolerancia.
- Planes: Sin plan (walk-in), Básico Deambulante, Plus, Full, Ilimitado. Cada uno con banco de horas y tarifa de excedente.
- Si una sesión supera el banco disponible, se divide: parte banco + parte excedente.
- Compatibilidad: si el auto no entra en el bay (medidas o peso), mostrar alerta de que no es compatible.
Detalle exacto en RN-01, RN-03, RN-04, RN-05, RN-06, RN-11 y UC-07.

## Pantallas (onboarding + 5 tabs)
Tabs: Perfil, Reservar, Acceso, Mi auto, Banco.
Pedidos de las Notas:
- Perfil: foto, plan actual, banco de horas, datos del auto, opción de cambiar plan.
- Escaneo de DNI con la cámara: leer el código PDF417 del dorso y autocompletar nombre, DNI y fecha de nacimiento.
- Licencia física: también tiene PDF417. Licencia digital (Mi Argentina): QR que vence a las 24 hs, no guardarlo como dato fijo.
- Marca y tipo de auto: selectores tipo scroll.
- Reserva: mapa interactivo (no imagen fija), hora de entrada exacta, duración con precisión de minutos usando un picker tipo scroll (como el temporizador de iPhone). Reemplaza los botones fijos de 1/2/4/8 hs.
- Cálculo automático del costo estimado.
- En esta etapa los botones tienen que ser interactivos aunque no procesen datos reales.

## Fuera de alcance por ahora (decisiones abiertas)
No implementar. Dejar marcado como pendiente:
- Pagos con Mercado Pago.
- Consola de operador (UC-12 a UC-17).
- Diseño final de Mi auto y Banco (usar una versión simple).
- Sección de seguros.
- Infraestructura en la nube.

## Primera tarea
1. Confirmá que el repo está clonado y que docs/ tiene los archivos. Si falta algo, avisame.
2. Leé los docs.
3. Proponeme un plan por fases. NO escribas código hasta que lo apruebe.
4. Fase 1 esperada:
   - Monorepo + docker-compose con PostgreSQL.
   - schema.prisma con el modelo de datos + seed con datos de prueba (3 torres: Palermo, Recoleta y Belgrano; Recoleta completa).
   - API con endpoints básicos: health, sitios, vehículos, reservas.
   - App Expo con navegación de 5 tabs y datos mock.
5. Creá un CLAUDE.md en la raíz con un resumen de este contexto, para las próximas sesiones.
6. Trabajá en una rama nueva (no en main) y andá haciendo commits chicos por paso.

## Cómo comunicarte conmigo
- Frases cortas.
- Estructura clara (títulos y listas).
- Resaltá lo importante.
- Explicá cada paso antes de hacerlo.
- Si algo del doc no cierra, o está marcado como [SUPUESTO — VALIDAR], preguntame antes de decidir.