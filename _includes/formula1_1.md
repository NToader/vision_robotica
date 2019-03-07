# Formula 1

<div style="display:flex; flex-flow:row; align-items:center">
<p>
En la presente página se recogerá el camino seguido para el desarrollo de la práctica de <em>Follow Line</em> 
implementada mediante el framework de <a href="https://academy.jderobot.org/">JdeRobot Academy</a>. Este framework
ofrece una cómoda aproximación al uso del simulador Gazebo, al dar acceso mediante una interfaz web y un notebook de 
Python, a la simulación ejecutada en un servidor. Esta práctica consiste en implementar el comportamiento de un 
monoplaza para que sea capaz de seguir una línea roja, a lo largo de un circuito, de manera rápida y robusta.
</p>
<img src="assets/images/thumbnail_follow_line.png" class="inline" style="max-height:10rem; width:20rem;">

</div>


## Primera aproximación al problema

El primer paso para realizar la práctica fue binarizar la imagen ofrecida por el sistema en dos categorías: 
línea y no línea. Dado que el rango de colores HSV venía dado en la documentación de la práctica, esta tarea 
fue trivial. Una vez generada esta máscara se calculó el punto medio de la línea a una altura de 190 pixeles, respecto
al borde inferior de la imagen. A continuación, se puede observar el resultado de los pasos descritos:

<img src="assets/images/car.jpg" class="inline" width="32%">
<img src="assets/images/car_seg.jpg" class="inline" width="32%">
<img src="assets/images/car_point.jpg" class="inline" width="32%">

La diferencia entre el punto definido anteriomente, al que denominaré **punto guía**, frente al centro de la imagen,
componen la **medida de desviación** del monoplaza respecto a la línea que éste debe seguir. Dada esta medida, se implementó
un sencillo **controlador reactivo** por medio de sentencias `if`. Unido a ello, también se implementó la **_telemetría_** del
monoplaza, en la imagen devuelta por este, ya que el mostrarlo en consola y poder centrarse en los valores mostrados 
era inviable. Posteriormente, los indicadores fueron mejorados para añadir mayor número de parámetros que facilitasen 
el ajuste del sistema.

<div style="display: flex; align-items:center; justify-content:center">
<img src="assets/images/car_tele.jpg" class="inline" width="32%">
<img src="assets/images/car_tele2.jpg" class="inline" width="32%">
</div>

Esta primera aproximación permitió obtener las **primeras sensaciones** respecto al comportamiento del entorno de simulación
y los **limites** en cuanto a velocidad y giro del monoplaza.