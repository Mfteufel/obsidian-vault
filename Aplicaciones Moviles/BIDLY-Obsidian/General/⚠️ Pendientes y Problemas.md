# ⚠️ Pendientes y Problemas

## 🔴 Críticos (bloquean la Segunda Entrega)

### 1. Rutas del frontend no coinciden con el backend
El archivo `src/api/endpoints.js` usa rutas propias que no existen en el backend.

**Solución:** Reescribir `endpoints.js` con las rutas reales del back.

### 2. Puerto incorrecto en client.js
```js
// Dice:
'http://10.0.2.2:8080/api'
// Debe decir:
'http://10.0.2.2:8083/api'
```

### 3. Circuito completo integrado (requerimiento obligatorio)
Falta conectar al menos una pantalla del front con el back real.  
**Candidato:** Login → llama a `POST /api/auth/login` → guarda token → navega a Home.

---

## 🟡 Importantes (para la entrega)

### 4. Splash screen (pantalla #1)
No está implementada en el frontend todavía.

### 5. Pantallas 22-23 de Figma
Las pantallas Admin Dashboard y Perfil no pudieron subirse a Figma por límite de rate.  
Hay que hacerlo en una sesión nueva con la cuenta UADE Education.

### 6. Manejo de errores
La Segunda Entrega pide documentar el manejo de errores:
- Campos obligatorios
- Campos opcionales  
- Alertas al usuario
- Sin conexión a internet

---

## ✅ Completado

- [x] 40/40 endpoints del backend funcionando
- [x] Base de datos PostgreSQL con schema completo
- [x] CorsConfig activa (el front puede llamar al back)
- [x] Auth funcionando con token UUID
- [x] Frontend generado con todas las pantallas
- [x] Figma con pantallas 1-21
- [x] Documentación técnica (DOCUMENTACION.md)
- [x] Script SQL de la base de datos
- [x] OpenAPI/Swagger YAML (42 endpoints)
- [x] Paleta de colores definida
- [x] Icono de app y splash screen diseñados
