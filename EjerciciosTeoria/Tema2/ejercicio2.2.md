# Ejercicios Tema 2 - SWAP: Alta disponibilidad y escalabilidad

## Ejercicio T2.2 - Buscar frameworks y librerías para diferentes lenguajes que permitan hacer aplicaciones altamente disponibles con relativa facilidad.

Como ya hemos visto a lo largo de la asignatura, buscamos ofrecer la máxima disponibilidad en todos nuestros servicios y aplicaciones, por lo que siempre puede ser útil ayudarnos de herramientas externas que aseguren cierto mecanismo de seguridad de este tipo.

Veamos una lista de tres frameworks o librerías que nos pueden ayudar a desarrollar aplicaciones con una cierta estabilidad y disponibilidad:

#### 1. ***PM2 (Process Manager)*** - [github.com/Unitech/pm2](https://github.com/Unitech/pm2)

Se trata de un gestor de procesos y aplicaciones Node.JS y/o Python con un balanceador de carga dedicado. Permite mantener las aplicaciones ejecutándose en todo momento, realizar recargas del servicio sin _downtime_ y facilita las tareas de administración al encargado de esto.

Permite ejecución de aplicaciones aisladas, en modo clúster, monitorización de procesos, manejo de archivos *log*, manejo de estados de procesos y despliegue de servicios, entre otras características.

Por ejemplo, con el siguiente comando estaríamos lanzando 4 instancias de una aplicación en Python en modo clúster, con la consecuente creación de sus *daemons* necesarios:

```bash
# Start, daemonize and auto-restart application (Python)
$ pm2 start app.py -i 4
```

Y a continuación, vemos una muestra de lo que sería la monitorización de diversos servicios con `pm2`:

![pm2-ls](https://raw.githubusercontent.com/unitech/pm2/master/pres/pm2-list.png)


### 2. ***Microsoft Operations Framework Process Model*** - [technet.microsoft.com/en-us/dd320379.aspx](https://technet.microsoft.com/en-us/dd320379.aspx)

El MOF consiste en una guía que ayuda a las organizaciones a conseguir la máxima fiabilidad y disponibilidad de las tecnologías de Microsoft. Dirige a estas organizaciones para la gestión de complejos entornos distribuidos de TIC. Por supuesto, viene desarrollada por los de Redmond; y se ofrece en forma de documentos, guías de operaciones, herramientas de ayuda, plantillas, etc.

El *MOF process model* aporta una aproximación de *ciclo-de-vida* de una aplicación o servicio, que se ve reflejada en el siguiente gráfico obtenido de su web. En él se aprecian las actividades comprendidas en cada cuadrante. Cada una de estas tareas son las que Microsoft pretende apoyar en cada empresa, mediante dicho framework de operaciones.

![mof-process-model](https://i-msdn.sec.s-msft.com/dynimg/IC57264.jpeg)


### 3. ***Apache Mesos Framework*** - [mesos.apache.org/documentation/latest/high-availability-framework-guide/](http://mesos.apache.org/documentation/latest/high-availability-framework-guide/)

Se trata de un framework de aplicaciones de alta disponibilidad para el servidor web Apache; y consiste en un *daemon* maestro ejecutándose en cada uno de los nodos de nuestro clúster, y que controla cada una de las aplicaciones ejecutándose en dichos nodos.

Visto esto, podemos apreciar la siguiente captura obtenida en la web de Mesos, donde cada nodo se identifica como un *agente Mesos*:

![mesos-architecture](http://mesos.apache.org/assets/img/documentation/architecture3.jpg)

Por otro lado, tendríamos planificadores MPI (Interfaz de paso de mensajes) y [Hadoop](http://hadoop.apache.org/) (framework para diseño de aplicaciones escalables y de entorno distribuido). Y en cada agente, como es lógico, estaríamos ejecutando alguna tarea de cualquier tipo.

Además, vemos el módulo **ZooKeeper quorum**. Se trata de otro framework de Apache, en este caso para el desarrollo y mantenimiento de aplicaciones distribuidas de alta fiabilidad. En este caso, como vemos en el diagrama, sería el encargado de proporcionar una coordinación adecuada entre los nodos *maestro* del framework Mesos.
