# Dos controladores PD

El siguiente paso en el desarrollo de la práctica fue añadir otro par de controladores PD para poder tener diferente
comportamiento en rectas y curvas.

## ¿Curva o Recta?
El primer paso para realizar esta división fue discernir si el vehículo circula en un tramo de recta o de curva, 
teniendo en cuenta que el método debe ser relativamente eficiente. De esta manera se reduce al máximo el tiempo que
el sistema pasa sin dar respuesta a la imagen de entrada. Dado que métodos complejos de regresión quedaban descartados, 
una primera idea fue tomar la diferencia entre la componente horizontal del punto de mayor altura con el de menor altura de la línea
a seguir. Pero esto aproximación tenía el inconveniente de que el sistema fallaría en caso de una curva abierta donde
el punto final y de principio, de la línea, estuviesen alineados.

Como alternativa a la solución anterior se siguió un camino similar, calcular la **ecuación de la recta** (`y = m x + b`) 
que une los puntos de menor y mayor altura. Dada la ecuación de esta recta, se puede calcular el punto
perteneciente a esta, situado a la misma altura que el punto de guía. Ahora, con la diferencia entre las componentes 
horizontales de ambos puntos, se obtiene una medida de discrepancia que puede ser utilizada para determinar si estamos en 
una recta o no. El caso limite, donde la pendiente es infinita, los puntos estan alineads,
 está contemplado generando 0 como discrepancia.

<div style="display: flex; align-items:center; justify-content:center">
<img src="assets/images/car_eq.jpg" class="inline" width="50%">
</div>

En la imagen anterior se puede apreciar:
* Punto superior de la línea (amarillo)
* Punto inferior de la línea (cian) 
* Recta que une el punto inferior y el superior, de la que se ha calculado la ecuación (blanco)
* Punto de la recta anterior a la altura del punto guía (magenta)
* Punto guía (verde)
* **Diferencia entre el punto guía y el punto de la recta de unión (azul marino)**

Por último, cabe considerar que existe el inconveniente de que esta medida es **altamente volátil**, esto es, 
que puede variar demasiado, 
por ello, se ha implementado un **buffer circular** de _n_ posiciones para albergar el histórico de valores de esta variable. 
De esta manera, mediante la **media de este histórico**, se realiza la decisión de si se trata de una recta o una curva en 
base a un límite, situado en 10 pixeles. 

## Dos pares de controladores PD 

Una vez implementado el algoritmo de decisión del tipo de tramo, implementé un cambio de las ganancias de los dos pares
de controladores PD utilizados en el código, en base a el tipo de tramo en el que se encontraba el monoplaza. 
Seguidamente, ajusté los parámetros de ``Kp``, 
``Kd``, ``velocidad máxima``, ``longitud del histórico``, ``threshold`` (discrepancia definida en el apartado anterior para la 
detección del tipo de tramo). Con estos cambios reduje los tiempos a 65 segundos. Un recorte de 15 segundos,
solo por definir un controlador PD para rectas que usase una velocidad mayor, pero no hiciese correcciones tan agresivas
como el de curvas.

<div style="display:flex;justify-content: center;">
    <video muted controls style="width:60%" preload="none">
        <source src="assets/video/result_both.mp4" type="video/mp4">
    </video>
</div>

## La altura del punto guía

Como los resultados anteriores no me parecían suficientes, dado que la simulación no presenta fuerzas que afecten al trazado, la velocidad
debía poder ser incrementada. Por tanto, recordé que había un grado de libertad sin ajustar, la altura del punto
de guía. Cuando este punto es situado muy abajo, cerca del monoplaza, la desviación tolerable es mayor y la capacidad
de reacción ante cambios es menor, ya que, se producen a escasa distancia. Mientras que, si se sitúa en un valor elevado, la desviación 
tolerable es mínima, puesto que, el coche deja fácilmente de _seguir_ la línea, pero se gana en capacidad de reacción. 
Por tanto, para ajustar este parámetro se debe minimizar la magnitud de la discrepancia y a cambio se gana la capacidad 
de reaccionar con mayor antelación a cambios en el tramo, en consecuencia se puede aumentar la velocidad del monoplaza.

Ajustando nuevamente los parámetros del sistema, esta vez con la altura del punto guía prácticamente en lo máximo 
capturado, se reduce el tiempo de vuelta considerablemente, bajando a 27 segundos (25 en simulación), tal y como se puede ver en el 
siguiente video:
<div style="display:flex;justify-content: center;">
    <video muted controls style="width:60%" preload="none">
        <source src="assets/video/result4.mp4" type="video/mp4">
    </video>
</div>

Vuelta en sentido contrario:
<div style="display:flex;justify-content: center;">
    <video muted controls style="width:60%" preload="none">
        <source src="assets/video/result_reverse.mp4" type="video/mp4">
    </video>
</div>

## El tiempo de ejecución

La solución anterior, aunque es buena en lo que respecta a velocidad y robustez, es muy sensible al tiempo de ejecución.
A pesar de que la iteracción que envía imagen, a la aplicación web, presenta un tiempo de ejecución mayor, si lo hacen
también subsiguientes iteraciones afectaran gravemente a la estabilidad del monoplaza. Un ejemplo de estas perturabaciones
se puede observar en la siguiente lista de tiempos de ejecución medidos:
```
6.215 24.973 4.701 4.329 4.512 6.236 23.329 4.272 5.400 7.287 7.1990489959 28.028 28.908 42.073 42.361 80.470 26.750 15.516 21.494 6.053 4.663
```

Para tener en cuenta la posible aparición del fenómeno anterior, se ha introducido el tiempo de ejecución medio de las 
10 últimas iteraciones, como elemento dentro de la _telemetría_ mostrada en la imagen de salida:
<div style="display: flex; align-items:center; justify-content:center">
<img src="assets/images/car_tele_lat.jpg" class="inline" width="50%">
</div>

## El no tan centro

Otras de las variaciones que probé fue modificar la medida de desviación del sistema para considerar el hecho de que
la cámara no está centrada en el monoplaza, como se puede observar en la imagen inferior. 
Al considerar este hecho intenté calcular la discrepancia situado a la derecha
el centro de la imagen, pero esto solo se traducía en un aumento de las oscilaciones del monoplaza, por ello lo 
descarté como posible factor de mejora.

<div style="display: flex; align-items:center; justify-content:center">
<img src="assets/images/eye.png" class="inline" width="50%">
</div>

## Término integral
También llegue a intentar introducir el término integral del controlador, pero la suma de errores se incrementaba
con mucha rapidez y no tenía claro cuando reiniciar su valor o si el acumulador debía ser infinito o tener un horizonte
temporal. Debido a que no tenía claro el valor esperado ni el beneficio aportado por este término, 
para el problema actual (carente de fuerzas a contrarestar), descarte su inclusión en la práctica.


## ¿Línea?

Como ultima mención, también he tenido en cuenta que debe hacer el monoplaza en caso de no detectar una línea. En
principio, el ejercicio consiste en seguir una línea, por tanto, mi solución a este problema se basa en este hecho.
Por ello, la aproximación que he seguido es realizar marcha atrás sin realizar ningún giro hasta volver a encontrar la 
línea. Esto se debe a que, si por algún mal giro el monoplaza ha perdido la línea de vista, la marcha atrás será la 
mejor manera para volver a encontrarla ya que volverá a recorrer una trayectoria similar a la seguida para perderla.
