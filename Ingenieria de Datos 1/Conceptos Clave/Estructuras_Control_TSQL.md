---
tags: [base-de-datos, sql-server, tsql, control-flujo]
aliases: [IF ELSE, WHILE, CASE, TRY CATCH, Control de flujo T-SQL]
---

# CC_Estructuras de Control T-SQL

> Permiten controlar el **flujo de ejecución** de un script según condiciones lógicas.

---

## Bloques de código (Párrafos)

| Tipo | Descripción | Cuándo usarlo |
|---|---|---|
| **Implícito** | Sin `BEGIN/END` | Solo 1 instrucción |
| **Explícito** | Con `BEGIN ... END` | Múltiples instrucciones |

```sql
-- Implícito (1 sola instrucción)
IF @Edad >= 18
    PRINT 'Mayor de edad';

-- Explícito (varias instrucciones)
IF @Edad >= 18
BEGIN
    PRINT 'Mayor de edad';
    INSERT INTO Log VALUES ('Acceso permitido');
END
```

---

## IF ... ELSE

```sql
IF condición_booleana
BEGIN
    -- código si verdadero
END
ELSE
BEGIN
    -- código si falso
END
```

> ⚠️ Los `NULL` pueden alterar la lógica de dos estados — tenerlos en cuenta.

```sql
DECLARE @Edad INT = 20;
IF @Edad >= 18
    PRINT 'Es mayor de edad.';
ELSE
    PRINT 'Es menor de edad.';
```

---

## CASE

Evalúa una expresión y devuelve un resultado según el valor o condición.

### Sencillo — compara contra valores fijos
```sql
DECLARE @DiaSemana INT = 3;
SELECT
    CASE @DiaSemana
        WHEN 1 THEN 'Lunes'
        WHEN 2 THEN 'Martes'
        WHEN 3 THEN 'Miércoles'
        WHEN 4 THEN 'Jueves'
        WHEN 5 THEN 'Viernes'
        ELSE 'Fin de semana'
    END AS Dia;
```

### Búsqueda — evalúa condiciones independientes
```sql
DECLARE @Calificacion INT = 85;
SELECT
    CASE
        WHEN @Calificacion >= 90 THEN 'A'
        WHEN @Calificacion >= 80 THEN 'B'
        WHEN @Calificacion >= 70 THEN 'C'
        ELSE 'F'
    END AS CalificacionLetra;
```

---

## WHILE

Repite un bloque **mientras la condición sea verdadera**.

```sql
WHILE condición_booleana
BEGIN
    -- instrucciones
    [ BREAK ]    -- sale del bucle inmediatamente
    [ CONTINUE ] -- salta al inicio del bucle
END
```

```sql
DECLARE @Contador INT = 1;
WHILE @Contador <= 10
BEGIN
    IF @Contador = 3
    BEGIN
        SET @Contador = @Contador + 1;
        CONTINUE; -- salta el 3
    END
    IF @Contador = 5 BREAK; -- para en el 5
    PRINT 'Iteración: ' + CAST(@Contador AS NVARCHAR(10));
    SET @Contador = @Contador + 1;
END
```

---

## TRY ... CATCH

Captura y maneja errores en tiempo de ejecución.

```sql
BEGIN TRY
    -- código que puede fallar
END TRY
BEGIN CATCH
    -- código que se ejecuta si hay error
    PRINT 'Error: ' + ERROR_MESSAGE();
END CATCH
```

```sql
DECLARE @Resultado INT;
BEGIN TRY
    SET @Resultado = 10 / 0; -- error: división por cero
END TRY
BEGIN CATCH
    PRINT 'Ocurrió un error: ' + ERROR_MESSAGE();
    SET @Resultado = 0;
END CATCH
```

---

## Relacionado

- [[Variables_TSQL]] — variables usadas en las estructuras de control
- [[Procedimientos_Almacenados]] — donde se usan estas estructuras
- [[Clase 9 - Procedimientos y Funciones]]
