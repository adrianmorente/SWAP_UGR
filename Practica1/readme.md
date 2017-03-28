# SERVIDORES WEB DE ALTAS PRESTACIONES
## Práctica 1 - Adrián Morente Gabaldón

En este caso, el desarrollo de la práctica es bastante sencillo, ya que de inicio tan solo tenemos que realizar la instalación de dos máquinas virtuales con Ubuntu Server (lo haremos para esta vez en su versión 16.10). Para la explicación, nos ahorraremos la parte de instalación del sistema, ya que es algo que estamos más que acostumbrados a hacer, y no aportaría nada.

Para empezar, veamos las versiones de Apache instaladas en cada máquina (que serán las mismas) y los procesos en curso, para verificar que Apache2 está en ejecución y dando servicio. Para ésto, utilizaremos los comandos provistos por el profesor en el guión de prácticas, ```apache2 -v``` para consultar la versión de Apache, y ```ps aux | grep apache``` para listar y filtrar los procesos en ejecución.

![CapturaPostInstalacion](https://github.com/adrianmorente/SWAP_UGR/blob/master/Practicas/Practica1/images/CapturaPostInstalacion.png)

Además, con ```ifconfig``` listamos las direcciones IP de cada host, que será el que utilicemos a continuación para verificar que existe conexión y acceso entre ambos. Veamos la solicitud y respuesta de cada una con el protocolo ```ping```:

![CapturaPing](https://github.com/adrianmorente/SWAP_UGR/blob/master/Practicas/Practica1/images/CapturaPing.png)

Para terminar, queremos ver que nuestras máquinas están realmente sirviendo páginas web, por lo que crearemos un documento HTML en ambas, situándolo en el archivo ```/var/www/html/hola.html```, con el siguiente contenido (sustituyendo X por el número correspondiente a la máquina):
```html
<!DOCTYPE html>
<html>
  <head>
    <title>Probando... 1,2,3...</title>
  </head>
  <body>
    <h1>Soy la máquina X, y ¡esto funciona!</h1>
  </body>
</html>
```

Por último, utilizaremos el comando ```curl http://IP_SERVIDOR/hola.html``` para descargar el archivo HTML ofrecido por la máquina que deseemos. Ésto es, desde la máquina 1 accederemos al servicio HTML de la 2, y viceversa. Terminemos con una muestra gráfica:

![SirviendoHTML](https://github.com/adrianmorente/SWAP_UGR/blob/master/Practicas/Practica1/images/SirviendoHTML.png)
