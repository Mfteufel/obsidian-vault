## Tipos de aplicaciones:

- [[Aplicaciones Nativas]] (App): diseñada para ser ejecutada completamente en dispositivos móviles y accediendo a y utilizando los recursos propios del dispositivo 
  
- [[Aplicaciones Web]]: (Resposive) Aplicación que accedemos atraves del browser del dispositivo. solo procesa la presentación

- [[Aplicaciones Hibridas]]: App que realiza parte del procesamiento de manera local accediendo a alguno recursos pero que accede a un servicio WEB para complementar el procesamiento:

 
 ## **Quick Comparison**

| Aspecto                  | Nativa      | Web                    | Híbrida   |
| ------------------------ | ----------- | ---------------------- | --------- |
| **Rendimiento**          | ⭐⭐⭐⭐⭐       | ⭐⭐                     | ⭐⭐⭐⭐      |
| **Tiempo desarrollo**    | Largo       | Rápido                 | Medio     |
| **Costo**                | Alto        | Bajo                   | Medio     |
| **Acceso a dispositivo** | Total       | Limitado               | Bueno     |
| **Distribución**         | App Store   | URL                    | App Store |
| **Mantenimiento**        | Complejo    | Simple                 | Medio     |
| **Aprobación**           | Obligatoria | Ninguna                | Liviana   |
| **Velocidad**            | Maxima      | Tan rapida como la red | Nativa    |



## Android Studio

Están escritas en Java y Kotlin 

Las app, archivos y recursos son empaquetados en un archivo APK.

Las app viven en su propio entorno de seguridad brindado por un a maquina virtual.

Cada aplicación se ejecuta en su propio proceso de Linux y posee su propio ID de usuario, segmentando recursos y accesos.

Se implementan el principio de menor privilegio para sus app.


### Tipos de aplicacion en android - Componentes

- [[Actividad]]

- [[Servicio]]

- [[Receptor de Transmisión]]

- [[Proveedor de Contenido]]



### Estructura Básica

[[Android - Estructura Básica y Pasaje de Actividades]]

Nuestras actividades comienzan siempre en el método: ​


protected void onCreate(...)​

​

Después vinculamos los componentes gráficos a objetos locales:​

objetoLocal = findViewById(R.id.componenteGrafico);​

​

De aquellos componentes que necesitamos controlar los eventos les asignamos un listener​

objetoLocal.setOnClickListener(new View.OnClickListener() {…}​


### Pasaje entre actividades

Cuando terminamos la interacción con una actividad y queremos pasar a otra vamos a vincularlas mediante un objeto de la clase Intend.​

​

Básicamente me agrega un elemento al stack de actividades de Android y lo vincula a la anterior de forma que podamos volver a la anterior (programado o con el botón atrás) ​

​

Intent aux = new Intent(MainActivity.this, GameActivity.class);​


Dentro del Intent podemos enviar una lista de parámetros con el formato key-value.​

​

Métodos: ​

​

intentName.putExtra(key, value)​

​

​

getIntent().getStringExtra(“key");​

​

​

​

Pasamos el control a la siguiente actividad​

​

startActivity(intentName);​