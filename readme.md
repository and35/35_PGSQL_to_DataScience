# practica de exploracion en BBDD
el reto es :
los ejecutivos nos piden un dashboard de su  BBDD de rentas de peliculas la cual tiene el siguiente diagrama de entidad relacion: 
![](https://static.platzi.com/media/public/uploads/platzi-movies_c8e24778-e866-4568-bec4-5cbe2af4b59a.png) 

 ## preguntas a resolver preguntas
1.  ¿cuales son las 10 peliculas con mas rentas?
2. ¿cuales son las ciudades con mas rentas?
3. ¿cual fue la evolucion del total de rentas en el año 2017?

## creamos un query para que responda cada pregunta
1. tienes que agregar una columna “lugar” aquí utilizaremos una window function:
```SQL

SELECT 
	peliculas.pelicula_id AS id_, 
	peliculas.titulo AS pelicula, 
	COUNT(*) AS total_de_rentas, 
	ROW_NUMBER() OVER(ORDER BY COUNT(*) DESC) AS lugar 
FROM peliculas 
INNER JOIN inventarios ON (inventarios.pelicula_id = peliculas.pelicula_id) 
INNER JOIN rentas ON (inventarios.inventario_id = rentas.inventario_id) 
GROUP BY peliculas.pelicula_id
ORDER BY total_de_rentas DESC
LIMIT 10;

```
2. Cualquier dato que contenga como referencia un lugar geografico nos servirá para crear una mapa_grafico. Por ejemplo podemos crear una tabla que nos indique el numero de rentas en cada ciudad.



```SQL
 
select ciudades.ciudad_id,
	   ciudades.ciudad,
	   count(*) as renta_por_ciudad
from   ciudades
	inner join direcciones on ciudades.ciudad_id = direcciones.ciudad_id
	inner join tiendas on tiendas.direccion_id = direcciones.direccion_id
	inner join inventarios on inventarios.tienda_id = tiendas.tienda_id
	inner join rentas on inventarios.inventario_id = rentas.inventario_id
group by ciudades.ciudad_id;

```
3. Utilizamos la funcion date_part para seleccionar una parte de la fecha.


```SQL
 
SELECT 
date_part('year', 
pagos.fecha_pago) AS anio, 
date_part('month', pagos.fecha_pago) AS mes, 
COUNT (*) AS Cant_pagos, 
SUM (cantidad) AS Cant_$_Rentas 
FROM pagos 
GROUP BY anio, mes 
ORDER BY anio, mes, cant_pagos 
DESC;

```

## creacion de dashboard
desde PGAdmin exportaremos el resultado de las consultas en formato CSV para caragarlas a power BI. en realidad es mas facil cargar la BBDD completa a powerBI, pero para efectos de mostrar los queries usaremos la importacion de CSV.
este es el resultado final del dashboard 

![image](./reporte%20_rentas_2017.png)
