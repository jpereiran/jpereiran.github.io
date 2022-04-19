---
title:  "¿Son confiables los comentarios en los sitios web? Caso Falabella"
date:   2022-04-18 21:45:16
categories: articles
abstract: Al momento de comprar algo por internet, ¿sueles revisar los comentarios dejados por otras personas?. Entonces deberías de saber que estos no siempre son confiables [...]
---

Aprovechando que durante estos días es el Cyber Wow en Perú, evento en donde diversas marcas tienen descuentos especiales al comprar sus productos por internet, buscaremos entender que tan útiles (o incluso confiables) son los comentarios que dejan otros usuarios como reseñas de sus compras.

Para esto, mediante el uso del **web scraping**, tecnica que ya les enseñe a grandes rasgos en un post anterior, he descargado los comentarios de los productos dejados por los usuarios en la web de [Falabella](https://www.falabella.com.pe/falabella-pe/), los cuales usaremos para analizar que tan fiables son ellos y si existen algunos casos particulares que nos llamen la atención.

Asimismo, esta vez el post será en español ya que el tema a tratar va mas orientado a las personas de Perú. (but if someone needs it to be translated just let me know)

### Introducción

Para poder realizar el análisis, procedí a extraer los comentarios de cada producto de la web con el uso de un script de Python, en el cual mediante el uso de las librerias **Pandas** y **BeautifulSoup** recorrí cada una de las categorías listadas en su barra de busqueda e ingrese a cada uno de los productos listados para obtener sus datos.

Mediante este paso inicial, obtuvimos una lista de 220759 comentarios, los cuales luego de una limpieza rápida de eliminación típica de casos comunes como comentarios con ID repetido o comentarios en blanco, se redujeron a 60227, los cuales cuentan con los siguientes atributos:

<img src="{{ site.baseurl }}/images/posts/requests/2022_04_18_1.PNG" title="Muestra del dataset de comentarios extraidos"> 

Cabe resaltar que no siempre eliminar cosas en blanco o comentarios con algún ID repetido es lo correcto, ya que esto puede indicar un error en la forma en que 
la página web muestra sus resultados o algún otro dato u problema que no se ve tan a la luz. Sin embargo, para simplificar un poco el análisis final se procedión a 
omitir esta doble verificación.

Del mismo modo, esta vez realizaremos los análisis en Excel mediante tablas dinámicas ya que lo que se busca es dar ideas de como realizar un análisis a alto nivel y no tanto de un tutorial propio de programación como en posts anteriores.

### Analizando los comentarios

El primer paso que haremos es crear una tabla dinámica con el dataset de comentarios que tenemos. De esta forma podremos ir encontrando diferentes relaciones entre los comentarios y sus atributos.

Los primeras relaciones que analizaremos son las de cantidad de comentarios por usuario, cantidad de comentarios por producto y cantidad de usuarios por marca, y las ordenaremos de mayor a menor de acuerdo a la cantidad.

<img src="{{ site.baseurl }}/images/posts/requests/2022_04_18_2.PNG" title="Muestra de las 3 relaciones mencionadas"> 

En estas relaciones ya podemos ver ciertas cosas intersantes:
1. Hay varios usuarios con muchos comentarios.
2. Hay ciertos productos que cuentan con muchos comentarios.
3. Hay varias marcas que tienen más comentarios que el resto.

A continuación, ahondaremos más en cada una de estas tres relaciones para ver que cosas interesantes encontramos.

#### Comentarios x Usuario

Para este caso, agregaremos la marca para ver si existe algo particular en los usuarios que tienen mas comentarios, y procederemos a revisar el caso del usuario Jorge.

<img src="{{ site.baseurl }}/images/posts/requests/2022_04_18_3.PNG" title="Comenarios x Usuario con su Marca"> 

Al inicio uno podría pensar que Jorge es un usuario asiduo de estas marcas de productos de cuidado personal. Sin embargo, al entrar al detalle de los comentarios ingresados encontramos lo siguiente:

<img src="{{ site.baseurl }}/images/posts/requests/2022_04_18_4.PNG" title="Detalle de los comenarios del usuario Jorge">

En este detalle podemos observar que todos los comentarios de Jorge fueron ingresados el mismo día y solo con un par de horas de diferencias, lo cual nos hace sospechar que se trata de algun usuario propio de estas marcas, ya que incluso todos estos tienen una calificación de 5 sobre 5. También, al sumar los montos de los productos vemos su compra fue de S/. 3,264 , lo cual es bastante elevado e incluso algunos de sus productos cumplen la misma función.

Asimismo, al buscar un poco la relación de ambas marcas, encontramos que ambas pertenencen a la misma compañia, lo cual aumenta nuestras sospechas sobre la veracidad de estos comentarios, y al ver los comentarios del usuario Marion que también compro productos de la misma marca, encontramos que aplica a los mismo productos que a Jorge y con el mismo comportamiento.

<img src="{{ site.baseurl }}/images/posts/requests/2022_04_18_5.PNG" title="Detalle de los comenarios del usuario Marion">

De forma contraria, al revisar los comentarios de los otros usuarios con varios comentarios (Flor, Teresa y Vicky), no encontramos estos comportamientos sospechosos por lo que no entraremos en más detalles. 

#### Comentarios x Producto


#### Comentarios x Marca




### Conclusión

Mediante el uso de web scraping y una herramienta tan sencilla como es una tabla dinámica en Excel, podemos hacer buenos análisis de datos a un nivel no tan complejo y de manera bastante rápida.

De este modo, hemos encontrado que no siempre los comentarios que aparecen en las webs estan para ayudarnos, ya sea por un tema de usuarios/marcas que aprovechan estos para crear un mejor puntaje para sus productos o por temas propios 
como el propio diseño funcional que se le dio a una web y que al parecer no cuenta con una revisión o mantenimiento tan adecuado. 

### Próximos pasos

Como próximos pasos, usaremos otras librerías de Python para hacer un análisis mas robusto e incluso aplicar algunas técnicas NLP (Procesamiento de Lenguaje Natural) para obtener mejores insights sobre los datos encontrados,
y trataremos de replicar este mismo procedimiento en otras webs similares para ver si suceden casos similares en la competencia.
