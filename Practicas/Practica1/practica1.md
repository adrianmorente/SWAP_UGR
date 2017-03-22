# SERVIDORES WEB DE ALTAS PRESTACIONES
## Práctica 1 - Adrián Morente Gabaldón

En este caso, el desarrollo de la práctica es bastante sencillo, ya que de inicio
tan solo tenemos que realizar la instalación de dos máquinas virtuales con Ubuntu
Server (lo haremos para esta vez en su versión 16.10). Para la explicación, nos
ahorraremos la parte de instalación del sistema, ya que es algo que estamos más que
acostumbrados a hacer, y no aportaría nada.

Para empezar, veamos las versiones de Apache instaladas en cada máquina (que serán
las mismas) y los procesos en curso, para verificar que Apache2 está en ejecución
y dando servicio. Para ésto, utilizaremos los comandos provistos por el profesor
en el guión de prácticas, ```apache2 -v``` para consultar la versión de Apache, y
```ps aux | grep apache``` para listar y filtrar los procesos en ejecución.

![CapturaPostInstalacion](https://github.com/adrianmorente/SWAP_UGR/blob/master/Practicas/Practica1/CapturaPostInstalacion.png)

Además, con ```ifconfig``` listamos las direcciones IP de cada host, que será el
que utilicemos a continuación para verificar que existe conexión y acceso entre
ambos. Veamos la solicitud y respuesta de cada una con el protocolo ```ping```:

![CapturaPing](https://github.com/adrianmorente/SWAP_UGR/blob/master/Practicas/Practica1/CapturaPing.png)
