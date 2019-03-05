# Controladores PD

Una vez obtenidas unas primeras sensaciones frente al sistema se pasó a la implementación de un controlador P.

## Un controlador PD

Como ya disponía de los valores para controlar el coche podía pasar a un controlador más complejo. Como ya tenía ciertos 
conocimientos sobre controladores PID aplicados a este tipo de problema decidí implementar un controlador PD, siguiendo 
la siguiente formula:

```
    derivate = deviation - last_deviation
    
    w = - deviation * Kp - derivate * Kd
    
    last_deviation = deviation
```

Pero solo implementé un controlador para el giro y la velocidad quedo regulada en base a los valores devueltos por este:
```
    v = max(1, w * 3)
```

Una vez ajustados los parámetro ``Kp`` y ``Kd`` el coche conseguía completar el circuito en un tiempo de alrededor 
de 1:40 min. Tras varios ajustes más conseguí reducir los tiempos alrededor de 1 min, pero fue entonces cuando, se
nos recordó en clase que debíamos _seguir_ la línea. En este caso, mi monoplaza no seguía la línea, más bien la _veía_
a cierta distancia.

## Dos controladores PD

Como el sistema anterior presentaba unas velocidades muy bajas y un control en general bastante pobre, además de las
indicaciones por parte del profesor, implemente dos controladores PD para el control del monoplaza. Uno de estos 
controladores sería el encargado de calcular el giro y el otro, el encargado de calcular la velocidad. Ambos,
utilizarían la formula del controlador de giro anterior. 

```
    derivate = deviation - last_deviation
    
    w = - deviation * Kp - derivate * Kd
    v = max_speed - abs(deviation * Kp + derivate * Kd)
    
    last_deviation = deviation
```

Una vez ajustados los parámetro ``Kp`` y ``Kd`` de ambos controladores, el coche conseguía unos tiempos de vuelta 
de alrededor de 01 minuto y 20 segundos, con un control razonable. 

<video muted controls>
    <source src="assets/video/2.mp4" type="video/mp4">
</video>
