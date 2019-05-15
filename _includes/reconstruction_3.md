# Mejoras

Una vez implementada un primera versión de la práctica, se pasó a la mejora de los
distintos pasos del algoritmo.

## Puntos de interés
Para el cálculo de los puntos de interés se incorporaron 3 métodos adicionales con
el objetivo de conseguir puntos de intereses de mayor densidad en comparación a Canny:
* **Laplaciano**: cálculo del laplaciano de la imagen y consideración como puntos de interés 
todos los pixeles cuyo valor absoluto sea mayor a 10.
* **SIFT**: se utiliza el algoritmo SIFT para la búsqueda de puntos de interés.
* **Análisis de varianza**: se considera punto de interés cualquier bloque cuya media de la varianza entre los canales sea 
mayor que 400. Este método es el que mayor densidad de puntos ofrece, en la imagen inferior se puede apreciar que cubre 
prácticamente toda la superficie de los objetos de la escena.

<div style="display: flex; flex-flow: row; align-items: center; justify-content: center">
    <img src="assets/images/pois.jpg" class="inline" style="width:30%;">
</div>

## Medidas de similitud

También se han ampliado las medidas de similitud presentes(MSE) en el sistema con las siguientes:

* **SAD**: suma de distancia absolutas entre los parches.
* **HSV**: ponderación entre el canal H y S de cada parche. Especialmente útil para escenarios reales donde existan 
variaciones importantes en la iluminación entre amabas cámaras.
* **CrossCorrelation**: correlación cruzada entre los dos parches.

## Líneas epipolares

Para el cálculo de las líneas epipolares, se ha generalizado el cálculo para sistemas no canónicos. También se ha 
añadido un hiper-parámetro (```margin```) que indica el número de pixeles a tomar como margen a cada lado de la epipolar
a la hora de la generación de índices para la búsqueda de correspondencias.

A continuación, se pueden observar algunos ejemplos de líneas epipolares donde se ha representado los diferentes
parches a lo largo de la epipolar en color azul y los puntos que definen la epipolar en rojo:
<div style="display: flex; align-items:center; justify-content:center">
<img src="assets/images/imEH.png" class="img-20">
<img src="assets/images/imEV.png" class="img-20">
<img src="assets/images/imI1.png" class="img-20">
<img src="assets/images/imI2.png" class="img-20">
<img src="assets/images/imI3.png" class="img-20">

</div>


## Triangulación

Por último, para la triangulación, aunque el método anterior presentaba buenos resultados, también
se ha implementado una forma genérica para el cálculo de menor distancia entre dos rayos, la forma
vectorial (extraído de [Stackexchange - Mathematics](https://math.stackexchange.com/a/1482836)). 
Este método se basa en que el rayo de menor distancia entre los dos rayos será perpendicular
a ambos, por tanto, define la siguientes ecuaciones:

* Siendo point un punto del rayo y O el centro óptico, el vector de dirección del rayo que los une es:

   <center><strong>p<sub>l</sub> = point<sub>l</sub> - O<sub>l</sub></strong></center>
   
   <center><strong>p<sub>l</sub> = point<sub>r</sub> - O<sub>r</sub></strong></center>
   
* La ecuación de cada rayo es:
   
   <center><strong>P<sub>l</sub>(t) = O<sub>l</sub> + t p<sub>l</sub></strong></center>
   
   <center><strong>P<sub>r</sub>(s) = O<sub>r</sub> + s p<sub>r</sub></strong></center>
   
* El segmento P<sub>l</sub>P<sub>r</sub> debe ser perpendicular a p<sub>l</sub> y a p<sub>r</sub>, siendo por tanto, su
producto escalar zero: 


   <center><strong>(P<sub>l</sub>(t) - P<sub>r</sub>(s)) · p<sub>l</sub> = 0</strong></center>
      
   <center><strong>(P<sub>l</sub>(t) - P<sub>r</sub>(s)) · p<sub>r</sub> = 0</strong></center>

* Desarrollando la ecuación anterior:


   <center><strong>t(p<sub>l</sub> ·  p<sub>l</sub>) - s(p<sub>l</sub> ·  p<sub>r</sub>) = (O<sub>r</sub> - O<sub>l</sub>) · p<sub>l</sub></strong></center> 
      
   <center><strong>t(p<sub>l</sub> ·  p<sub>r</sub>) - s(p<sub>r</sub> ·  p<sub>r</sub>) = (O<sub>r</sub> - O<sub>l</sub>) · p<sub>r</sub></strong></center> 
   
   
* Finalmente se calcula el punto de menor distancia:
<center><strong>inter<sub>l</sub> = O<sub>l</sub> + t p<sub>l</sub></strong></center>

<center><strong>inter<sub>r</sub> =  O<sub>r</sub> + s p<sub>r</sub></strong></center>



<center><strong>inter = (inter<sub>l</sub> + inter<sub>r</sub>) / 2</strong></center>


Con esta implementación extra, se pudo comprobar que la triangulación era correcta con el método anterior, al coincidir
sus resultados.
