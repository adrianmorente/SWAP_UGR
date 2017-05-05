# SERVIDORES WEB DE ALTAS PRESTACIONES
## Práctica 3 - Adrián Morente Gabaldón

El contenido de esta práctica se basa simplemente en configurar ```nginx``` y ```haproxy``` como balanceadores de carga de nuestra granja web. Para ello, crearemos dos nuevas máquinas virtuales llamadas ```SWAP-3-nginx``` y ```SWAP-3-haproxy```, en cada uno de los cuales instalaremos uno de estos programas de balanceo.

No detallaremos aquí el proceso de instalación y configuración de ninguno de estas herramientas ya que dicho proceso se basa en seguir los pasos dictados por el documento de la práctica. Con seguir ésto, basta para tener los servicios funcionando (salvo errores).

Sin embargo, cabe destacar que la configuración de cualquier servicio de balanceo de carga requerirá aportaciones sencillas como todas las direcciones IP de las máquinas servidoras de nuestra granja web.

Una vez configurado dicho(s) servicio(s), la máquina a la que realizaremos las consultas HTTP será únicamente la tercera, olvidándonos de cada una de las máquinas que componen nuestra granja. Es decir, que para comprobar el funcionamiento de este balanceo de carga, realizaremos solicitudes Curl desde la máquina anfitriona hacia la máquina balanceadora.

### 1 - **nginx** como balanceador de carga
En cuanto al balanceo de carga por medio de nginx, podemos configurar fácilmente (en este caso, en el fichero ubicado en la ruta /etc/nginx/conf.d/default.conf) la máquina balanceadora para que siga diferentes algoritmos que mantengan estable el tráfico de nuestra granja web. El documento nos instruye en cómo configurar dicha máquina para ejecutar algoritmos por prioridad o incluso mediante hashing; pero en este caso nos quedaremos con la opción por defecto, que se trata del algoritmo por turnos ```Round-Robin```.

A continuación comprobamos este funcionamiento desde la máquina anfitriona (en Windows 10 mediante terminal Bash):

![Nginx](https://github.com/adrianmorente/SWAP_UGR/blob/master/Practica3/images/demo-nginx.png)

### 2 - **haproxy** como balanceador de carga
Por otro lado, hablemos de haproxy. Configuraremos este servicio fácilmente sobre el fichero /etc/haproxy/haproxy.cfg, también con los parámetros aportados por el documento de esta práctica. En este caso, tenemos a nuestra disposición un menor volumen de configuraciones que con nginx. Por defecto, el algoritmo de balanceo también se trata de Round-Robin.

Veamos, de la misma forma que en el caso anterior, la ejecución con haproxy:

![Haproxy](https://github.com/adrianmorente/SWAP_UGR/blob/master/Practica3/images/demo-haproxy.png)

### 3 - Medida de prestaciones con ```nginx``` y ```haproxy```

![BenchmarkNginx](https://github.com/adrianmorente/SWAP_UGR/blob/master/Practica3/images/ab-nginx.png)

![BenchmarkHaproxy](https://github.com/adrianmorente/SWAP_UGR/blob/master/Practica3/images/ab-haproxy.png)
