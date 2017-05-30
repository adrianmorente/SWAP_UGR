# Ejercicios Tema 2 - SWAP: Alta disponibilidad y escalabilidad

## Ejercicio T2.1 - Calcular la disponibilidad del sistema si tenemos dos réplicas de cada elemento (en total 3 elementos en cada subsistema).

Calcular la disponibilidad de un sistema no es tarea fácil, ya que éste consta de diferentes componentes, que a su vez pueden o no estar replicados según el caso que nos ataña. Dado esto, hemos de ponernos siempre en el peor caso para este cálculo, que comprendería el hecho de que los componentes fuesen fallando de forma sucesiva uno tras otro.

Para estudiar este porcentaje con 1 única unidad de cada componente usamos la fórmula vista en teoría:

> As = Ac1 \* Ac2 \* Ac3 \* ... \* Acn

De esta forma, en este ejercicio, la disponibilidad inicial equivale a:

> As = 85% \* 90% \* 99'9% \* 98% \* 85% \* 99% \* 99'99% \* 95% = ***59'86%***

___

Sin embargo, para componentes replicados, el valor de disponibilidad aproximada de éste equivale a la siguiente fórmula (también vista en teoría):

> As = Ac1 + ( (1 - Ac1) * Ac2 )

De esta forma, la disponibilidad aproximada para el servicio de Web si redundamos **una** vez este componente, sería:

> As = 85% \+ ( (1 - 85%) \* 85% ) = ***97'75%***

___

Visto esto, podemos aplicar esta última fórmula para un valor **3** de replicado de cada uno de los componentes, para lo que obtendríamos los siguientes valores:

| COMPONENT   | AVAILABILITY (1) | AVAILABILITY (2) | AVAILABILITY (3) |
|-------------|------------------|------------------|------------------|
| Web         | 85%              | 97'75%           | 99'6625%         |
| App         | 90%              | 99%              | 99'9%            |
| Database    | 99'9%            | 99'9999%         | 99'999999%       |
| DNS         | 98%              | 99'96%           | 99'9992%         |
| Firewall    | 85%              | 97'75%           | 99'6625%         |
| Switch      | 99%              | 99'99%           | 99'9999%         |
| Data Center | 99'99%           | 99'99%           | 99'999999%       |
| ISP         | 95%              | 99'75%           | 99'9875%         |
| ***TOTAL*** | ***59'86%***     | ***94'3%***      | ***99'2135%***   |

Para finalizar, vemos que la disponibilidad estimada equivale a **99'2135%**.
