# SERVIDORES WEB DE ALTAS PRESTACIONES
## Práctica 2 - Adrián Morente Gabaldón

Comenzamos la práctica con la instalación de ```rsync```, herramienta que utilizaremos para realizar copias de seguridad incrementales entre nuestras máquinas. En nuestra versión de Ubuntu Server (16.10) ya vendrá instalado por defecto. A continuación, configuraremos el servicio SSH para permitir el acceso root entre ambas, accediendo al fichero de configuración de SSH ubicado en ```/etc/ssh/sshd_config``` y modificando su contenido (PermitRootLogin yes).

### 1 - Copia de archivos por SSH
Tras ajustar la configuración del servicio ```ssh``` arriba mencionado, podremos utilizar el siguiente comando para realizar un clonado del directorio web en la máquina servidora número 1. El mecanismo se basa en la compresión de dicho directorio y su inmediata transmisión vía ssh. Acto seguido, se imprime su contenido para demostrar su funcionamiento:
```bash
tar zcvf - /var/www/ | ssh adrian@SWAP-1 "cat > ~/bk.tgz"
```
![CopiaArchivosSSH](https://github.com/adrianmorente/SWAP_UGR/blob/master/Practica2/images/CopiaArchivosSSH.png)


### 2 - Clonado de una carpeta entre dos máquinas
Una vez que hemos configurado ```rsync``` y ```ssh``` correctamente, utilizaremos el comando provisto por el profesor en el guión de prácticas para el clonado de directorios entre máquinas. El clonado no se realiza desde la máquina que contiene los archivos a copiar, sino desde la máquina desde la que se quieren obtener dichos archivos. Es decir, si quisiéramos clonar el contenido web del servidor 1, ejecutaríamos el siguiente comando en la terminal del servidor 2:
```bash
rsync -avz -e ssh root@IP_SERVIDOR_1:/var/www/ /var/www/
```
En este caso, la dirección IP del servidor 1 es ```10.0.2.15``` como vimos en la práctica 1. Una vez visto el comando a ejecutar, comprobemos su funcionamiento en la siguiente captura de pantalla. En ella podemos apreciar cómo cambia el contenido del fichero ```index.html``` en la máquina 2, pasando a tener el contenido del fichero con mismo nombre en la máquina 1:

![ClonadoRsync](https://github.com/adrianmorente/SWAP_UGR/blob/master/Practica2/images/ClonadoRsync.png)


### 3 - Configuración de SSH para acceso sin contraseña
La conexión vía SSH entre máquinas es muy fácil y segura, pero en ciertos casos nos resultará más cómodo configurar dicho servicio de forma que no tengamos que introducir la contraseña en cada una de las conexiones que realicemos. De esta forma, cuando realicemos copias de seguridad y clonados de ficheros entre máquinas, no tendremos la necesidad de introducir la contraseña; lo que resultará muy útil cuando programemos tareas de este tipo.

Para hacer esto, utilizamos comandos integrados en la herramienta SSH, como lo son ```ssh-keygen``` para generar un par de claves pública-privada, y ```ssh-copy-id``` para transferir dichas claves al servidor con el que asociarnos. El proceso es el siguiente:

1 - Creación del par clave pública-privada en la máquina nº2.
```bash
ssh-keygen -b 4096 -t rsa
```
2 - Copiado de la clave privada en la otra máquina (ya sea mediante dirección IP o mediante el nombre del host, como será en nuestro caso).
```bash
ssh-copy-id SWAP-1
```

Y eso es todo. Como hemos realizado esta configuración desde el usuario root, ahora podemos acceder como superusuario al servidor sin introducir la clave SSH. Terminemos con una demostración gráfica.

![ComprobacionSSHsinContraseña.png](https://github.com/adrianmorente/SWAP_UGR/blob/master/Practica2/images/ComprobacionSSHsinContraseña.png)


### 4 - Mantenimiento actualizado de /var/www con crontab
En este caso, para mantener sincronizada la máquina 2 con respecto al servidor principal, añadiremos una nueva línea al archivo ```/etc/crontab``` con el siguiente contenido:

![ArchivoCrontab.png](https://github.com/adrianmorente/SWAP_UGR/blob/master/Practica2/images/ArchivoCrontab.png)
