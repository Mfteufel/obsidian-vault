#SIP #seminario #mvp

← [[MT - Almacenamiento seguro PYMEs]]

# Hipótesis de solución

⚠️ Esto es una idea inicial a validar con research, no la solución definitiva todavía.

## Idea original: Servidor privado de bajo costo

Mini PC configurado como servidor propio, accesible mediante una aplicación, para que la PYME almacene su información sensible sin depender de terceros ni pagar grandes costos de nube.

## Qué problemas de las entrevistas resolvería (a chequear)
- Trazabilidad → registro de quién accede, descarga o modifica cada documento
- Control de permisos real → no todo o nada, sino por usuario/rol
- No depender de un tercero externo (AWS, Azure, Google)
- Separación clara entre uso personal y profesional (a diferencia del plan familiar de Drive)

## Preguntas abiertas sobre esta hipótesis
- [ ] ¿Qué pasa si se corta la luz o se rompe el mini PC? (punto único de falla físico)
- [ ] ¿Cómo se garantiza backup si es un servidor local?
- [ ] ¿Necesita alguien que lo instale/mantenga? (recordar: las PYMEs no tienen IT propio)
- [ ] ¿Costo real del mini PC + mantenimiento vs. costo de Drive/nube actual?

## Otras soluciones posibles a explorar (no cerrar todavía)
- App con control de permisos y trazabilidad que corra sobre un proveedor de nube económico (en vez de servidor propio)
- Servicio de nube "gestionado" pensado específicamente para PYMEs chicas
- Híbrido: servidor local + backup automático en la nube

---
Ver también: [[01 - Problema]] · [[04 - Research y validación]]
