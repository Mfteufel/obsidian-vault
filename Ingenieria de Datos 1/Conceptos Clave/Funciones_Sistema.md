---
tags: [base-de-datos, sql-server, tsql, funciones-sistema]
aliases: [Funciones del sistema, Built-in functions, Funciones integradas SQL]
---

# CC_Funciones del Sistema

> Funciones **integradas en SQL Server** que realizan cálculos y transformaciones de datos. Se usan con `SELECT`.

---

## Agregado

Ya cubiertas en [[Operadores_Agregacion]].

| Función | Descripción |
|---|---|
| `AVG()` | Promedio de un conjunto |
| `COUNT()` | Número de elementos |
| `MAX()` | Valor máximo |
| `MIN()` | Valor mínimo |
| `SUM()` | Suma de valores |

---

## Cadena (String)

| Función | Descripción |
|---|---|
| `CHARINDEX()` | Retorna la posición de una subcadena |
| `CONCAT()` | Concatena cadenas |
| `LEFT(cadena, n)` | Extrae n caracteres desde la izquierda |
| `RIGHT(cadena, n)` | Extrae n caracteres desde la derecha |
| `LEN()` | Longitud sin espacios finales |
| `LOWER()` | Convierte a minúsculas |
| `UPPER()` | Convierte a mayúsculas |
| `LTRIM()` | Elimina espacios iniciales |
| `RTRIM()` | Elimina espacios finales |
| `SUBSTRING(cadena, inicio, largo)` | Extrae parte de una cadena |
| `REPLACE(cadena, viejo, nuevo)` | Reemplaza valores en una cadena |

```sql
SELECT TOP 10 LEN([Nombre]) AS LongitudNombre
FROM Empleado
ORDER BY LEN([Nombre]) DESC;
```

---

## Fecha y Hora

| Función | Descripción |
|---|---|
| `GETDATE()` | Fecha y hora actual del servidor |
| `SYSDATETIME()` | Fecha y hora con más precisión (datetime2) |
| `DATEADD(parte, n, fecha)` | Agrega n unidades a una fecha |
| `DATEDIFF(parte, inicio, fin)` | Diferencia entre dos fechas |
| `DATEFROMPARTS(año, mes, día)` | Construye una fecha |
| `DATENAME(parte, fecha)` | Nombre de la parte de fecha |
| `DAY()` / `MONTH()` / `YEAR()` | Extrae día, mes o año |
| `EOMONTH(fecha)` | Último día del mes |
| `ISDATE()` | Verifica si un valor es una fecha válida |

```sql
SELECT DATEADD(month, 11, '2019/01/05');
-- Resultado: 2019-12-05 00:00:00.000
```

---

## Conversión

| Función | Descripción |
|---|---|
| `CAST(expr AS tipo)` | Convierte a otro tipo de dato |
| `CONVERT(tipo, expr, estilo)` | Como CAST, con formato para fechas |
| `PARSE(cadena AS tipo)` | Convierte cadena a fecha/número |
| `TRY_CAST()` | Como CAST, devuelve NULL si falla |
| `TRY_CONVERT()` | Como CONVERT, devuelve NULL si falla |
| `TRY_PARSE()` | Como PARSE, devuelve NULL si falla |

```sql
SELECT
    CAST(25.5 AS FLOAT)          AS NUM_FLOTANTE,
    CAST('2023-01-01' AS DATE)   AS FECHA,
    CAST(13 AS DECIMAL(4,2))     AS DECIMALES,
    CAST(13.05 AS INT)           AS ENTERO;
```

---

## Matemáticas

| Función | Descripción |
|---|---|
| `ABS()` | Valor absoluto |
| `ROUND(n, decimales)` | Redondea a precisión indicada |
| `SQRT()` | Raíz cuadrada |
| `SQUARE()` | Cuadrado de un valor |
| `SIGN()` | Signo: +1, -1 o 0 |
| `EXP()` | Valor exponencial |
| `LOG()` | Logaritmo natural |
| `SIN()` / `ASIN()` / `TAN()` | Funciones trigonométricas |

```sql
SELECT ROUND(123.4567, 2) AS Resultado;
-- Resultado: 123.46
```

---

## Categorías (Ranking / Window functions)

Requieren la cláusula `OVER()`.

| Función | Descripción |
|---|---|
| `ROW_NUMBER()` | Número de fila único, sin empates |
| `RANK()` | Rango con saltos en empates |
| `DENSE_RANK()` | Rango sin saltos en empates |
| `NTILE(n)` | Divide filas en n grupos |

```sql
SELECT ID_Empleado, NombreEmpleado, Sueldo,
    ROW_NUMBER() OVER (ORDER BY Sueldo DESC) AS NumeroFila,
    RANK()       OVER (ORDER BY Sueldo DESC) AS Rango,
    DENSE_RANK() OVER (ORDER BY Sueldo DESC) AS RangoDenso,
    NTILE(3)     OVER (ORDER BY Sueldo DESC) AS Tercil
FROM Empleados;
```

---

## Analíticas (Window functions avanzadas)

| Función | Descripción |
|---|---|
| `LAG(col, n)` | Valor de la fila anterior |
| `LEAD(col, n)` | Valor de la fila siguiente |
| `FIRST_VALUE()` | Primer valor en el conjunto ordenado |
| `LAST_VALUE()` | Último valor en el conjunto ordenado |
| `CUME_DIST()` | Distribución acumulativa |
| `PERCENT_RANK()` | Rango porcentual |
| `PERCENTILE_CONT(p)` | Percentil continuo (ej. mediana con 0.5) |
| `PERCENTILE_DISC(p)` | Percentil discreto |

```sql
SELECT NombreEmpleado, Sueldo,
    LAG(Sueldo, 1)   OVER (ORDER BY Sueldo DESC) AS SueldoAnterior,
    LEAD(Sueldo, 1)  OVER (ORDER BY Sueldo DESC) AS SueldoSiguiente,
    PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY Sueldo) OVER () AS Mediana
FROM Empleados;
```

---

## Configuración (@@)

Ver tabla completa en [[Variables_TSQL]].

| Variable | Descripción |
|---|---|
| `@@VERSION` | Versión de SQL Server |
| `@@SERVERNAME` | Nombre del servidor |
| `@@SPID` | ID del proceso actual |
| `@@MAX_CONNECTIONS` | Máximo de conexiones permitidas |
| `@@CPU_BUSY` | Tiempo de CPU usado |

---

## Relacionado

- [[Operadores_Agregacion]] — SUM, AVG, COUNT, MIN, MAX con álgebra relacional
- [[Variables_TSQL]] — variables de sistema @@
- [[Funciones_Usuario]] — funciones definidas por el usuario
- [[Clase 9 - Procedimientos y Funciones]]
