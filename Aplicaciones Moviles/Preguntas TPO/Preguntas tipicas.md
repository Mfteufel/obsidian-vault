
**Como funciona la navegacion?**


**"¿Dónde se aplican...?" → en los Módulos**

Los Modules (@Module + @InstallIn) son donde se aplican/definen las reglas de construcción de dependencias (los @Provides), y también donde se determina:

El scope/alcance de la dependencia: @InstallIn(SingletonComponent.class) dice que ese módulo vive mientras vive toda la app. Otras opciones serían ActivityComponent, FragmentComponent, ViewModelComponent, etc., cada uno con un ciclo de vida distinto.
Qué anotaciones de scope se aplican a cada provisión (@Singleton = una sola instancia para toda la app)




runOnIu


REACT NATIVE

Que diferencias hay entre react native y react web:

Se distigue mas que nada por los imports 



Cualquier pantalla que no este en Navigation container expota todo

Se necesitan 3 componentes Navigation controler, Navigator y Screen



Por tengo disponible al navigation?
Lo inyecta la librería

Todos componentes declarados ven a tener disponibles navigation. Puede llegar por un prop


MALA PRACTICA
En un unico contexto guarde el user, preferencias, tema, etc. Cualquier cambio comienza a renderizar 


React android luncher