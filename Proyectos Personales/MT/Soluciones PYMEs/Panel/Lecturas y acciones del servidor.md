---
tags: [mt-panel, arquitectura]
fuente:
  - "src/lib/queries.ts"
  - "src/lib/actions.ts"
  - "src/lib/form.ts"
  - "src/components/forms.tsx"
  - "src/lib/guards.test.ts"
---

# Lecturas y acciones del servidor

**Para qué:** asegurar que cada dato se lea y cada cambio se haga solo con una sesión válida, y que los errores lleguen a la persona que usa el panel con un mensaje claro.

## Lecturas (`queries.ts`)

Cada lectura arranca con `await connection()` y después `await requireUser()`. El primero obliga a leer los datos en cada visita: la base es síncrona y, sin esto, Next los guardaría al compilar la app. El segundo verifica la sesión.

## Acciones (`actions.ts`)

Son las 13 operaciones que cambian datos. Cada una empieza con `await requireUser()`, porque una acción puede llamarse con un pedido directo sin pasar por la pantalla.

| Acción | Para qué sirve |
|---|---|
| `createClient` | Alta de cliente |
| `updateClient` | Cambiar estado y plan de un cliente |
| `createTicket` | Cargar un error, consulta o funcionalidad |
| `updateTicket` | Cambiar estado y sumar horas |
| `triggerEmergency` | Registrar una emergencia |
| `resolveEmergency` | Cerrar una emergencia, con o sin mal uso |
| `addMember` | Sumar una persona al equipo |
| `toggleMember` | Activar o desactivar a una persona |
| `generateShifts` | Completar la rotación de guardias |
| `setShift` | Asignar o quitar la guardia de una semana |
| `savePlan` | Guardar un plan |
| `saveSettings` | Guardar el extra por mal uso |
| `loadDemoData` | Cargar datos de ejemplo |

## Por qué devuelven `{ error }`

En producción, Next oculta el mensaje de los errores lanzados desde una acción. Por eso las acciones no lanzan: devuelven `{ error }` y el formulario lo muestra sin recargar la página. `attempt` atrapa solo los errores previstos (los que se muestran a la persona); cualquier otro error va a la pantalla de error general.

## Validación de formularios (`form.ts`)

Los campos se validan con `required`, `optional`, `intId`, `optNumber` (acepta coma decimal y rechaza negativos) y `oneOf`. Un valor inválido corta el cambio antes de tocar la base.

## Protección automática

La prueba `guards.test.ts` lee el código fuente y falla si una acción no empieza con `requireUser()` o una lectura no empieza con `connection()`. Si alguien olvida la verificación, se entera en las pruebas, no en producción.

## Relacionado

- [[Acceso, sesiones y seguridad]]
- [[Base de datos SQLite]]
- [[Pruebas automáticas]]
- [[Problemas conocidos y notas técnicas]]
- [[Panel MT]]
