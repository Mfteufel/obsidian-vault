# 📱 Frontend — Estructura React Native (Expo)

> ⚠️ El frontend fue generado con Claude Design a partir de los wireframes de Figma.  
> **Importante:** Las rutas de la API del frontend NO coinciden con las del backend. Ver sección de pendientes.

---

## Stack

| Tecnología | Versión |
|---|---|
| React Native | 0.74.5 |
| Expo | ~51.0.0 |
| React Navigation | 6.x |
| AsyncStorage | 1.23.1 |
| Expo Vector Icons | ^14.0.0 |

---

## Cómo levantar el frontend

```bash
cd bidly-front/bidly-front
npm install
npx expo start
```

Para Android: presionar `a` en la terminal (requiere emulador o dispositivo conectado)

---

## Estructura de archivos

```
bidly-front/
├── App.js
├── app.json
├── package.json
└── src/
    ├── api/
    │   ├── client.js       ← HTTP client con Bearer token
    │   └── endpoints.js    ← Mapa de todos los endpoints
    ├── components/
    │   └── ui.js           ← Componentes reutilizables
    ├── context/
    │   └── AuthContext.js  ← Manejo de sesión global
    ├── data/
    │   └── mock.js         ← Datos mock para desarrollo
    ├── navigation/
    │   ├── RootNavigator.js
    │   └── TabNavigator.js
    ├── screens/
    │   ├── AuthScreens.js      ← Login, Registro
    │   ├── HomeScreens.js      ← Home, Filtros
    │   ├── AuctionScreens.js   ← Subasta en vivo, Info producto
    │   ├── PaymentScreens.js   ← Pago, Recibo, Multa, Seguro
    │   ├── AccountScreens.js   ← Perfil, Historial
    │   └── AdminScreens.js     ← Dashboard admin
    └── theme/
        └── theme.js            ← Colores y tipografía
```

---

## Pantallas implementadas

| Pantalla | Archivo | Corresponde a Figma |
|---|---|---|
| Login | AuthScreens.js | #3 |
| Registro | AuthScreens.js | #2 |
| Home | HomeScreens.js | #5 |
| Filtros | HomeScreens.js | #6 |
| Info Producto | HomeScreens.js | #7 |
| Subasta en Vivo | AuctionScreens.js | #8 |
| Notificaciones | AccountScreens.js | #9 |
| Panel Ganador | AuctionScreens.js | #11 |
| Seguro | PaymentScreens.js | #12 |
| Panel de Pago | PaymentScreens.js | #13 |
| Recibo | PaymentScreens.js | #14 |
| Historial | AccountScreens.js | #15-16 |
| Multa | PaymentScreens.js | #17 |
| Admin Dashboard | AdminScreens.js | #22 |
| Perfil | AccountScreens.js | #23 |

---

## Colores (theme.js)

| Color | Hex | Uso |
|---|---|---|
| Primary | `#2E75B6` | Botones, header, nav activo |
| Success | `#52BA1F` | Confirmaciones, pagos OK |
| Warning | `#FF9500` | FAB, tiempo restante |
| Danger | `#DC3545` | Errores, multas |
| Dark | `#1A1A2E` | Textos, fondos oscuros |

---

## ⚠️ Problema crítico — Rutas del frontend

Las rutas en `endpoints.js` **NO coinciden** con las del backend:

| Frontend usa | Backend tiene |
|---|---|
| `/auctions` | `/subastas` |
| `/auctions/{id}/bids` | `/pujos?item={id}` |
| `/payment-methods` | `/clientes/{id}/medios-pago` |
| `/users/me/profile` | `/personas/{id}` |
| `/admin/dashboard` | `/subastas` + `/clientes` |

**Solución:** Editar `src/api/endpoints.js` para que apunte a las rutas reales del back.

---

## URL base del backend

En `src/api/client.js` cambiar:
```js
// Actual (incorrecto):
'http://10.0.2.2:8080/api'

// Correcto para emulador Android:
'http://10.0.2.2:8083/api'
```

> `10.0.2.2` es la IP que usa el emulador Android para llegar al `localhost` de la PC.
