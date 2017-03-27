# SERVIDORES WEB DE ALTAS PRESTACIONES
## Práctica 2 - Adrián Morente Gabaldón

Comenzamos la práctica con la instalación de ```rsync```, herramienta que utilizaremos para realizar copias de seguridad incrementales entre nuestras máquinas. En nuestra versión de Ubuntu Server (16.10) ya vendrá instalado por defecto. A continuación, configuraremos el servicio SSH para permitir el acceso root entre ambas (PermitRootLogin yes).

### 1 - Copia de archivos por SSH


### 2 - Clonado de una carpeta entre dos máquinas
Una vez que hemos configurado ```rsync``` y ```ssh``` correctamente, utilizaremos el comando provisto por el profesor en el guión de prácticas para el clonado de directorios entre máquinas. El clonado no se realiza desde la máquina que contiene los archivos a copiar, sino desde la máquina desde la que se quieren obtener dichos archivos. Es decir, si quisiéramos clonar el contenido web del servidor 1, ejecutaríamos el siguiente comando en la terminal del servidor 2:
```bash
rsync -avz -e ssh root@IP_SERVIDOR_1:/var/www/ /var/www/
```
En este caso, la dirección IP del servidor 1 es ```10.0.2.15``` como vimos en la práctica 1. Una vez visto el comando a ejecutar, comprobemos su funcionamiento en la siguiente captura de pantalla. En ella podemos apreciar cómo cambia el contenido del fichero ```index.html``` en la máquina 2, pasando a tener el contenido del fichero con mismo nombre en la máquina 1:

![2ClonadoRsync](https://github.com/adrianmorente/SWAP_UGR/blob/master/Practicas/Practica2/images/2ClonadoRsync.png)

### 3 - Configuración de SSH para acceso sin contraseña


### 4 - Mantenimiento actualizado de /var/www con crontab
