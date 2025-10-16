# Evidencias de la unidad 7

### Actividad 01

Tu análisis de 3-4 ejemplos de Ji Lee, explicando cómo logran la conexión palabra-imagen.

<img width="500" height="500" alt="image" src="https://github.com/user-attachments/assets/c9b44adf-b98e-49e5-a161-11b0e6b54e9f" />

Se inspira fuertemente de la idea de la caricaturización de un tunel, además de que sabe juntarlo con la propia palabra, resultando en que las dos "n" juntas podrían parecer entrada y salida de dichos tuneles.

<img width="500" height="503" alt="image" src="https://github.com/user-attachments/assets/9cc2ed77-f1b0-43e7-ba0b-cae2e588898b" />

En cierta medida, los zippers se pueden ver como un montón de diente entrelazados, siendo así como sus dientes se pueden interpretar como la letra "E", de lo cual es facilmente identificable en el patrón dicha letra.

<img width="500" height="500" alt="image" src="https://github.com/user-attachments/assets/fd789899-d265-441c-b186-0ecd208e43d8" />

Debido que los constrastes se benefician de comunicar ideas con facilidad, aqui se usa para denotar una oscuridad en la cual se ven dos ojos muy claros, esto es algo que se puede lograr gracias a que los ojos en su minima expresion pueden ser pintados como simples punto, siendo posibles de reconocer cuando se tienen dos de ellos. Por añadido la palabra insomnia posee dos puntos en sus dos "i", siendo de gran ayuda.

Tus propias ideas (descripción o boceto simple) para representar visualmente 2-3 palabras distintas de forma estática.

Luna

<img width="464" height="206" alt="image" src="https://github.com/user-attachments/assets/8220e626-d1a6-44bb-863c-52636250fe65" />

Corte

<img width="333" height="143" alt="image" src="https://github.com/user-attachments/assets/91c13306-c9bc-4eed-bd9d-795b45cd67dc" />

Wave

<img width="515" height="155" alt="image" src="https://github.com/user-attachments/assets/b75b3a7f-3b35-44a4-b424-5f130c95c80b" />


### Actividad 02

Muestra el código de los dos (o más) experimentos básicos que replicaste integrando Matter.js y p5.js.

``` js
// === Imports abreviados de Matter.js ===
const { Engine, World, Bodies } = Matter;

// === Variables globales ===
let engine, world;
let boxes = [];
let ground;

// === Configuración inicial ===
function setup() {
  createCanvas(800, 500);
  rectMode(CENTER);
  noStroke();

  // Crear motor y mundo físico
  engine = Engine.create();
  world = engine.world;

  // Crear suelo (estático)
  const groundOptions = {
    isStatic: true,
    restitution: 0.5, // rebote leve
  };
  ground = Bodies.rectangle(width / 2, height - 10, width, 20, groundOptions);
  World.add(world, ground);

  // Crear algunos cuadrados iniciales
  for (let i = 0; i < 20; i++) {
    boxes.push(new Box(random(width), random(-200, 0), random(20, 60)));
  }
}

// === Clase para los cuadrados ===
class Box {
  constructor(x, y, size) {
    this.size = size;

    // Propiedades físicas aleatorias
    const options = {
      restitution: random(0.3, 0.8), // rebote
      friction: random(0.1, 0.4),
      density: 0.001 * size, // más grandes = más pesados
    };

    // Crear cuerpo físico
    this.body = Bodies.rectangle(x, y, size, size, options);
    World.add(world, this.body);

    // Color aleatorio
    this.color = color(random(200, 255), random(100, 200), random(50, 150));
  }

  // Mostrar en el canvas
  show() {
    const pos = this.body.position;
    const angle = this.body.angle;

    push();
    translate(pos.x, pos.y);
    rotate(angle);
    fill(this.color);
    rect(0, 0, this.size, this.size);
    pop();
  }

  // Eliminar si sale de pantalla (limpieza)
  isOffScreen() {
    return this.body.position.y > height + 100;
  }

  removeFromWorld() {
    World.remove(world, this.body);
  }
}

// === Loop principal ===
function draw() {
  background(15, 15, 30);

  Engine.update(engine);

  // Dibujar suelo
  fill(30, 30, 60);
  rect(ground.position.x, ground.position.y, width, 20);

  // Dibujar y actualizar todas las cajas
  for (let i = boxes.length - 1; i >= 0; i--) {
    boxes[i].show();

    // Eliminar cajas que caen fuera de pantalla
    if (boxes[i].isOffScreen()) {
      boxes[i].removeFromWorld();
      boxes.splice(i, 1);
    }
  }

  // Añadir nuevas cajas cada cierto tiempo
  if (frameCount % 20 === 0) {
    boxes.push(new Box(random(width), -50, random(20, 60)));
  }
}
```

``` js
// === Alias Matter ===
const { Engine, World, Bodies, Constraint, Mouse, MouseConstraint, Body } = Matter;

// === Globals mínimos ===
let engine, world;
let chains = [];
const NUM_CHAINS = 3;
const COLORS = ["#F25C05", "#F3A712", "#F5E9BE", "#04395E", "#005377"];

function setup() {
  createCanvas(900, 600);
  noStroke();
  rectMode(CENTER);
  ellipseMode(RADIUS);

  // Motor y mundo
  engine = Engine.create();
  world = engine.world;
  engine.world.gravity.y = 1;

  // Crear varias cadenas (péñdulos compuestos)
  for (let i = 0; i < NUM_CHAINS; i++) {
    const x = 150 + i * 260;      // separación horizontal
    const y = 120;                // anclaje vertical
    const segments = floor(random(6, 10));
    const segLen = random(26, 36);
    // Pasamos el índice i para usar un collisionGroup único por cadena
    chains.push(new PendulumChain(x, y, segments, segLen, i));
  }

  // Agregar interacción con mouse (arrastrar cuerpos)
  const canvasMouse = Mouse.create(canvas.elt);
  canvasMouse.pixelRatio = pixelDensity();

  const mcOpts = {
    mouse: canvasMouse,
    constraint: {
      stiffness: 0.2,
      render: { visible: false }
    }
  };
  const mConstraint = MouseConstraint.create(engine, mcOpts);
  World.add(world, mConstraint);
}

function draw() {
  background(15, 18, 30);
  Engine.update(engine, 1000 / 60);

  // Dibujar cada cadena
  for (const ch of chains) ch.show();
}

/* ===========================
   Clase PendulumChain (antes 'Chain')
   - Construye segmentos circulares y constraints
   - Usa collisionFilter.group negativo único por cadena para evitar colisiones internas
   =========================== */
class PendulumChain {
  constructor(anchorX, anchorY, numSegments, segmentLength, chainIndex) {
    this.links = [];
    this.constraints = [];

    // Grupo de colisión negativo y único para esta cadena:
    // cuerpos con el mismo group negativo NO colisionan entre sí.
    // (Usamos - (chainIndex + 1) para asegurarnos de valores no-cero y únicos).
    this.collisionGroup = - (chainIndex + 1);

    let prevBody = null;

    for (let i = 0; i < numSegments; i++) {
      const r = random(12, 24);
      const col = random(COLORS);
      const px = anchorX;
      const py = anchorY + i * segmentLength;

      const seg = new CircleSegment(px, py, r, col, this.collisionGroup);
      this.links.push(seg);

      if (prevBody) {
        // Constraint entre prevBody y este segmento
        const c = Constraint.create({
          bodyA: prevBody,
          bodyB: seg.body,
          length: segmentLength,
          stiffness: 0.85,     // ajustar para menos "penetración"
          damping: 0.02
        });
        World.add(world, c);
        this.constraints.push(c);
      }

      prevBody = seg.body;
    }

    // Anclar el primer segmento a un punto fijo (techo)
    const topConstraint = Constraint.create({
      pointA: { x: anchorX, y: anchorY - 18 },
      bodyB: this.links[0].body,
      length: 18,
      stiffness: 1
    });
    World.add(world, topConstraint);
    this.constraints.unshift(topConstraint);
  }

  show() {
    for (const c of this.constraints) {
      // Puede ser constraint con pointA o con bodyA
      const pa = c.pointA ? c.pointA : (c.bodyA ? c.bodyA.position : null);
      const pb = c.pointB ? c.pointB : (c.bodyB ? c.bodyB.position : null);
      if (pa && pb) line(pa.x, pa.y, pb.x, pb.y);
    }
    noStroke();

    // Dibujar segmentos
    for (const s of this.links) s.show();
  }
}

/* ===========================
   Clase CircleSegment
   - Crea el círculo físico y aplica collisionFilter.group
   =========================== */
class CircleSegment {
  constructor(x, y, r, col, collisionGroup) {
    // Opciones físicas
    const options = {
      restitution: random(0.2, 0.6),
      friction: random(0.01, 0.12),
      density: 0.001 * (r / 18),
      collisionFilter: {
        // Grupo negativo único por cadena evita colisiones internas.
        group: collisionGroup
      }
    };

    this.r = r;
    this.body = Bodies.circle(x, y, r, options);
    this.color = color(col);
    World.add(world, this.body);
  }

  show() {
    const pos = this.body.position;
    const angle = this.body.angle;

    push();
    translate(pos.x, pos.y);
    rotate(angle);
    fill(this.color);
    ellipse(0, 0, this.r, this.r);
    pop();
  }
}
```

Incluye una **captura de pantalla o ENLACE a un GIF (no olvides, enlace) de cada experimento funcionando.

https://github.com/user-attachments/assets/a4b4fc40-c5e3-4c13-ae8d-f206a7b778ed

[Experimento 1](https://editor.p5js.org/ElJuanfe/sketches/MDIAe0BzC)

https://github.com/user-attachments/assets/6c3f69b2-735f-4f54-ab62-b4c11a50d0bc

[Experimento 2](https://editor.p5js.org/ElJuanfe/sketches/34JyxdKJA)

Proporciona tu explicación clara y concisa de los conceptos clave (Engine, World, Bodies, Constraint, MouseConstraint).

* Engine: motor principal, calcula las fuerzas, colisiones y movimientos de todos los cuerpos.
* World: contenedor que guarda todos los cuerpos.
* Bodies: colección de formas físicas básicas que puedes crear.
* Constraint: es una conexión entre dos puntos, pudiendo ser cuerpos.
* MouseConstraint: facilita interactuar con los cuerpos usando el mouse.

Menciona brevemente cualquier dificultad encontrada al configurar o usar Matter.js inicialmente.

Inicialmente me salté la parte del vídeo donde explican añadir en el index esto: 
``` js
<script src="https://cdnjs.cloudflare.com/ajax/libs/matter-js/0.19.0/matter.min.js"></script>
```
pero posteriormente lo ví despues de intentar multiples veces fallidas ajacutar mi códdigo.

### Actividad 03

Indica claramente la palabra elegida.

Golpe

Explica tu idea conceptual: ¿Cómo la animación física representa el significado de la palabra?

Simplemente muestra una colisión con dicha palabra, la cual pareciera cuando golpean con una bola de demolición a una pared y salen volando escombros.

Describe brevemente los aspectos técnicos clave de tu implementación: ¿Cómo formaste las letras con Matter.js? ¿Qué propiedades físicas fueron importantes? ¿Usaste restricciones?

La palabra se formó mediante una imagen png, la cual toma los valores no transparentes de los pixeles y los convierte a cuadros de colores individuales.

* isStatic mantiene los cuerpos inmóviles al inicio.
* restitution controla qué tanto rebotan los cuerpos al colisionar.
* frictionAir es resistencia del aire. con esta fricción evitamos que los cuadros vuelen descontroladamente.
* friction adicionalemnte da fricción entre los cuerpos
* applyForce sirve para que empujemos los bloques con el cursor

No usé constraints.

Incluye el código completo de tu sketch final.

``` js
// Requiere matter.js
// <script src="https://cdnjs.cloudflare.com/ajax/libs/matter-js/0.19.0/matter.min.js"></script>

let Engine = Matter.Engine,
    World = Matter.World,
    Bodies = Matter.Bodies,
    Body = Matter.Body;

let engine, world;
let boxes = [];
let img;

function preload() {
  // 🔹 Carga tu imagen — puede ser una palabra o figura
  img = loadImage('golpe.png');
}

function setup() {
  createCanvas(800, 400);
  pixelDensity(1);

  engine = Engine.create();
  world = engine.world;

  // Sin gravedad, estilo "espacio"
  engine.world.gravity.y = 0;
  engine.world.gravity.x = 0;

  createBoxesFromImage(img);
}

function createBoxesFromImage(img) {
  img.resize(600, 0); // mantiene un tamaño razonable
  img.loadPixels();

  let step = 4; // mayor resolución

  for (let y = 0; y < img.height; y += step) {
    for (let x = 0; x < img.width; x += step) {
      let index = (x + y * img.width) * 4;
      let r = img.pixels[index + 0];
      let g = img.pixels[index + 1];
      let b = img.pixels[index + 2];
      let a = img.pixels[index + 3];

      // Solo crea cuerpos si el pixel es visible
      if (a > 100) {
        let bx = x + width / 2 - img.width / 2;
        let by = y + height / 2 - img.height / 2;
        let bColor = color(r, g, b);

        // 🔹 Los bloques comienzan estáticos (sin moverse)
        let body = Bodies.rectangle(bx, by, step, step, {
          isStatic: true,      // inicialmente fijos
          restitution: 0.001,  // casi sin rebote
          frictionAir: 0.2,
          friction: 0.8,
        });

        body.customColor = bColor;
        boxes.push(body);
        World.add(world, body);
      }
    }
  }
}

function draw() {
  background(10, 15, 25);
  Engine.update(engine);

  noStroke();
  for (let b of boxes) {
    push();
    translate(b.position.x, b.position.y);
    rotate(b.angle);
    fill(b.customColor);
    rectMode(CENTER);
    rect(0, 0, 4, 4, 1);
    pop();
  }

  if (mouseIsPressed) {
    applyGentleForce(mouseX, mouseY, 100, 0.001);
  }

  gentleDrift();

  fill(150);
  textSize(14);
  textAlign(LEFT, BOTTOM);
  text("Haz clic o mueve el mouse para romper la imagen", 10, height - 10);
}

function applyGentleForce(x, y, radius, strength) {
  for (let b of boxes) {
    let dx = b.position.x - x;
    let dy = b.position.y - y;
    let distSq = dx * dx + dy * dy;
    if (distSq < radius * radius) {
      let force = createVector(dx, dy).normalize().mult(strength);
      Body.applyForce(b, b.position, { x: force.x, y: force.y });
    }
  }
}

function gentleDrift() {
  for (let b of boxes) {
    if (random() < 0.02) {
      let fx = (random() - 0.5) * 0.0001;
      let fy = (random() - 0.5) * 0.0001;
      Body.applyForce(b, b.position, { x: fx, y: fy });
    }
  }
}

// 🔹 Cuando haces clic, todos los bloques se "liberan"
function mousePressed() {
  for (let b of boxes) {
    Body.setStatic(b, false);
  }
}

// 🔹 También se libera si arrastras el mouse
function mouseDragged() {
  for (let b of boxes) {
    Body.setStatic(b, false);
  }
}
```

[Link en p5.js](https://editor.p5js.org/ElJuanfe/sketches/DTKreUYd7)

Inserta una captura de pantalla estática Y un enlace a un GIF animado (¡Esencial!) que muestre tu tipografía semántica animada en acción.

<img width="794" height="392" alt="image" src="https://github.com/user-attachments/assets/9cba0ee3-1fbb-46f2-ad7b-524c448983fb" />

![ezgif-7d363fdf9d3fed](https://github.com/user-attachments/assets/0cf49837-bb12-4ae6-bb96-3b148988cba5)

### Autoevaluación

Tu nota propuesta.

5.0

La defensa de esa nota para cada actividad.

1. 5.0
2. 5.0
3. 5.0
