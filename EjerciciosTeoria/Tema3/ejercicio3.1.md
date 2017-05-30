# Ejercicios Tema 3 - SWAP

## Ejercicio T3.1 - Buscar con qué órdenes de terminal o herramientas gráficas podemos configurar bajo Windows y bajo Linux el enrutamiento del tráfico de un servidor para pasar el tráfico desde una subred a otra.

Por cualquier razón, llegará el momento en el que queramos que cierto tráfico no sea procesado en cierta máquina sino que sea redirigido a otra de nuestra granja web; por lo que veremos algunas herramientas y formas de hacer esto, tanto en Linux como en Windows.

![Rerouting...](https://i0.wp.com/www.olgcparish.net/wp-content/uploads/2016/09/reroute-logo-1.png?fit=3101%2C786&ssl=1)

### 1. ***Enrutamiento del tráfico de un servidor a otro (Linux)***

Por parte de Linux, podemos realizar fácilmente esto mediante comandos de terminal, utilizando las herramientas de `iptables`:

```bash
# activamos el modo forwarding de la máquina
$ sysctl net.ipv4.ip_forward=1

# activamos el enrutamiento a la máquina X.Y.Z.W a través del puerto [PUERTO], con protocolo TCP
$ iptables -t nat -A PREROUTING -p tcp --dport [PUERTO] -j DNAT --to-destination X.Y.Z.W:[PUERTO]

# activamos el algoritmo MASQUERADE de iptables, que "disfraza" el enrutamiento de un server a otro
$ iptables -t nat -A POSTROUTING -j MASQUERADE
```

Por otra parte, podríamos redirigir el tráfico solo para un host concreto (primera opción) o para una subred completa (segundo comando):

```bash
# para un solo host
$ iptables -t nat -A PREROUTING -s 192.168.X.Y -p tcp --dport [PUERTO] -j DNAT --to-destination X.Y.Z.W:[PUERTO]

# para una subred
$ iptables -t nat -A PREROUTING -s 192.168.X.0/[MASK] -p tcp --dport [PUERTO] -j DNAT --to-destination X.Y.Z.W:[PUERTO]
```

[Referencia - Debuntu.org](https://www.debuntu.org/how-to-redirecting-network-traffic-to-a-new-ip-using-iptables/)

### 2. ***Enrutamiento del tráfico de un servidor a otro (Windows)***

En este caso, veremos dos opciones, aunque seguramente haya cientos de alternativas escondidas por los foros de Internet:

+ ***Command line***, con *route add*: [dreamincode.net](http://www.dreamincode.net/forums/topic/163502-redirect-network-traffic-over-a-specific-interface/)

En este caso, el procedimiento es tan simple como añadir una nueva entrada a la lista de enrutamiento de la máquina, que actuaría como "router" en cierto modo. El comando a ejecutar sería similar al siguiente, alterando los valores deseados:

```cmd
route -p add IP_DESTINO mask subnet INTERFAZ_DEFAULT_GATEWAY
```

+ ***Simple User Interface***, con *ReDirProxy*: [redirproxy.net](http://redirproxy.bplaced.net/)

En este caso, nos encontramos con una herramienta gráfica que podría servirnos para analizar el tráfico que está pasando por nuestra máquina, con filtrado de puertos, y realizar con éste un reenrutamiento si así lo quisiéramos. Basta con instalar el ejecutable en una máquina Windows y comenzar a probar sus opciones, aunque cabe destacar que su última versión tiene fecha de abril de 2012.

![ReDirProxy example](http://redirproxy.bplaced.net/css/redir1.jpg)
