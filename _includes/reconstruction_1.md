# Reconstrucción 3D - WIP

<div style="display:flex; flex-flow:row; align-items:center">
<p>
A continuación, se detallarán los pasos seguidos para el desarrollo de la práctica de <em>3D Reconstruction</em> 
implementada mediante el framework de <a href="https://unibotics.org/">JdeRobot Academy</a>. Este framework
ofrece una cómoda aproximación al uso del simulador Gazebo, al dar acceso mediante una interfaz web y un notebook de 
Python, a la simulación ejecutada en un servidor. Esta práctica consiste en realizar la reconstrucción 3D de una 
escena por medio de un sistema estéreo canónico formado por dos cámaras.
</p>
<img src="assets/images/thumbnail_reconstruction_3d.png" class="inline" style="max-height:10rem; width:20rem;">
</div>

## Fundamentos teóricos y su aplicación al presente problema
Para el desarrollo de esta práctica se deben utilizar los conocimientos sobre sistemas estereos. 
### Matrices de extrínsecos e intrínsecos
Lo primero a tener en cuenta es la calibración de las cámaras, esto viene definido en las matrices
de parámetros intrínsecos y extrínsecos que son ya proporcionadas por el sistema en ```self.camLeftP.RT```, 
```self.camLeftP.K```, ```self.camRightP.RT``` y ```self.camRightP.K```.
```
            [ 0  0 1  0  ]          [ 277  0  120 0 ]
    RT_l =  [ 0 -1 0 110 ]    K_l = [  0  277 160 0 ]
            [ 1  0 0  0  ]          [  0   0   1  0 ]
            [ 0  0 0  0  ]
            
            [ 0  0 1   0  ]          [ 277  0  120 0 ]
    RT_l =  [ 0 -1 0 -110 ]    K_l = [  0  277 160 0 ]
            [ 1  0 0   0  ]          [  0   0   1  0 ]
            [ 0  0 0   0  ]            
```

Mediante la fórmula ```R = R_r * R_l.t``` y ```T = T_l - R.t * Tr``` se obtienen los parámetros extrínsecos del
sistema estéreo:

```
         [ 1 0 0 ]        [  0  ]
    R =  [ 0 1 0 ]    T = [ 220 ]
         [ 0 0 1 ]        [  0  ]
```

En el vector de traslación se puede apreciar que existe una traslación en Y entre las cámaras pero por las imágenes
producidas por el sistema esta traslación es en X, por ello y por motivos que se verán más adelante, se comprueba 
que el sistema de coordenadas 3D utilizado será (Z,X,Y).

### Funciones auxiliares para la conversión de puntos
Una vez se tienen los parámetros extrínsecos del sistema estéreo ya se puede comenzar a utilizar. Para ello, el 
sistema donde se desarrolla la practica ofrece una serie de métodos que facilitan la conversión de puntos entre
el espacio imagen, óptico y el mundo real. Estas funciones son:

* ``graficToOptical``: convierte coordenadas graficas en coordenadas ópticas
* ``opticalToGrafic``: convierte coordenadas ópticas en coordenadas graficas
* ``backproject``: proyecta de un punto del plano imagen a un punto tridimensional en el mundo real
* ``project``: proyecta un punto 3D del mundo real al plano imagen
* ``getCameraPosition``: extrae las coordenadas del centro óptico de la cámara (también presente la matriz RT anterior)

Mediante el uso de estas funciones se ha desarrollado la práctica.
