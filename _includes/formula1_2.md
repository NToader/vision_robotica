# Un controlador real

Tras obtener el punto medio de la linea a seguir, y por tanto, tener el valor de la desviacion respecto al centro de la imagen
se prosiguio con mover el vehiculo. Para ello se implemento un sencillo controlador reactivo a base de sentencias ```if```.
Mediante este sencillo controlador me hice una idea de los limites de velocidad y giro soportador por el formula 1 
(posteriormente confirmados por los desarooladores de JdeRobot).

## Controlador PD

<img src="assets/images/car_tele.jpg" class="inline" width="49%">


Como ya disponia de los valores para controlar el coche podia pasar a un controlador mas complejo. Como ya tenia ciertos 
conocimientos sobre controladores PID aplicados a este tipo de problema decidi implementar un controlador PD, sigueindo 
la siguiente formula:

```
    derivate = deviation - last_deviation
    
    w = - deviation * Kp - derivate * Kd
    
    last_deviation = deviation
```

Pero solo implemente un controlador para el giro y tecnicamente tambien para la velocidad:
```
    v = max(1, w * 3)
```

Una vez ajustados los parametro ``Kp`` y ``Kd`` el cocher conseguia completar el circuito en un tiempo de alrededor de 1 min.
