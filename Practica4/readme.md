# SERVIDORES WEB DE ALTAS PRESTACIONES
## Práctica 4 - Adrián Morente Gabaldón

### 1 - Instalación de un certificado SSL autofirmado para configurar el acceso HTTPS a los servidores.

Un certificado SSL (***Secure Sockets Layer***, *Capa de puertos seguros*) provee a un sitio web de seguridad firmada por el autor. Ésto brinda al visitante la confianza de estar accediendo a un sitio auténtico y confiable. Sin embargo, el protocolo SSL está algo obsoleto, y hoy en día se utiliza **TLS** (***Transport Layer Security***, *Seguridad en la capa de transporte*). De cualquier forma, tanto SSL como TLS se encuentran disponibles en el paquete ***openSSL***.

Aunque existen varias formas de certificar con SSL nuestro sitio web, nosotros optaremos por crear un certificado autofirmado sobre dicho sitio. Cabe destacar que con ésto mostramos al visitante nuestra identidad para *tranquilizar* de alguna forma en cuanto a aspectos de seguridad; ya que dudosamente querríamos cometer algún robo de identidad y datos al usuario mostrando nuestro nombre real, por poner un ejemplo.

#### Generación e instalación de un certificado autofirmado

Para empezar, utilizaremos la herramienta `a2enmod` (apache2 enable module); subherramienta de Apache2 destinada a la activación de cualquiera de sus submódulos, y reiniciaremos el servicio Apache2 para hacer efectiva esta activación. Hecho esto, crearemos el directorio `/etc/apache2/ssl` en el que alojar este nuevo certificado.

Acto seguido, ejecutaremos un largo comando, que explicaremos antes por partes:
+ *openssl*: se trata de la herramienta con la que realizamos operaciones relativas a la librería SSL/TLS.
+ *req -x509*: especificamos la necesidad de uso del estándar criptográfico X.509.
+ *-nodes* (no DES): desactivamos el uso del algoritmo *Data Encryption Standard*.
+ *-days 365*: elegimos el número de días durante los cuales tendrá validez nuestro certificado. Un año, en este caso.
+ *-newkey rsa:2048*: aquí creamos una nueva clave cifrada con el algoritmo RSA de 2048 bytes.
+ *-keyout /etc/apache2/ssl/apache.key*: aquí especificamos el archivo en el que alojaremos esta clave RSA.
+ *-out /etc/apache2/ssl/apache.crt*: por último, especificamos la ubicación de nuestro certificado autofirmado.

El proceso completo en cuanto a comandos es el siguiente:

```bash
a2enmod ssl
service apache2 restart
mkdir /etc/apache2/ssl
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/apache2/ssl/apache.key -out /etc/apache2/ssl/apache.crt
```

A continuación, la herramienta openssl nos pedirá la cumplimentación de muchos campos con nuestros datos a los que asociar nuestro certificado identificativo. Veamos esto en la siguiente figura:

![SSL-Config](https://github.com/adrianmorente/SWAP_UGR/blob/master/Practica4/images/ssl-config.png)

Para terminar con el proceso de configuración, solo nos faltará especificarle al servicio Apache2 dónde están alojados nuestros ficheros con la clave privada y el certificado recién creados; cosa que hacemos con los siguientes dos comandos:

```bash
echo "SSLCertificateFile /etc/apache2/ssl/apache.crt" >> /etc/apache2/sites-available/default-ssl
echo "SSLCertificateKeyFile /etc/apache2/ssl/apache.key" >> /etc/apache2/sites-available/default-ssl
```

Por último, activamos el sitio protegido con SSL con el comando `a2ensite default -ssl` *(apache2 enable site)* y reiniciamos una vez más el servicio.

Hecho esto, podremos visitar el sitio a través de HTTPS, aunque debemos tener claro que el navegador nos advertirá de que el sitio contiene un certificado autofirmado cuya autoridad no reconoce; como vemos en la siguiente figura:

![SSL-Prueba](https://github.com/adrianmorente/SWAP_UGR/blob/master/Practica4/images/ssl-prueba.png)

De cualquier forma, podremos aceptar las condiciones y acceder al sitio "no seguro".

### 2 - Configuración del cortafuegos mediante IPTABLES para asegurar el acceso web (por los puertos HTTP y HTTPS) en la máquina 1.

La forma más efectiva de asegurar nuestra granja web es proteger todas las máquinas que la componen mediante la configuración de un cortafuegos. De esta forma, podemos privarla de los accesos indebidos, de forma que solo permitamos la entrada a los usuarios por ciertos puertos (como son los puertos del servicio web, por ejemplo). La premisa es **prohibir todo el acceso** de primeras, y a continuación ir liberando algunos de los accesos de forma personalizada.

Para este caso práctico, cerraremos la entrada por todos los puertos salvo los utilizados por los protocolos HTTP, HTTPS y SSH. De igual forma, permitiremos la salida de paquetes tan solo por dichos puertos, ya que la comunicación TCP/IP se produce mediante entrada/salida de datos.

Para este fin, a modo de cortafuegos usaremos la herramienta ***iptables***; con la que el administrador del sistema puede definir reglas de filtrado de paquetes de entrada y/o salida. Empezaremos por denegar todo el tráfico, pasando después a permitir parte de éste de forma explícita. Además, podremos definir rangos de direcciones IP a las que dotar de ciertos permisos; y mantendremos archivos .log de registro de accesos, intentos y/o errores.

Para hacer esto, haremos uso de las múltiples opciones de configuración que nos brinda la herramienta; las cuales detallaremos paso a paso a continuación:

+ *-F (--flush)*: vaciado de una cadena de reglas, equivale a un vaciado secuencial de todas y cada una de las reglas. Si no se le especifica ninguna cadena, se vacian todas las de la tabla.
+ *-X (--delete-chain)*: borrado de una cadena de reglas. No deben existir reglas ni referencias a otras cadenas dentro de ésta.
+ *-Z (--zero)*: puesta a cero de todos los contadores de paquetes y bytes de la cadena especificada, o de todas si no se determina alguna concreta.
+ *-t nat*: con esta opción especificamos a qué tabla se aplicará la operación que acompaña al comando; siendo en este caso la tabla NAT la afectada.
+ *-P (--policy)*: se especifica la política a seguir para una cadena determinada. Por ejemplo, `iptables -P INPUT DROP` rechazaría (drop) todas las operaciones de tráfico entrante.
+ *-i (--in-interface)*: con esta opción especificamos la interfaz de red por la que configurar la regla que acompaña al comando.
+ *-j (--jump)*: especifica el objetivo de una regla; es decir, qué hacer si algún paquete la cumple.
+ *-A (--append)*: adjunta una o más reglas a la cadena seleccionada.
+ *-m (--match)*: se usa para comprobar una propiedad concreta. En nuestro caso lo usaremos para especificar diversos puertos a la vez para una misma regla.
+ *-p (--protocol)*: se especifica el protocolo que debe cumplir el paquete para satisfacer cualquiera de las reglas.
+ *-L (--list)*: se listan todas las reglas especificadas; o todas las existentes si no se especifica un criterio.
+ *-n (--numeric)*: se activa la salida numérica para la impresión de direcciones IP y números de puertos.
+ *-v (--verbose)*: esta opción activa el listado de todos y cada uno de los parámetros, opciones de reglas, nombres de interfaces, etc.

A modo de prueba, veamos una captura de lo que sería una tabla de configuración de iptables vacía:

![Iptables-empty](https://github.com/adrianmorente/SWAP_UGR/blob/master/Practica4/images/iptables-empty.png)

Pasemos a usar todas estas opciones en el script de configuración provisto por el profesor, acompañada cada línea de un breve recordatorio de su función. Dicho script lo alojaremos bajo el fichero `/etc/rc.local` en la máquina servidora número 1.

```bash
#!/bin/sh

iptables -F           #vaciado de todas las reglas
iptables -X           #borrado de todas las cadenas de reglas
iptables -Z           #puesta a cero de todos los contadores de paquetes y bytes
iptables -t nat -F    #vaciado de las reglas de la tabla NAT

iptables -P INPUT DROP    #rechazo de cualquier tipo de tráfico de entrada
iptables -P OUTPUT DROP   #rechazo de cualquier tipo de tráfico de salida

iptables -A INPUT  -i lo -j ACCEPT    #se acepta el tráfico entrante por el interfaz loopback
iptables -A OUTPUT -o lo -j ACCEPT    #se acepta el tráfico saliente por el interfaz loopback

#se acepta tráfico entrante por el intefaz Ethernet mediante TCP, para los puertos 22, 80 y 443 para conexiones nuevas y/o establecidas
iptables -A INPUT  -i eth0 -p tcp -m multiport --dports 22,80,443 -m state --state NEW,ESTABLISHED -j ACCEPT
#se acepta tráfico saliente por el intefaz Ethernet mediante TCP, para los puertos 22, 80 y 443 para conexiones establecidas
iptables -A OUTPUT -o eth0 -p tcp -m multiport --sports 22,80,443 -m state --state ESTABLISHED -j ACCEPT

iptables -L -n -v     #impresión de todas las reglas, con salida numérica y con todos los nombres y opciones
```

Para terminar, veamos la salida impresa por este script una vez ejecutado y realizada la configuración completa; en la que constarán los pasos realizados; teniendo ahora los puertos de HTTP, HTTPS y SSH con tráfico permitido en ambos sentidos para el protocolo TCP:

![Iptables-script_end](https://github.com/adrianmorente/SWAP_UGR/blob/master/Practica4/images/iptables-script_end.png)
