# Controladores PD

Una vez obtenidas unas primeras sensaciones frente al sistema se pasó a la implementación de un controlador PD. 

## Un controlador PD

Como ya disponía de los valores para controlar el monoplaza podía pasar a un controlador más complejo. Como ya 
tenía ciertos conocimientos sobre controladores PID, aplicados a este tipo de problema, decidí implementar un 
**controlador PD** para el giro, siguiendo la siguiente formula:

```
    derivate = deviation - last_deviation
    
    w = - deviation * Kp - derivate * Kd
    
    last_deviation = deviation
```

Por su parte, la velocidad, quedo regulada en base a los valores devueltos por el controlador PD del giro, de tal manera
que la velocidad mínima siempre fuese 1 y la máxima 10:

```
    v = max(1, min(10, 16 / (w + 0.0001)))
```

Para la experimentación comencé ajustando la ganancia proporcional (``Kp``), situando la ganancia derivativa (``Kd``) a 0, 
hasta tener unas oscilaciones controladas. Tras ello, ajuste la ganancia derivativa hasta tener un comportamiento
razonable a la hora de seguir la línea. Con esta sencilla implementación conseguí un tiempo de 100 segundos, que, 
posteriormente, ajustando más las ganancias reduje hasta los 40 segundos. Pero fue entonces cuando, se
nos recordó, en clase, que debíamos _seguir_ la línea. En este caso, mi monoplaza no seguía la línea, más bien la _veía_
a cierta distancia. Por tanto, a pesar de los prometedores tiempos que alcanzaba el sistema no eran válidos.

## Dos controladores PD

Como el sistema anterior presentaba una velocidad baja y un control, en general, bastante pobre, sumado ello a las
indicaciones por parte del profesor, implemente **dos controladores PD** para el control del monoplaza. Uno de estos 
controladores sería el encargado de calcular el **giro** y, el otro, el encargado de calcular la **velocidad**. Ambos,
utilizarían la formula del controlador de giro anterior. Con la salvedad que para el controlador PD de la velocidad
se introduce el termino de ``max_speed`` para limitar los valores que este puede generar.

```
    derivate = deviation - last_deviation
    
    w = - deviation * Kp - derivate * Kd
    v = max_speed - abs(deviation * Kp + derivate * Kd)
    
    last_deviation = deviation
```

Nuevamente, fueron ajustados la ganancia proporcional y derivativa de ambos controladores. Con ello, el monoplaza
conseguía unos tiempos de vuelta de alrededor de 80 segundos, con un control razonable y lo más importante,
_siguiendo_ la línea.

<div style="display:flex;justify-content: center;">
    <video muted controls style="width:60%" preload="none">
       <source src="assets/video/2.mp4" type="video/mp4">
    </video>
</div>