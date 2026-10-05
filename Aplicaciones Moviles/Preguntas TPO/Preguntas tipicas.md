
**Como funciona la navegacion?**


**"¿Dónde se aplican...?" → en los Módulos**

Los Modules (@Module + @InstallIn) son donde se aplican/definen las reglas de construcción de dependencias (los @Provides), y también donde se determina:

El scope/alcance de la dependencia: @InstallIn(SingletonComponent.class) dice que ese módulo vive mientras vive toda la app. Otras opciones serían ActivityComponent, FragmentComponent, ViewModelComponent, etc., cada uno con un ciclo de vida distinto.
Qué anotaciones de scope se aplican a cada provisión (@Singleton = una sola instancia para toda la app)




runOnIu


