# SERVIDORES WEB DE ALTAS PRESTACIONES
## Práctica 5 - Adrián Morente Gabaldón

### 1 - Crear una BD con al menos una tabla y algunos datos.

Para empezar, creamos una nueva base de datos fácilmente con la siguiente secuencia de comandos MySQL, además de añadirle una línea de valores de prueba. Para iniciar sesión en MySQL con permisos de usuario root, ejecutamos el comando `mysql -u root -p`:

```mysql
create database contactos;
use contactos;
create table datos(nombre varchar(100),tlf int);
insert into datos(nombre,tlf) values ("pepe",95834987);
```

Hecho esto, tendríamos nuestra base de datos creada y con la tabla "datos" añadida.

![mysql-firstDB](https://github.com/adrianmorente/SWAP_UGR/blob/master/Practica5/images/mysql-firstDB.png)

### 2 - Realizar la copia de seguridad de la BD completa usando mysqldump.

Para realizar una copia de la base de datos completa en otra máquina, el primer paso a realizar es generar dicha copia en una de las máquinas, que denotaremos como principal, y el resto serán quienes obtengan dicha copia a partir de esta máquina "principal". Para empezar, generaremos esta copia `contactos.sql` usando la herramienta `mysqldump`.

La secuencia de comandos a seguir será la siguiente:

```bash
mysql -u root -p
mysql> FLUSH TABLES WITH READ LOCK;   #para bloquear la escritura en las tablas mientras son copiadas
mysql> QUIT;
mysqldump contactos -u root -p > /root/contactos.sql
mysql -u root -p
mysql> UNLOCK TABLES;
```

![mysqldump-unlock](https://github.com/adrianmorente/SWAP_UGR/blob/master/Practica5/images/mysqldump-unlock.png)

### 3 - Restaurar dicha copia en la segunda máquina (clonado manual de la BD).

Para finalizar con la parte relativa al clonado de la base de datos, tocará irse a la segunda máquina servidora, crear la base de datos de igual nombre que en la primera, e invocar el siguiente comando `scp` que será quien obtenga el archivo .sql de la primera máquina:

```bash
scp root@IP-MAQUINA1:/root/BASEDEDATOS.sql /root/
```

A continuación, bastará con volcar el contenido del archivo obtenido en la nueva base de datos previamente creada. Con esto bastará para tener la base de datos replicada en la segunda máquina. Veamos una captura con los pasos realizados:

![mysqldump-copied](https://github.com/adrianmorente/SWAP_UGR/blob/master/Practica5/images/mysqldump-copied.png)

### 4 - Realizar la configuración maestro-esclavo de los servidores MySQL para que la replicación de datos se realice automáticamente.

Para comenzar con esta configuración, empezaremos desde el lado del Maestro (máquina 1, en nuestro caso); y configuraremos en ambas partes el archivo `/etc/mysql/mysql.conf.d/mysqld.cnf` aplicando los cambios citados en el guión de la práctica:

```bash
...
#bind-address 127.0.0.1
log_error = /var/log/mysql/error.log
server-id = 1
log_bin = /var/log/mysql/bin.log
...
```

Una vez modificados estos campos, reiniciamos el servicio `mysql` como ya sabemos, y pasamos a configurar la máquina esclava; que en este caso, seguirá la misma configuración del mismo fichero exceptuando el campo `server-id`, que tendrá valor ***2***.

Una vez hecho esto, reiniciamos el servicio y volvemos a la máquina maestra, e introducimos los siguientes comandos en mysql:

```mysql
CREATE USER esclavo IDENTIFIED BY 'esclavo';
GRANT REPLICATION SLAVE ON *.* TO 'esclavo'@'%' IDENTIFIED BY 'esclavo';
FLUSH PRIVILEGES;
FLUSH TABLES;
FLUSH TABLES WITH READ LOCK;
```

Con estos comandos, estaremos creando el usuario esclavo con sus credenciales, además de recargar la información de tablas y privilegios, y bloqueando estas tablas ante nuevas escrituras (como ya hicimos antes). Además, ejecutamos el comando `SHOW MASTER STATUS;` para mostrar la información del maestro, tan necesaria para la configuración del esclavo:

![mysql-master](https://github.com/adrianmorente/SWAP_UGR/blob/master/Practica5/images/mysql-master.png)

Visto esto, rellenamos el siguiente comando con la información obtenida en esta última captura para configurar mysql en la parte de la máquina esclava, de forma que empiece a funcionar como tal:

```mysql
CHANGE MASTER TO MASTER_HOST='192.168.56.101', MASTER_USER='esclavo', MASTER_PASSWORD='esclavo', MASTER_LOG_FILE='bin.000001', MASTER_LOG_POS=980, MASTER_PORT=3306;
```

Una vez hecho esto, iniciamos la máquina en modo esclavo y mostramos su estado de configuración y ejecución para comprobar que todo está funcionando correctamente. Ésto lo hacemos con los siguientes comandos en mysql:

```mysql
START SLAVE;

SHOW SLAVE STATUS\G
```

La última orden imprimirá diversas líneas de estado y configuración, pero la que nos interesa es la que comprende el campo ***"Seconds_Behind_Master"***, que nos garantiza que el servicio está funcionando correctamente si su valor es ***distinto de NULL***.

En nuestro caso, como vemos en la siguiente captura, su valor es 0; por lo que el esclavo está funcionando correctamente y actualizando la base de datos con respecto a la máquina 1.

![mysql-slave](https://github.com/adrianmorente/SWAP_UGR/blob/master/Practica5/images/mysql-slave.png)

Para finalizar, veamos una muestra del replicado automático mediante conexión maestro-esclavo. Primero, mostramos los valores de la tabla DATOS en la máquina 2, a continuación insertamos una nueva línea desde la máquina 1; y vemos cómo se añade en la misma tabla de la máquina esclava:

![mysql-master-slave](https://github.com/adrianmorente/SWAP_UGR/blob/master/Practica5/images/mysql-master-slave.png)
