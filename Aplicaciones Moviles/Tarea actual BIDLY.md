## OBJETIVO:
Ajustar la base de datos de manera correcta y verificacion de ENDPOINTS y Luego subirla a Railwal

---

## CONTEXTO:

#### GITHUB

Tenemos el front y back al 80% 

Creamos un repositorio en GITHUB: https://github.com/Walter-FB/Backend---Bidly



#### Railway

Estamos utilizando Railway que contiene el Backend y la base de datos Postgres


<u>Nombre del proyecto:</u> 
bidly-postgres

<u>Project ID:</u> 
b6776e59-845d-416f-a8a6-03172b6fd23e


 **Backend**

<u>Sourcee repo:</u>
https://github.com/Walter-FB/Backend---Bidly


<u>Root Directory:</u>
/bidly-backend


<u>Public Networking:</u>
backend-bidly.up.railway.app



**Base de datos:**

<u>Nombre del proyecto:</u> 
bidly-postgres

<u>Project ID:</u> 
b6776e59-845d-416f-a8a6-03172b6fd23e


tablas:

el Archivo: data-1781731761610.CSV



#### INFORMACION DE COMPAñEROS

Te doy contexto parece que si tocamos las tablas de mas.
Segun un Luchito que las estuvo revisando digo esto:

revise las tablas una por una y para que quede igual al sql tendria que hacer esto:

En Clientes sacar columnas email y passwordhash (contraseña)
En Subastas sacar categoria moneda
En Pujos sacar fechahora
En registroDeSubasta sacar reembolsado

El siguiente paso obligatorio
Hacer nuevas tablas con esos datos que borro lucho
Los endpoints ajustarlos a ese punto.

---

## Restriciones


asegurate de no tocar nada de las tablas principales


no te atrevas a agregar una columna a alguna de NUESTRAS TABLAS que aparescan ahi EstructuraActual.txt


cualquier dato que quieras agregar va a una tabla nueva
y que usen foreigns keys para conectarse con las otras tablas


PGHOST: postgres.railway.internal

PGPORT: 5432

PGUSER: postgres

PGPASSWORD: rzOnesuXFNdajrJWXelLgoOlwkcGJECU

PGDATABASE: railway


---


