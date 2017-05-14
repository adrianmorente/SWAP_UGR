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

### 2 - Configuración del cortafuegos mediante IPTABLES para asegurar el acceso web (por los puertos HTTP y HTTPS) en la máquina 1.
