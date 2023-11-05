# J2D23_Hackaton

Èric Martínez Illamola

## 1. Introducción

Partimos de dos datasets:

- **df_A:** dataset de los precios de los alquileres por barrio. 584 rows × 8 columns

- **df_B:** dataset de los sonidos por barrio. 18980 rows × 7 columns

Tras transformar y unir los datasets obtenemos:

- **df**: 70 rows × 272 columns. Donde cada fila es un barrio distinto y cada columna es una variable (4 columnas identificadoras (barrio y distrito), 8 variables de los precios del alquiler y 260 variables de los sonidos).

Las 8 variables de los precios del alquiler las podemos analizar con cierta facilidad, pero en el caso de las variables de los sonidos, 260 variables son muchas y esto nos dificulta el análisis. A las 260 variables de los sonidos les aplicaremos el Análisis de Componentes Pricipales (PCA) para reducir su dimensionalidad.

## 2. Depuración de los datos

**Limpieza básica de los datos:**

- Eliminamos la columna 'Any' de df_A, ya que en todas las filas aparece el mismo valor (2017).

- Detectar y tratar los valores null: df_A tiene 38 null, df_B no tiene ninguno.

- Comprobamos los duplicados: df_A y df_B no tienen duplicados.

- La columna 'Valor' de df_B está en formato object (string) y los valores aparecen con el símbolo %, por ejemplo 7.73%. Eliminamos el símbolo % y convertimos la columna a formato float.

**Valores null**

En df_A existen 8 entradas (o filas) por cada barrio. La columna 'Preu' de df_A tiene 38 valores null, que pertenecen a 6 barrios distintos. 

Estos 6 barrios con valores null, los podemos clasificar en 3 tipos:

- **8 de 8 valores son null:** No disponemos de ningún dato. Eliminaremos las filas referentes a estos barrios, pero lo haremos más adelante, después de haber unificado los dos datasets. 

- **6 de 8 valores son null:** Los dos datos de que disponemos hacen referencia al trimestre 2, los 6 datos que faltan son del resto de trimestres.'Rellenamos' los valores null tomando como referencia los dos valores del trimestre 2 (uno 'mensual (Euros/mes)', el otro 'superfície (Euros/m2 mes)').

- **2 de 8 valores son null:** Nos faltan los datos del trimestre 2. Hacemos lo mismo que en el caso anterior, pero tomando como referencia la media de los otros tres trimestres.

**Transformar los datos**

El siguiente paso es transformar los datos para poder unir los dos dataframe (df). Los dos df comparten 4 columnas (Codi_Districte, Nom_Districte, Codi_Barri, Nom_Barri), es decir, aportan información sobre los 73 barrios de Barcelona. Por lo tanto, queremos que tanto df_A como df_B tengan 73 filas (una fila por cada barrio), así podremos unirlos agregando sus columnas.

Ahora mismo tenemos:

- df_A: 584 rows × 7 columns

- df_B: 18980 rows × 7 columns

Queremos obtener:

- df_A: 73 rows × 12 columns

- df_B: 73 rows × 264 columns

Empecemos con df_A. Ya hemos dicho antes que df_A contiene 8 filas por cada barrio, es decir, de cada barrio tenemos 8 datos sobre el alquiler: 4 trimestres x 2 métricas del alquiler (mensual y superficie) = 8 variables distintas. Al juntar las 8 variables, que ahora son 8 filas distintas, en una misma fila, pasaremos de 584 a 73 filas (584/8=73). Al final tendremos 12 columnas: las 4 columnas comunes (barrio y distrito) y las 8 columnas sobre el alquiler.

Con df_B ocurre algo parecido, pero en lugar de 8 variables distintas tenemos 260. En resumen, df_B contiene 260 filas por cada barrio, es decir, de cada barrio tenemos 260 datos sobre los sonidos de ese barrio: 10 intensidades x 26 tipos de sonido (dependiendo si provienen de parques, industrias, peatones...) = 260 variables distintas. Al juntar las 260 variables, que ahora son 260 filas distintas, en una misma fila, pasaremos de 18980 a 73 filas (18980/260=73). Al final tendremos 264 columnas: las 4 columnas comunes (barrio y distrito) y las 260 columnas sobre los sonidos.

## 3. Resultados

Los sonidos de los barrios parecen tener cierta correlación con el precio de los alquileres por superficie (euros / m2 mes). En el caso del precio de los alquileres mensuales (euros / mes) la correlación parece ser bastante más débil. 

En la Figura 17 vemos como existen 3 PC que tienen una correlación entre 28% y 44% con Lloguer_superficie (euros/ m2 mes). En la Figura 18 hemos hecho otro gráfico 3D, esta vez representando PC2, PC8 y PC32. 

En este gráfico vemos varias cosas:

- PC2: Los barrios con Lloguer_superficie bajo suelen tener valores de PC2 positivos o cercanos a cero, mientras que los barrios con Lloguer_superficie medio y alto suelen tener valores de PC2 negativos. Ya antes habíamos detectado que PC2 tiene cierta relación con los datos de los alquileres.

- PC32: Los barrios con Lloguer_superficie bajo suelen tener valores de PC32 negativos. Los barrios con Lloguer_superficie alto suelen tener valores de PC32 positivos. Los barrios con Lloguer_superficie medio tienen valores de PC32 tanto positivos como negativos.

- PC8: Los barrios con Lloguer_superficie alto suelen tener valores de PC8 negativos. Los barrios con Lloguer_superficie bajo y medio tienen valores de PC8 tanto positivos como negativos.

Por último, destacar que hemos obtenido df_PCA3, nuestro dataset unificado y comprimido: manteniendo el 99% de la variación de las 260 variables de los sonidos, en sólo 35 PC. Podríamos utilizar df_PCA3 para continuar con este análisis o para realizar otros nuevos. 

## 4. Conclusiones

Hemos consegido detectar cierta relación entre algunos aspectos de los datos de los sonidos (PC2, PC8 y PC32) y el precio de los alquileres por superficie (euros / m2 mes), dado que el dataset principal es el de los alquileres, este parece el objetivo prioritario. Podriamos volver a descomponer esos tres PC en los diferentes tipos de sonidos, pero ya hemos visto antes que cada PC está compuesto por muchas variables. Lo que sí podríamos hacer es, a partir de datos de sonido de un nuevo barrio (o los tres barrios que hemos eliminado previamente por no disponer de datos sobre el precio de los alquileres), obtener PC2, PC8 y PC32 (utilizando los loadings) y en base a eso intentar predecir el rango del precio de los alquileres por superficie (euros / m2 mes). 