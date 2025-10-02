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

Elige un tema musical que te inspire.

Para este caso, usaré: [Supermoon de No Man's Sky](https://music.youtube.com/watch?v=PZAM-_5h4QE&list=PLIlit4SMGXA6iiFgkaseAUo-DvXcNVv5c)

Documenta todo el proceso de diseño y creación en tu bitácora, incluyendo bocetos y decisiones de diseño.

<img width="583" height="610" alt="image" src="https://github.com/user-attachments/assets/06c7e4a5-56d1-494d-bc1f-0931c04f6865" />

<img width="641" height="521" alt="image" src="https://github.com/user-attachments/assets/22fd914c-a492-46ca-b7c5-6127d7cca6c0" />

<img width="758" height="766" alt="image" src="https://github.com/user-attachments/assets/36acdcd4-efb4-4466-abc4-935f34b46659" />


El código fuente completo de tu sketch en p5.js.

´´´ js
// Supermoon NMS - p5.js
// Naves orbitando estación Atlas con color reactivo a la música, vibración y pulsación

let flock = [];
let atlas;
let song, fft, amp;

function preload() {
  song = loadSound("supermoon.mp3"); // Reemplaza con tu audio
}

function setup() {
  createCanvas(800, 800);
  angleMode(DEGREES);

  atlas = new Atlas(width / 2, height / 2, 150);

  // Crear naves en formación de anillo
  let naves = 40;
  for (let i = 0; i < naves; i++) {
    let angle = map(i, 0, naves, 0, 360);
    let x = width / 2 + cos(angle) * 250;
    let y = height / 2 + sin(angle) * 250;
    flock.push(new Boid(x, y));
  }

  fft = new p5.FFT();
  amp = new p5.Amplitude();

  song.loop();
}

function draw() {
  background(10, 10);

  // Música
  let spectrum = fft.analyze();
  let bass = fft.getEnergy("bass");
  let level = amp.getLevel();

  // Atlas con color, vibración y pulsación
  atlas.display(level);

  // Cursor controla radio de órbita
  let d = dist(mouseX, mouseY, atlas.pos.x, atlas.pos.y);
  let orbitStrength = map(d, 0, width / 2, 4, 0.5);

  // Dibujar naves
  for (let boid of flock) {
    boid.orbit(atlas.pos, orbitStrength);

    if (bass > 180) {
      boid.turnImpulse();
    }

    boid.update();
    boid.render(level);
  }
}

/* =======================
   CLASE ATLAS
   ======================= */
class Atlas {
  constructor(x, y, s) {
    this.pos = createVector(x, y);
    this.s = s;
    this.pulseSize = 0;
  }

  display(level) {
    noStroke();
    let c = color(255, 50 + level * 400, 50 + level * 300);
    fill(c);

    // vibración en tamaño
    let vib = map(level, 0, 1, -20, 20);

    beginShape();
    vertex(this.pos.x, this.pos.y - (this.s / 2 + vib));
    vertex(this.pos.x + (this.s / 2 + vib), this.pos.y);
    vertex(this.pos.x, this.pos.y + (this.s / 2 + vib));
    vertex(this.pos.x - (this.s / 2 + vib), this.pos.y);
    endShape(CLOSE);

    // Pulsación expansiva (onda)
    this.pulseSize += 5;
    if (this.pulseSize > width) this.pulseSize = 0;

    noFill();
    stroke(150, 100 + level * 255, 255, 100);
    strokeWeight(2);
    ellipse(this.pos.x, this.pos.y, this.pulseSize + level * 200);
  }
}

/* =======================
   CLASE BOID
   ======================= */
class Boid {
  constructor(x, y) {
    this.pos = createVector(x, y);
    this.vel = p5.Vector.random2D().setMag(2);
    this.acc = createVector(0, 0);
    this.r = random(8, 20);
    this.col = color(random(255), random(255), random(255));
    this.prevHeading = this.vel.heading();
    this.pulse = random(1000); // desfase de la pulsación de cada nave
  }

  applyForce(force) {
    this.acc.add(force);
  }

  orbit(target, strength) {
    let dir = p5.Vector.sub(target, this.pos);
    let distFromCenter = dir.mag();
    dir.normalize();

    let perpendicular;
    if (dist(mouseX, mouseY, target.x, target.y) < atlas.s / 2) {
      perpendicular = createVector(dir.y, -dir.x);
    } else {
      perpendicular = createVector(-dir.y, dir.x);
    }
    perpendicular.mult(strength);
    this.applyForce(perpendicular);

    let desiredRadius = 250;
    let radialError = distFromCenter - desiredRadius;
    let radialForce = p5.Vector.sub(target, this.pos).setMag(radialError * 0.02);
    this.applyForce(radialForce);
  }

  turnImpulse() {
    let impulse = p5.Vector.random2D().mult(0.5);
    this.applyForce(impulse);
  }

  update() {
    this.vel.add(this.acc);
    this.vel.limit(4);
    if (this.vel.mag() > 0.05) {
      this.prevHeading = this.vel.heading();
    }
    this.pos.add(this.vel);
    this.acc.mult(0);
  }

  render(level) {
    push();
    translate(this.pos.x, this.pos.y);

    let theta = (this.vel.mag() > 0.05) ? this.vel.heading() : this.prevHeading;
    rotate(degrees(theta));

    // pulsación de las naves
    let pulseScale = map(sin(frameCount * 0.1 + this.pulse), -1, 1, 0.8, 1.2);

    noStroke();
    fill(this.col);
    beginShape();
    vertex(this.r * 2 * pulseScale, 0);
    vertex(-this.r * pulseScale, this.r / 1.5 * pulseScale);
    vertex(-this.r * pulseScale, -this.r / 1.5 * pulseScale);
    endShape(CLOSE);
    pop();
  }
}

´´´

Un enlace a tu sketch en el editor de p5.js.

https://editor.p5js.org/ElJuanfe/sketches/-LCuHUXUw

Capturas de pantalla mostrando tu pieza en acción.

<img width="739" height="754" alt="image" src="https://github.com/user-attachments/assets/9f8b0b71-32be-4e47-9789-5508f9d404e6" />

https://github.com/user-attachments/assets/bfd44b88-25bb-4966-87ac-054779177304

Autoevaluación

Tu nota propuesta: 4.8

La defensa de esa nota para cada actividad.

1. 5.0
2. 5.0
3. 5.0
4. 5.0
5. 4.0 pude hacer unas cosas mejor
