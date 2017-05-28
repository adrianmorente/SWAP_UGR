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
mysqldumpl contactos -u root -p > /root/contactos.sql
mysql -u root -p
mysql> UNLOCK TABLES;
```

![mysqldump-unlock](https://github.com/adrianmorente/SWAP_UGR/blob/master/Practica5/images/mysqldump-unlock.png)

### 3 - Restaurar dicha copia en la segunda máquina (clonado manual de la BD).

![mysqldump-copied](https://github.com/adrianmorente/SWAP_UGR/blob/master/Practica5/images/mysqldump-copied.png)

### 4 - Realizar la configuración maestro-esclavo de los servidores MySQL para que la replicación de datos se realice automáticamente.

![mysql-config](https://github.com/adrianmorente/SWAP_UGR/blob/master/Practica5/images/mysql-config.png)
