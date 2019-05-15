# Primera implementación

Una vez conocido el funcionamiento general del sistema y obtenidos los parámetros extrínsecos del sistema estéreo
el siguiente paso fue la implementación del algoritmo, siguiendo los siguientes pasos:

1. Extracción de puntos de interés para la búsqueda de correspondencia
2. Por cada punto de interés:
   1. Proyección del punto al mundo 3D en un rayo 3D.
   2. Generación de puntos en el rayo.
   3. Proyección de los puntos generados en la imagen 2D de la cámara derecha.
   4. Calculo de la epipolar en base a los puntos proyectados.
   5. Búsqueda de la correspondencia comparando parches, un parche fijo de la imagen izquierda contra cada parche a lo
   largo de la epipolar en la imagen derecha.
   6. Proyección al mundo 3D del punto donde se ha encontrado la correspondencia y generación del respectivo rayo.
   7. Calculo del punto de menor distancia entre los dos rayos del mundo 3D generados.
   8. Dibujo del punto encontrado.

## Puntos de interés
Inicialmente, para la extracción de puntos de interés se utilizó un filtro Canny, el cual proporciona una serie de 
puntos situados en los bordes de los objetos de la imagen. Se optó por este algoritmo para obtener un número razonable 
de puntos para poder comprobar el correcto funcionamiento del algoritmo.

<div style="display: flex; flex-flow: row; align-items: center; justify-content: center">
    <img src="assets/images/canny.png" class="inline" style="width:20%;">
</div>

## Líneas epipolares
Para el cálculo de las líneas epipolares se calculó la ecuación de la recta que une los puntos proyectados en la imagen 
2D. Mediante está ecuación se generaron todos los posibles índices a lo largo de esta línea. En esta primera aproximación
se trataba de una única línea horizontal.

## Correspondencia

Para la búsqueda de la correspondencia se calculaba la distancia media de las diferencias cuadradas (MSE) entre parches
de las dos imágenes generadas por el sistema estéreo. Por cada punto de interés de la imagen izquierda, se generaba
un parche cuyo pixel central fuese el punto de interés. Con el parche anterior se calculaba la MSE contra cada parche
definido a lo largo de la línea epipolar, en la imagen derecha, de forma análoga. El punto de mayor parecido, la
correspondencia, quedaba determinada por la posición del pixel central del parche que menor MSE presentaba. 

## Triangulación
Finalmente, una vez obtenidos los dos rayos en el mundo 3D respecto al sistema de referencia de cada cámara se obtiene
el punto 3D correspondiente al mundo 3D por medio triangulación. En una primera aproximación, se calculó mediante la 
formula genérica de los sistemas estéreo (se usó como referencia la lección 21 del curso CSE486 de la Penn State 
[(Link al PDF consultado)](www.cse.psu.edu/~rtc12/CSE486/lecture21.pdf)). En el curso citado se usa lo que denominan
un diagrama de circuito que permite la extracción del punto buscado a partir de los rayos del punto y los parámetros
extrínsecos.
<div style="display:flex; align-items:center; justify-content:center">
    <img src="assets/images/circuit_diag.png" class="inline" style="width:50%;">
</div>

Solucionando el sistema de ecuaciones (todos los términos son vectores 3 posiciones):

<center><strong>x · R<sub>l</sub>  - y · R<sup>T</sup> · P<sub>r</sub> + z · P<sub>l</sub>  · R<sup>T</sup>  · P<sub>r</sub>  = T</strong></center>

Se obtienen los coeficientes x, y, z para el cálculo de los dos puntos que delimitan el segmento de mínima distancia
entre los dos rayos:

<center><strong>inter<sub>l</sub> = O<sub>l</sub> + x · R<sub>l</sub></strong></center>


<center><strong>inter<sub>r</sub> = O<sub>r</sub> + y · R<sup>T</sup> · P<sub>r</sub></strong></center>

Siendo, por tanto, el punto de mínima distancia entre ambos rayos:

<center><strong>inter = (inter<sub>l</sub> + inter<sub>r</sub>) / 2</strong></center>
