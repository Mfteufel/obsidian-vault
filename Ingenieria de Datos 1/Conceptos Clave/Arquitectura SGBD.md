### Tres Niveles de Abstracción

#### 1. Nivel Externo (External Level)

- **Vistas individuales de los usuarios**
- Cada usuario ve los datos de forma diferente según sus necesidades
- Usuarios normales, programadores, análisis, DBA

#### 2. Nivel Conceptual (Conceptual Level)

- **Vista global y lógica de los datos** y sus relaciones
- Define la estructura de la base de datos
- **Independencia Lógica**: Los cambios en el nivel externo no afectan al nivel conceptual

#### 3. Nivel Interno (Internal Level)

- **Define el almacenamiento físico de los datos**
- Estructura de archivos, índices, datos estadísticos
- **Independencia Física**: Los cambios en el nivel interno no afectan al nivel conceptual

### Componentes Internos del SGBD

- **Compilador y enlazador**: Procesa consultas en LMD
- **Motor de evaluación de consultas**: Optimiza y ejecuta consultas
- **Procesador de Consultas**
    - Intérprete de LDD (Lenguaje de Definición de Datos)
    - Compilador del LMD y organizador
    - Consultas LMD (Lenguaje de Manipulación de Datos)
- **Gestor de Autorización**: Controla permisos de acceso
- **Gestor de Transacciones**: Asegura ACID
- **Gestor de Memoria**: Administra buffer y caché
- **Gestor de Archivos**: Maneja el almacenamiento

### Almacenamiento

- **DATOS**: Los datos reales de la base de datos
- **DICCIONARIOS DE DATOS**: Metadatos sobre la estructura
- **DATOS ESTADÍSTICOS**: Información para optimización
- **ÍNDICE**: Estructuras para acceso rápido