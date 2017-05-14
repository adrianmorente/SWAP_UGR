# SERVIDORES WEB DE ALTAS PRESTACIONES
## Práctica 4 - Adrián Morente Gabaldón

### 1 - Instalación de un certificado SSL autofirmado para configurar el acceso HTTPS a los servidores.

Un certificado SSL (***Secure Sockets Layer***, *Capa de puertos seguros*) provee a un sitio web de seguridad firmada por el autor. Ésto brinda al visitante la confianza de estar accediendo a un sitio auténtico y confiable. Sin embargo, el protocolo SSL está algo obsoleto, y hoy en día se utiliza **TLS** (***Transport Layer Security***, *Seguridad en la capa de transporte*). De cualquier forma, tanto SSL como TLS se encuentran disponibles en el paquete ***openSSL***.

Aunque existen varias formas de certificar con SSL nuestro sitio web, nosotros optaremos por crear un certificado autofirmado sobre dicho sitio. Cabe destacar que con ésto mostramos al visitante nuestra identidad para *tranquilizar* de alguna forma en cuanto a aspectos de seguridad; ya que dudosamente querríamos cometer algún robo de identidad y datos al usuario mostrando nuestro nombre real, por poner un ejemplo.



```bash
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/apache2/ssl/apache.key -out /etc/apache2/ssl/apache.crt
```

![SSL-Config](https://github.com/adrianmorente/SWAP_UGR/blob/master/Practica4/images/ssl-config.png)

### 2 - Configuración del cortafuegos mediante IPTABLES para asegurar el acceso web (por los puertos HTTP y HTTPS) en la máquina 1.
