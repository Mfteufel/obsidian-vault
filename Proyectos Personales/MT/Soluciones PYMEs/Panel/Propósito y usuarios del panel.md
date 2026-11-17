---
tags: [mt-panel, introduccion]
fuente: [README.md, src/components/nav.tsx, "src/app/(panel)/page.tsx"]
---

# Propósito y usuarios del panel

El panel reúne en un solo lugar lo que MT hace con cada cliente PYME: en qué etapa de venta está, qué plan tiene, qué pidió y qué emergencias tuvo. Antes esa información estaba repartida; ahora cada cliente tiene su historia completa.

## El ciclo del negocio

1. **Relevamiento:** se conoce a la empresa y lo que necesita.
2. **Demo:** se muestra una prueba, que tiene un límite de 7 días.
3. **Cliente activo:** elige un plan de seguimiento (Básico, Medio o Alto).
4. **Soporte:** los pedidos llegan como tickets con plazo de respuesta.
5. **Emergencias:** cuando el sistema del cliente se cae, se registra y lo atiende la guardia.

Cada paso tiene su pantalla. El detalle está en [[Clientes y embudo de venta]], [[Tickets y plazos de respuesta]] y [[Emergencias y cobros]].

## Quién lo usa

- El equipo de MT, 4 personas, cada una con su usuario y contraseña.
- No hay registro público: los usuarios se crean desde la terminal (ver [[Acceso, sesiones y seguridad]]).
- Los clientes no entran al panel.

## Qué no hace (hoy)

- No factura ni cobra. Registra cuánto corresponde cobrar por una emergencia, pero el cobro ocurre fuera del panel.
- No tiene los precios: se cargan en [[Planes y precios]] y, hasta que se definan, aparecen como "Sin definir".

## Por qué una web propia y sencilla

Es una sola aplicación que guarda todo en un archivo de base de datos. Eso la hace fácil de entender, de respaldar y de mantener sin depender de servicios externos para los datos.

## Relacionado

- [[Panel MT]]
- [[Recorrido por las pantallas]]
- [[Glosario del panel]]
- [[Planes y precios]]
- [[Acceso, sesiones y seguridad]]
