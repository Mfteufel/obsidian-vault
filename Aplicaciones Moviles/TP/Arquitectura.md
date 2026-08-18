# Arquitectura del Sistema

El sistema seguirá una arquitectura cliente-servidor.

## Componentes

App móvil  
↓  
API REST  
↓  
Base de datos

La aplicación móvil se conectará al backend mediante solicitudes HTTP.

El backend se encargará de:

- gestionar usuarios
- validar pujas
- administrar subastas
- registrar pagos
- almacenar historial

## Comunicación

Protocolo: HTTP/HTTPS

Formato de datos: JSON

## Tecnologías posibles

Frontend móvil:
- React Native
- Android Studio

Backend:
- Java Spring Boot
- Node.js

Base de datos:
- PostgreSQL
- MySQL