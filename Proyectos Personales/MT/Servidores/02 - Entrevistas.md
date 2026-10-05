#SIP #seminario #research

← [[MT - Almacenamiento seguro PYMEs]]

# Entrevistas

Acá va cada entrevista que hagamos. Cuando sumes una nueva, copiá el formato de abajo (## Entrevista N - persona/rubro).

---

## Entrevista 1 - Inmobiliaria (mamá)

**Rubro:** Inmobiliaria — maneja contratos de exclusividad de propiedades de alto valor

**Qué usan hoy:** Google Drive, plan familiar

**Hallazgos:**
- El plan es familiar → hay mezcla de cuentas personales y profesionales. Familiares (como yo) tienen acceso a carpetas de trabajo, y al revés, los colaboradores del trabajo tienen acceso a fotos personales.
- Varios colaboradores tienen acceso a la carpeta compartida, pero sin control granular (todo o nada).
- No hay registro de quién entró a una carpeta.
- No hay registro de si alguien descargó un archivo.
- No hay registro de si alguien sacó una captura de pantalla.
- Si se cae el WiFi, no pueden acceder a nada — dependencia total de internet.

**Dato fuerte:** son contratos de exclusividad de propiedades muy caras → si se filtran, el impacto económico es grande.

---

## Entrevista 2 - Abogada (amiga de mamá)

**Rubro:** Estudio jurídico — documentos confidenciales (secreto profesional)

**Qué usan hoy:** Google Drive + copia local en su computadora

**Hallazgos:**
- Mismos problemas de seguridad que el Drive de la inmobiliaria.
- Los documentos son todavía más sensibles (secreto profesional de por medio).
- Al guardar también en la compu local, aparece un riesgo nuevo: ¿qué pasa si le roban la compu o se rompe? No queda claro si tiene backup de eso.

---

## 🔑 Patrones que se repiten entre las entrevistas

1. **Cero trazabilidad** → nadie sabe quién vio o descargó qué documento.
2. **Cero control de permisos reales** → o tenés acceso a todo, o no tenés nada.
3. **Dependencia de un solo punto de falla** → internet (Drive) o un dispositivo (compu local).
4. **Mezcla de lo personal y lo profesional** en la misma cuenta/plan (hallazgo que no estaba contemplado al principio).

---

## Próximas entrevistas (ideas)
- [ ] Contador/a
- [ ] Otra inmobiliaria (para no depender solo del caso de mamá)
- [ ] Consultora chica

---
Ver también: [[01 - Problema]] · [[03 - Hipótesis de solución]]
