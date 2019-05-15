# Resultados, mejoras y conclusiones
En la presente sección se recogerán los resultados del algoritmo, las mejoras realizadas tras su presentación y las
una breve conclusión. En lo que respecta a resultados de se han realizado diferentes ejecuciones modificando los parámetros
del algoritmo. Se podrá apreciar cierta variabilidad entre diferentes conjuntos de pruebas
con parámetros iguales o equivalentes, que se deben a que se ejecutaron en distintos momentos o sesiones, 
influyendo en ellos la carga del servidor.

## Tamaño de ventana y franja epipolar

Como se puede observar en la siguiente tabla, el tamaño de ventana se puede apreciar
que tiene una importancia notable en el tiempo de procesamiento. A medida que este aumenta también lo hace el tiempo
de ejecución, al ser mayor el número de operaciones a realizar en cada comparación entre parches en la búsqueda de 
correspondencias.

<div style="display: flex; flex-flow: row; align-items: center; justify-content: center; width=100%">
    <table style="width:auto">
      <tr style="background-color: #fafafa">
        <th>Tamaño ventana</th>
        <th>Tiempo medio por punto</th>
        <th>Desviación típica</th>
      </tr>
      <tr>
        <td>7</td>
        <td>38,85 ms</td>
        <td>1,07 ms</td>
      </tr>
      <tr>
         <td>11</td>
         <td>39,24 ms</td>
         <td>1,15 ms</td>
      </tr>
      <tr>
         <td>15</td>
         <td>45,47 ms</td>
         <td>1,22 s</td>
      </tr>
      <tr>
         <td>19</td>
         <td>44,36 ms</td>
         <td>0,9 ms</td>
      </tr>
      <tr>
         <td>23</td>
         <td>45,9 ms</td>
         <td>1,11 ms</td>
      </tr>
      <tr>
         <td>27</td>
         <td>45,46 ms</td>
         <td>0,98 ms</td>
      </tr>
      <tr>
         <td>31</td>
         <td>45,51 ms</td>
         <td>1,6 ms</td>
      </tr>
      <tr>
         <td>35</td>
         <td>48,91 ms</td>
         <td>1,13 ms</td>
      </tr>
      <tr>
         <td>39</td>
         <td>48,87 ms</td>
         <td>1,04 ms</td>
      </tr>
      <tr>
         <td>43</td>
         <td>49,79 ms</td>
         <td>1,19 ms</td>
      </tr>
    </table>
</div>

El mismo hecho ocurre modificado el tamaño de la franja epipolar. A medida que esta aumenta, también lo hace el tiempo
de ejecución puesto que aumenta el número de parches a comprar. Se puede observar que este aumento es lineal, como es
de esperar.

<div style="display: flex; flex-flow: row; align-items: center; justify-content: center; width=100%">
<table style="width:auto">
  <tr style="background-color: #fafafa">
    <th>Tamaño de la franja</th>
    <th>Tiempo medio por punto</th>
    <th>Desviación típica</th>
  </tr>
  <tr>
    <td>1</td>
    <td>45,26 ms</td>
    <td>1,34 ms</td>
  </tr>
  <tr>
     <td>3</td>
     <td>128,12 ms</td>
     <td>2,72 ms</td>
  </tr>
  <tr>
     <td>5</td>
     <td>213,18 ms</td>
     <td>3,28 ms</td>
  </tr>
  <tr>
     <td>7</td>
     <td>289,72 ms</td>
     <td>5,82 ms</td>
  </tr>
  <tr>
     <td>9</td>
     <td>370,30 ms</td>
     <td>10,09 ms</td>
  </tr>
</table>
</div>

## Mejora en la triangulación
De cara a la triangulación se presentaban una serie de puntos erróneos que han sido corregidos eliminando todos aquellos
cuyo rayo de unión entre los puntos de menor distancia entre los rayos proyectados desde cada centro óptico sea menor al
hiper-parámetro `min_triang_dist`. En la imagen inferior, se puede apreciar la eliminación de todos los puntos 
incorrectamente triangulados al establecer el `min_triang_dist` a 10.
<div style="display: flex; flex-flow: row; align-items: center; justify-content: center">
    <img src="assets/images/Ruido.png" class="inline" style="width:80%;">
</div>


## Optimización de la franja epipolar

En la versión presentada en clase se utilizó una franja epipolar parametrizada mediante el hiper-parámetro 
`epipolar_margin`. Sin embargo, esta franja engloba todos los índices a lo largo de la imagen. Para su optimización se
han implementado las siguientes variaciones (implementado tras la presentación):

* **N**: Franja epipolar completa, implementación base.
* **M**: Franja epipolar completa pero eliminados los puntos que no son de puntos de interés.
* **R**: Franja epipolar completa limitada a la posición del punto en la imagen izquierda (las correspondencias solo pueden
ocurrir antes de la posición de este punto)
* **R+M**: Combinación entre el filtrado de puntos de interés y la posicion del punto en la imagen izquierda.
* **R+B**: Franja que termina en el punto de la imagen izquierda y tiene una longitud definida por el hiper-parámetro 
`epipolar_length`.
* **R+B+M**: Franja que termina en el punto de la imagen izquierda, tiene una longitud definida por el hiper-parámetro 
         `epipolar_length` y solo representa los puntos de interés de la imagen derecha.

En base a las variaciones anteriores se han realizado una serie de pruebas de rendimiento, recogidas en la siguiente
tabla:

<div style="display: flex; flex-flow: row; align-items: center; justify-content: center; width:100%">
<table style="width:auto">
  <tr style="background-color: #fafafa">
    <th></th>
    <th><img src="assets/images/1.Normal.jpg" class="inline" style="width:100%;margin:0">
    N</th>
    <th><img src="assets/images/2.Point.jpg" class="inline" style="width:100%;margin:0">R</th>
    <th><img src="assets/images/3.Mask.jpg" class="inline" style="width:100%;margin:0">M</th>
    <th><img src="assets/images/4.Point_mask.jpg" class="inline" style="width:100%;margin:0">R+M</th>
    <th><img src="assets/images/5.Point_back.jpg" class="inline" style="width:100%;margin:0">R+B</th>
    <th><img src="assets/images/6.Point_back_mask.jpg" class="inline" style="width:100%;margin:0">R+B+M</th>
  </tr>
  <tr>
    <td>t</td>
    <td>9,501 ms</td>
    <td>6,426 ms</td>
    <td>5,386 ms</td>
    <td>3,791 ms</td>
    <td>1,407 ms</td>
    <td>1,326 ms</td>
  </tr>
  <tr>
     <td>std</td>
     <td>0,26 ms</td>
     <td>0,12 ms</td>
     <td>0,09 ms</td>
     <td>0,11 ms</td>
     <td>0,06 ms</td>
     <td>0,05 ms</td>
  </tr>
</table>
</div>



##  Resultados cualitativos


En la siguiente figura se puede observar una reconstrucción mediante 15397 puntos.
<div style="display: flex; flex-flow: row; align-items: center; justify-content: center">
<img src="assets/images/frontal.png" class="inline" style="max-height:20rem; width:80rem;">
</div>


El siguiente video muestra una reconstrucción de los puntos de interés generados por el algoritmo Canny, en total 4105 
puntos con un tiempo de ejecución de 4,69 segundos.

<div style="display:flex;justify-content: center;">
    <video muted controls style="width:60%" preload="metadata">
        <source src="assets/video/rec_canny_margin_0.mp4" type="video/mp4">
    </video>
</div>

Mediante el algoritmo Variance se puede obtener una representación más densa, 
15253 puntos con un tiempo de ejecución de 24,90 segundos.

<div style="display:flex;justify-content: center;">
    <video muted controls style="width:60%" preload="metadata">
        <source src="assets/video/rec_variance_margin_0.mp4" type="video/mp4">
    </video>
</div>

Finalmente, ampliando la franja epipolar a 6 pixeles de ancho se procesan 14477 puntos con un tiempo de ejecución 
total de 105,32 segundos.

<div style="display:flex;justify-content: center;">
    <video muted controls style="width:60%" preload="metadata">
        <source src="assets/video/rec_variance_margin_6.mp4" type="video/mp4">
    </video>
</div>

## Conclusiones
Aunque el sistema usado es simulado, se ha realizado una implementación que sea compatible con un sistema real. Este
hecho ha conllevado tener en cuenta gran parte de las dificultades inherentes a los sistemas estéreos. Se ha conseguido
una implementación relativamente rápida, que puede conseguir reconstruir cada punto en `1,326 ms`.
