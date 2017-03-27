# SERVIDORES WEB DE ALTAS PRESTACIONES
## Práctica 2 - Adrián Morente Gabaldón

Comenzamos la práctica con la instalación de ```rsync```, herramienta que utilizaremos para realizar copias de seguridad incrementales entre nuestras máquinas. En nuestra versión de Ubuntu Server (16.10) ya vendrá instalado por defecto. A continuación, configuraremos el servicio SSH para permitir el acceso root entre ambas, accediendo al fichero de configuración de SSH ubicado en ```/etc/ssh/sshd_config``` y modificando su contenido (PermitRootLogin yes).

### 1 - Copia de archivos por SSH
Tras ajustar la configuración del servicio ```ssh``` arriba mencionado, podremos utilizar la herramienta ```scp``` para la copia de archivos vía Secure SHell entre dos máquinas.


### 2 - Clonado de una carpeta entre dos máquinas
Una vez que hemos configurado ```rsync``` y ```ssh``` correctamente, utilizaremos el comando provisto por el profesor en el guión de prácticas para el clonado de directorios entre máquinas. El clonado no se realiza desde la máquina que contiene los archivos a copiar, sino desde la máquina desde la que se quieren obtener dichos archivos. Es decir, si quisiéramos clonar el contenido web del servidor 1, ejecutaríamos el siguiente comando en la terminal del servidor 2:
```bash
rsync -avz -e ssh root@IP_SERVIDOR_1:/var/www/ /var/www/
```
En este caso, la dirección IP del servidor 1 es ```10.0.2.15``` como vimos en la práctica 1. Una vez visto el comando a ejecutar, comprobemos su funcionamiento en la siguiente captura de pantalla. En ella podemos apreciar cómo cambia el contenido del fichero ```index.html``` en la máquina 2, pasando a tener el contenido del fichero con mismo nombre en la máquina 1:

![2ClonadoRsync](https://github.com/adrianmorente/SWAP_UGR/blob/master/Practicas/Practica2/images/2ClonadoRsync.png)


### 3 - Configuración de SSH para acceso sin contraseña
La conexión vía SSH entre máquinas es muy fácil y segura, pero en ciertos casos nos resultará más cómodo configurar dicho servicio de forma que no tengamos que introducir la contraseña en cada una de las conexiones que realicemos. De esta forma, cuando realicemos copias de seguridad y clonados de ficheros entre máquinas, no tendremos la necesidad de introducir la contraseña; lo que resultará muy útil cuando programemos tareas de este tipo.

Para hacer esto, utilizamos comandos integrados en la herramienta SSH, como lo son ```ssh-keygen``` para generar un par de claves pública-privada, y ```ssh-copy-id``` para transferir dichas claves al servidor con el que asociarnos. El proceso es el siguiente:



### 4 - Mantenimiento actualizado de /var/www con crontab
