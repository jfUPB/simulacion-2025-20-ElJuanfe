# Evidencias de la unidad 6

### Actividad 01

Captura en tu bitácora dos imágenes de Tyler Hobbs que te llamen la atención y explica por qué.

1.
<img width="1268" height="715" alt="image" src="https://github.com/user-attachments/assets/c019f0a5-9cb0-4905-9303-0ed6b0c30dbc" />

Esta me gusta porque se siente como si las líneas que hay fuesen arena rastrillada, dando en cierta medida una calma, la cual se balancea con la incertidumbre que da el negro.

2.
<img width="1087" height="534" alt="image" src="https://github.com/user-attachments/assets/1cdf09ff-2fc5-4c20-8ced-79d3db6e72e6" />

En cierta forma, esta pieza se siente bastante "pop", por lo cual da sentimientos alegres y fluidos, además de que las franjas representan en cierta medida creatividad y rigidez por sus ondulaciones y sus puntas cuadradas.

3.
<img width="763" height="780" alt="image" src="https://github.com/user-attachments/assets/e3ed313f-0406-4ab8-8c29-655d13af0842" />

Esta obra pareciera tener una luz en la parte superior y da la impresión de ser un efecto que se usaría para estilizar comics y mangas, la cual casi se siente como fuego.

¿Qué te inspira de su trabajo?

Que entre tanto caos se puede ver orden, dicho orden se siente agradable y demasiado expresivo. Se rompe el orden con cierta frecuencia para que no haya monotonía.

### Actividad 02

¿Qué es una fuerza de dirección (steering force)?

Es una fuerza que empuja hacia un comportamiento deseado, dando un vector resultante que guía.

¿Qué diferencia tiene este tipo de fuerza con las que ya hemos estudiado en el contexto de la simulación de agentes?

Si comparamos fuerzas modeladas (como el peso) con la steering force, podemos llegar a que las fuerzas modeladas pueden ser más naturales y no tienen un resultado puntal deseado, mientras que la steering es quien manipula para conseguir resultados deseados, manipulando cosas como el movimiento.

¿Qué relación tiene la steering force con Craig Reynolds y su trabajo en simulación de comportamiento animal?

Craig presentó los boids (bird-oid objects) que contaban con separación, alineamiento y cohesión, que producían flocking. Así siendo de cierta forma como se nacen las steering forces en cierta medida.

### Actividad 03

Explica brevemente la estructura de datos usada para el campo de flujo y cómo se generan sus vectores.

El campo de flujo es una matriz bidimensional de vectores. Cada celda contiene un vector que representa la dirección que deben seguir los agentes en esa región del espacio. Estos vectores se generan usando perlin para obtener ángulos suaves.

Describe con tus palabras cómo un agente utiliza el campo para calcular su fuerza de dirección.

El campo es como un mapa lleno de flechitas invisibles. Cada flecha dice hacia dónde moverse.

Lista los parámetros clave identificados (resolución, maxspeed, maxforce).

* resolution: tamaño de cada celda de la grilla.

* maxspeed: velocidad máxima del agente.

* maxforce: fuerza máxima de dirección.

Describe la modificación que realizaste al código y explica detalladamente el efecto que tuvo en el movimiento y comportamiento colectivo de los agentes. Incluye una captura de pantalla o GIF si ilustra bien el cambio. Muestra el fragmento de código modificado.

https://github.com/user-attachments/assets/6e4138f9-203b-4f45-9767-81dd8305112f

Basicamente, alteré xoff += 0.5 en flowfield, también en sketch cambié en setup esto:

new Vehicle(random(width), random(height), random(0, 10), random(0, 10))
)

donde basicamente permití valores diferentes en dos de los randoms.

### Actividad 04

Explica con tus palabras el objetivo y la lógica general de cálculo de cada una de las tres reglas de Flocking (Separación, Alineación, Cohesión).

* Separación

Evita el amontonamiento. cada boid mira a su alrededor en un radio pequeño. Si detecta vecinos muy cerca, calcula un vector que lo empuje en dirección contraria a ellos.

* Alineación

Mueve en la misma dirección a vecinos. Observa la velocidad de los vecinos y ajusta la suya para parecerse.

* Cohesión

Mantiene unido al grupo. Calcula el centro y genera un vector débil hacia ahí.

Lista los parámetros clave identificados (radio de percepción, pesos de las reglas, maxspeed, maxforce).

* Radio de percepción:

desiredSeparation = 25, usado en separación.

neighborDistance = 50, usado en alineación y cohesión.

* Pesos de las reglas: esto sirve para determinar la importancia de las reglas

sep.mult(1.5), separación tiene mayor peso, siendo predominante.

ali.mult(1.0), peso de alineación.

coh.mult(1.0), peso de cohesión.

* maxspeed = 3: velocidad máxima del agente.

* maxforce = 0.05: fuerza máxima de dirección.

Describe la modificación que realizaste al código y explica detalladamente el efecto que tuvo en el comportamiento colectivo del enjambre (¿Se dispersan? ¿Forman grupos compactos? ¿se mueven caóticamente?). Incluye una captura de pantalla o GIF si ilustra bien el cambio. Muestra el fragmento de código modificado.

https://github.com/user-attachments/assets/74491b7d-5673-4e29-85ee-59db5a4d929c

Básicamente di prioridades así:

sep.mult(1.0)
ali.mult(2.0)
coh.mult(3.0)

y también cambié radios:

desiredSeparation = 100

neighborDistance = 1000

tendían a veriar poco sus direcciones y formaban grupos estrechos.

### Actividad 05

