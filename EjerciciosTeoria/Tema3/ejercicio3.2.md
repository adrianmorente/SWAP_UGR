# Ejercicios Tema 3 - SWAP

## Ejercicio T3.2 - Buscar con qué órdenes de terminal o herramientas gráficas podemos configurar bajo Windows y bajo Linux el filtrado y bloqueo de paquetes.

Por términos de seguridad e integridad de los datos de nuestros servidores web, nos interesa realizar un filtrado constante del tráfico que circula por nuestra red. Veamos hoy formas de filtrar y/o bloquear paquetes tanto en Linux como en Windows.

### 1. ***Filtrado y bloqueo de paquetes (Linux)***

Aunque existen otras alternativas (como [tcpdump](http://www.tcpdump.org/tcpdump_man.html)), hablaremos concretamente de la herramienta `iptables`, que hemos utilizado en las prácticas de la asignatura, y que se considera una de las más utilizadas en el mundo Linux para este fin. Ya sabemos cómo funciona, así que veamos rápidamente unas líneas de configuración de la herramienta junto con la explicación de su funcionamiento. Cabe destacar que, como bien sabemos, las primitivas de seguridad nos instan a bloquear de primeras absolutamente todo el tráfico, y a continuación permitir ciertos resquicios:

```bash
$ iptables -P INPUT DROP    #rechazo de cualquier tipo de tráfico de entrada
$ iptables -P OUTPUT DROP   #rechazo de cualquier tipo de tráfico de salida

$ iptables -A INPUT  -i lo -j ACCEPT    #se acepta el tráfico entrante por el interfaz loopback
$ iptables -A OUTPUT -o lo -j ACCEPT    #se acepta el tráfico saliente por el interfaz loopback

#se acepta tráfico entrante por el interfaz Ethernet mediante TCP, para los puertos 22, 80 y 443 para conexiones nuevas y/o establecidas
$ iptables -A INPUT  -i eth0 -p tcp -m multiport --dports 22,80,443 -m state --state NEW,ESTABLISHED -j ACCEPT
```

### 2. ***Filtrado y bloqueo de paquetes (Windows)***

En cuanto a Windows, hablaremos de estas dos opciones:

+ ***Firewall nativo de Windows***: se trata de la herramienta gráfica integrada en el propio sistema operativo. Aunque no parece brindar demasiadas opciones, parecen suficientes para el bloqueo de tráfico proveniente de ciertas subredes completas o de direcciones IP (v4 o v6) concretas. Además, suele ser la herramienta preferida para filtrar y/o bloquear paquetes en Windows, ya que no requiere instalación aparte:

![firewall-Windows](http://support.smartbear.com/support/media/images/support/kb/data/2010/6/10/Windows7Firewall.gif)

+ ***TinyWall*** [tinywall.pados.hu](http://tinywall.pados.hu/): se trata de una herramienta libre para controlar el avanzado firewall de los sistemas Windows más modernos. Se trata de un software ligero y básico, sin pop-ups y por tanto nada intrusivo. Es una herramienta fácil y no requiere de demasiados conocimientos para realizar un simple bloqueo de direcciones IP. No hace falta ni explicar lo intuitiva que es su interfaz:

![tinywall-example](http://cdn1.portalprogramas.org/imagenes/programas/es/643/14643_2.jpg)
