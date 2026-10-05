
```java
public class Main {
    public static void main(String[] args) {
    
        C.InicializarCola();
		C.Acolar(5);
		C.Acolar(9);
		C.Acolar(2);
		C.Acolar(8);
		System.out.println(C.Primero());   // A
		C.Desacolar();
		C.Desacolar();
		C.Acolar(6);
		C.Acolar(11);
		System.out.println(C.Primero());   // B
		C.Desacolar();
		System.out.println(C.Primero());   // C


    }
}
```




1. Indicá los tres valores mostrados.
   
   
   La Cola se inicializa vacia []
   
   se ingresan los valores [5, 9, 2, 8]
   
   A) 5
   
   B) 2
   
   C) 8


2. Escribí el estado final: Primero -> ... -> último.
   
   [8,6,11]

3. ¿Qué elemento fue el último en ingresar?
   
   11

4. ¿Cuál sería el próximo elemento en salir?
   
   8





