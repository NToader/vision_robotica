# Formula 1

En la presente página se recogerá el camino seguido para el desarrollo de la práctica de _follow line_ para la 
asignatura de Visión Robótica.

## Primeros pasos

Los primeros pasos para realizar la practica fueron segmentar la imagen para poder conseguir la medida de divergencia entre el centro de la linea y el cnetro de la imagen.
Para ello, se guardo una imagen devulta por el robot que posteriomente fue utilizada localmente para desarrollar la segmentacion. Esta segmentacion se realiza en base a los rangos de colores ofrecidos en el tutorial de la practica siguiendo las instruciones.

A continuacion se puede observar el resultado:

<img src="assets/images/car.jpg" class="inline" width="49%">
<img src="assets/images/car_seg.jpg" class="inline" width="49%">

Seguidamente se calculo el punto medio de la linea segmentada, esto es indmediato siguiendo el tutorial de la practica.

<img src="assets/images/car_point.jpg" class="inline" width="49%">