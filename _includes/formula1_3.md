# Dos controladores PD

El siguiente paso en el desarrollo de la practica fue añadir otro par de controladores PD para poder tener diferente
comportamiento en rectas que en curvas.

## ¿Curva o Recta?
El primer paso para realizar esta división fue discernir si el vehicula circula en un tramo de recta o de curva, 
teniendo en cuenta que el método debe ser relativamente eficiente. De esta manera se reduce al máximo el tiempo que
el sistema pasa sin dar respuesta a la imagen de entrada. Dado que métodos complejos de regresión quedaban descartados, 
una primera idea fue simplemente tomar la diferencia entre el punto de mayor altura con el de menor altura de la línea
a seguir. Pero esto aproximación tenía el inconveniente de que el sistema fallaría en caso de una curva abierta donde
el punto final y el de principio de la línea esta alineados:

Descartada la solución anterior se pensó en la siguiente. Calcular la ecuación de la línea (`y = m x + b`) que 
conforma la línea que une los puntos de menor y mayor altura. Dada la ecuación de esta recta, se puede calcular el punto
perteneciente a este situado a la misma altura que el punto de guía. Ahora con la diferencia entre las componentes 
horizontales de cada punto se obtiene una medida de discrepancia que puede ser utilizada para determinar si estamos en 
una recta o no.

<div style="display: flex; align-items:center; justify-content:center">
<img src="assets/images/car_eq.jpg" class="inline" width="50%">
</div>
En la imagen anterior se puede apreciar:
* Punto superior de la línea (amarillo)
* Punto inferior de la línea (cian) 
* Recta une el punto inferior y el superior, de la que se ha calculado la ecuación (blanco)
* Punto de la recta anterior a la altura del punto guía (magenta)
* Punto guía (verde)
* **Diferencia entre el punto guía y el punto de la recta de unión (azul marino)**

Pero existe el inconveniente de que esta medida es **altamente volátil**, esto es que, puede variar mucho, 
por ello, se ha implementado un buffer circular de n posiciones para albergar el histórico de valores de esta variable. 
De esta manera, mediante la media de este histórico, se realiza la decisión de si se trata de una recta o una curva en 
base a un threshold. 

## Dos pares de controladores PD 

Una vez implementado el algoritmo de decisión de recta o curvas, implementé un cambio de los parámetros de los dos PD
utilizados en el código en base a el tipo de tramo en el que se encontraba el monoplaza. Ajusté los parámetros de 'Kp', 
'Kd', 'velocidad máxima', 'longitud del histórico', 'threshold' (discrepancia definida en el apartado anterior para la 
dirección del tipo de tramo). Con estos cambios reduje los tiempos a 1 minuto y 05 segundos. Un recorte de 15 segundos,
solo por definir un controlador PD para rectas que usase una velocidad mucho mayor pero no hiciese correcciones tan agresivas
como el de curvas.

<div style="display:flex;justify-content: center;">
    <video muted controls style="width:60%">
        <source src="assets/video/result_both.mp4" type="video/mp4">
    </video>
</div>

## El no tan centro

Otras de las variaciones que probé fue modificar la medida de desviación del sistema para considerar el hecho de que
la cámara no está centrada en el monoplaza, como se puede observar en la imagen inferior. Al considerar este hecho intenté calcular la discrepancia situado a la derecha
del centro de la imagen, pero esto solo se traducía en un aumento de las oscilaciones del monoplaza

<div style="display: flex; align-items:center; justify-content:center">
<img src="assets/images/eye.png" class="inline" width="50%">
</div>

## La altura del punto

Como los resultados anteriores no me parecían suficientes dado que la simulación no presenta inercia?? la velocidad
debía poder ser mucho mayor. Por tanto, recordé que había un punto de libertad aun si ajustar, la altura del punto
de guía. Cuando este punto es situado muy abajo, cerca del monoplaza, la desviación tolerable es mayor y se la capacidad
de reacción ante cambios es menor, ya que está muy cerca. Mientras que, si se sitúa en un valor elevado, la desviación 
tolerable es mínima, ya que el coche deja fácilmente de _seguir_ la línea, pero se gana en capacidad de reacción. 
Por tanto, para ajustar este parámetro se debe minimizar la magnitud de la discrepancia y a cambio se gana la capacidad 
de reaccionar mucho antes a cambios en el tramo, en consecuencia se puede aumentar la velocidad del monoplaza.

Ajustando nuevamente los parámetros del sistema, esta vez con la altura del punto guía prácticamente en lo máximo capturado
se reduce el tiempo de vuelta considerablemente, bajando a 27 segundos, tal y como se puede ver en el siguiente video:
<div style="display:flex;justify-content: center;">
    <video muted controls style="width:60%">
        <source src="assets/video/result4.mp4" type="video/mp4">
    </video>
</div>
En sentido contrario:
<div style="display:flex;justify-content: center;">
    <video muted controls style="width:60%">
        <source src="assets/video/result_reverse.mp4" type="video/mp4">
    </video>
</div>

## El tiempo de ejecución

Eso sí, es altamente sensible al tiempo de ejecución, en cuanto el servidor no sea homogéneo (a pesar de que la) iteración
en la que envía imagen presenta tiempos muy elevados, si se produce algún pico en los tiempos el monoplaza podría quedar
gravemente afectado, en el siguiente bloque algunos tiempos medidos:
```
6.215 24.973 4.701 4.329 4.512 6.236 23.329 4.272 5.400 7.287 7.1990489959 28.028 28.908 42.073 42.361 80.470 26.750 15.516 21.494 6.053 4.663
```

Si se modifica el tiempo del método `execute` el sistema presenta demasiada velocidad como para poder controlarlo.


<div style="display: flex; align-items:center; justify-content:center">
<img src="assets/images/car_tele_lat.jpg" class="inline" width="50%">
</div>
