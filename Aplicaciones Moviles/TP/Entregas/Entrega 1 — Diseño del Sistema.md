La primera entrega se centra en **definir cómo será la aplicación antes de comenzar a programar**.  
El objetivo es diseñar la interfaz, la navegación y la estructura del backend.  
  
---  
  
## 1. Wireframes de la aplicación  
  
Se deben diseñar **al menos cinco wireframes en alta definición**.  
  
Un wireframe es un diseño de pantalla que muestra:  
  
- estructura de la interfaz  
- ubicación de botones  
- flujo de navegación  
- colores elegidos  
  
Ejemplos de pantallas que se pueden incluir:  
  
- Login  
- Registro de usuario  
- Lista de subastas  
- Detalle de subasta  
- Pantalla para realizar pujas  
- Historial del usuario  
  
Los wireframes deben mostrar **la paleta de colores seleccionada para la aplicación**.  
  
---  
  
## 2. Plano de despliegue de wireframes (Figma)  
  
Los wireframes deben organizarse en **Figma** mostrando el flujo de navegación.  
  
Esto permite visualizar cómo se mueve el usuario dentro de la aplicación.  
  
Ejemplo de flujo:  
  
Login → Lista de subastas → Detalle de subasta → Puja  
  
---  
  
## 3. Icono de la aplicación  
  
Se debe diseñar el **icono de la app**.  
  
Este icono será el que aparezca:  
  
- en el teléfono  
- en el menú de aplicaciones  
- en la pantalla principal  
  
Debe representar la temática del sistema (por ejemplo, un martillo de subasta).  
  
---  
  
## 4. Splash Screen  
  
La splash screen es la **pantalla inicial que aparece al abrir la aplicación**.  
  
Generalmente incluye:  
  
- logo de la aplicación  
- nombre del sistema  
- colores de la marca  
  
Se muestra durante unos segundos mientras la app se carga.  
  
---  
  
## 5. Definición de la API REST  
  
Se deben definir los endpoints del backend.  
  
Un endpoint es una dirección que permite acceder a una funcionalidad del sistema.  
  
Ejemplos:  
  
POST /usuarios  
Registrar usuario  
  
GET /subastas  
Listar subastas  
  
POST /pujas  
Realizar puja  
  
Para cada endpoint se deben definir:  
  
- parámetros de entrada  
- respuesta del servidor  
- códigos HTTP  
  
Ejemplos de códigos HTTP:  
  
200 → operación exitosa  
404 → recurso no encontrado  
400 → error en los datos enviados  
  
Lista completa de códigos HTTP:  
  
https://www.iana.org/assignments/http-status-codes/http-status-codes.xhtml  
  
La documentación del API puede realizarse con **Swagger**.