# SERVIDORES WEB DE ALTAS PRESTACIONES
## Práctica 6 - Adrián Morente Gabaldón

### 1 - Realizar la configuración de dos discos en RAID 1 bajo Ubuntu, automatizando el montaje del dispositivo creado al inicio del sistema.

Para empezar con la configuración del RAID, deberemos crear los discos virtuales de manera externa a nuestras máquinas; es decir, desde la interfaz gráfica de configuración de VirtualBox, y las añadiremos a la máquina virtual donde montaremos dicho RAID. Cabe destacar que el RAID que montaremos será del tipo 1 (*mirroring*) ya que deseamos redundar en espejo la información entre dos discos duros.

Para este caso, ambos tendrán un tamaño máximo de 10GiB reservados de forma dinámica:

![disks-config](https://github.com/adrianmorente/SWAP_UGR/blob/master/Practica6/images/disks-config.png)

Una vez que tenemos los discos creados y asignados a la máquina, la iniciamos, y con el comando `fdisk -l` mostramos las interfaces de disco conectadas a la misma. Veamos en la siguiente captura los directorios (o particiones) asignados a tales discos (*/dev/sdb* y */dev/sdc*):

![fdisk-l](https://github.com/adrianmorente/SWAP_UGR/blob/master/Practica6/images/fdisk-l.png)

---

Hecho esto, ya podemos pasar a crear el RAID 1, que montaremos sobre el dispositivo `/dev/md0` con 2 dispositivos (los discos creados anteriormente). Para ello utilizaremos la herramienta **mdadm** de la siguiente forma:

```bash
sudo mdadm -C /dev/md0 --level=raid1 --raid-devices=2 /dev/sdb /dev/sdc
```

Cabe destacar que con la opción `--level=` indicamos el tipo de RAID deseado, 1 en este caso; y con `--raid-devices=` especificamos el número de dispositivos que deseamos usar en dicho RAID (los ubicados en `sdb` y `sdc` en este caso).

Para terminar, deberemos darle formato y sistema de archivos a la partición creada:

```bash
#creamos el sistema de ficheros ext2 por defecto
sudo mkfs /dev/md0

#creamos la partición /dat y montamos el disposito md0 en ella
sudo mkdir /dat
sudo mount /dev/md0 /dat
```

![raid-create](https://github.com/adrianmorente/SWAP_UGR/blob/master/Practica6/images/raid-create.png)

Una vez realizados estos pasos (ilustrados en la captura de arriba), ya tendremos el RAID creado y funcionando. Podemos comprobarlo fácilmente con el comando `mdadm --detail /dev/md0`, que nos mostrará los dos discos con sus detalles correspondientes y sobre todo, su **estado** (*active* ahora mismo):

![raid-detail](https://github.com/adrianmorente/SWAP_UGR/blob/master/Practica6/images/raid-detail.png)

Para terminar, desearemos que el sistema monte el RAID automáticamente durante el inicio de la máquina. Para ello seguiremos la siguiente secuencia de comandos, modificando el parámetro *UUID* a nuestra necesidad, y habremos terminado:

```bash
#listamos los discos conectados al sistema con su identificador
ls -l /dev/disk/by-uuid/

#incluimos dicho dispositivo en el archivo /etc/fstab para su automontado
echo "UUID=ccbbbbcc-dddd-eeee-ffff-aaabbbcccddd /dat ext2 defaults 0 0" >> /etc/fstab
```

![raid-fstab](https://github.com/adrianmorente/SWAP_UGR/blob/master/Practica6/images/raid-fstab.png)


### 2 - Simular un fallo en uno de los discos del RAID (mediante comandos con el mdadm), retirarlo "en caliente", comprobar que se puede acceder a la información que hay almacenada en el RAID, y por último, añadirlo al conjunto y comprobar que se reconstruye correctamente.

Para comprobar que el RAID funciona cómo debe, es decir, que replica los datos entre dos discos; vamos a pasar a desconectar uno de los dos creados en el punto anterior simulando un error en éste. A continuación, volveremos a añadirlo como si se tratase de un disco nuevo; y observaremos el comportamiento de la configuración del RAID1.

Para simular dicho fallo y eliminar el disco seguiremos los siguientes comandos:

```bash
#fijamos el disco sdb como fallido
sudo mdadm --manage --set-faulty /dev/md0 /dev/sdb

#eliminamos el disco sdb como si estuviera estropeado
sudo mdadm --manage --remove /dev/md0 /dev/sdb
```

A continuación, si volvemos a imprimir el estado del RAID (`mdadm --detail /dev/md0`) vemos cómo se ha eliminado el disco sdb de dicha configuración:

![raid-faulty](https://github.com/adrianmorente/SWAP_UGR/blob/master/Practica6/images/raid-faulty.png)

Para solucionar esto, bastará con añadir un disco "nuevo" (será el mismo, pero como si fuese nuevo). Esto en una sala de servidores equivaldría a desenchufar físicamente un disco roto para conectar uno nuevo. Haremos esto con el simple comando siguiente:

```bash
sudo mdadm --manage --add /dev/md0 /dev/sdb
```

Hecho esto, volveremos a imprimir los detalles (--detail) del RAID y apreciaremos cómo se ha reconocido el disco y se está sincronizando la información con éste: (un 2% de avance nada más enchufarlo):

![raid-rebuilding](https://github.com/adrianmorente/SWAP_UGR/blob/master/Practica6/images/raid-rebuilding.png)
