# Un controlador real

Tras obtener el punto medio de la línea a seguir y, por tanto, tener el valor de la desviación respecto al centro de la imagen
se prosiguió con mover el vehículo. Para ello se implementó un sencillo controlador reactivo a base de sentencias ```if```.


## Controlador PD 01:20

<img src="assets/images/car_tele.jpg" class="inline" width="49%">


Como ya disponía de los valores para controlar el coche podía pasar a un controlador más complejo. Como ya tenía ciertos 
conocimientos sobre controladores PID aplicados a este tipo de problema decidí implementar un controlador PD, siguiendo 
la siguiente formula:

```
    derivate = deviation - last_deviation
    
    w = - deviation * Kp - derivate * Kd
    
    last_deviation = deviation
```

Pero solo implemente un controlador para el giro y técnicamente también para la velocidad:
```
    v = max(1, w * 3)
```

Una vez ajustados los parámetro ``Kp`` y ``Kd`` el coche conseguía completar el circuito en un tiempo de alrededor de 1 min.
