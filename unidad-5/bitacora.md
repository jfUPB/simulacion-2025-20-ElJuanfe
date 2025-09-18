# Evidencias de la unidad 5

### Actividad 03

#### Es hora de una nueva creación. Diseña e implementa una obra de arte generativa algorítmica interactiva en tiempo real en p5.js que cumpla con los siguientes requisitos:

Documenta el proceso de creación, incluyendo la idea inicial, bocetos, experimentación con el código y el resultado final.

Es unidad incluye una novedad: DISEÑO. Debes intencionar tu obra. Esta vez te pediré que DISEÑES antes de generar código. Define un concepto, haz bocetos, define la interacción, etc. ¿Cuál es el concepto de tu obra? ¿Qué quieres comunicar con ella?

Quiero poder tener un volcán dibujado por un trapecio, del cual saldrán del centro de su lado superior partículas como si fuesen lava y otras que parezcan humo y piedras. El usuario puede controlar con la barra espaciadora la erupción del volcán, si la presiona, lava saldrá brotando del volcán, si no, las partículas no se generan.

Debes utilizar los conceptos de herencia y polimorfismo que revisaste en la fase de investigación.

Debes utilizar al menos un concepto de cada una de las unidades anteriores: 4 conceptos.

* Unidad 1: Usaré random para que las partículas tengan colores dentro de cierto rango, peso y salgan con una dirección aleatoria pero no muy descontrolada.
* Unidad 2: Tendré el marco 101 para manejar dichas partículas.
* Unidad 3: Quiero que las partículas tengan un peso determinado, aleatorio, el cual varía ligeramente, junto con su tamaño de forma proporcional.
* Unidad 4: Ondas sinusoides para las particulas que saldrán disparadas, como si fuesen fuego.

Debes definir cómo vas a gestionar el tiempo de vida de las partículas y la memoria.

La obra debe ser interactiva en tiempo real. Puedes usar teclado, mouse, música, el micrófono, video, sensor o cualquier otro dispositivo de entrada.

* Usaré la barra espaciadora para que el usuario pueda controlar cuándo el volcán lanzará las particulas hacia arriba, utilizando el viento que habíamos usado en una de las unidades anteriores

Incluye un enlace a tu código en el editor de p5.js.

[Volcán](https://editor.p5js.org/ElJuanfe/sketches/SemqgAh-K)

Incluye el código fuente.

``` js
// emitter.js
// Emitter that spawns Lava / Smoke / Stone particles.
// maneja array de partículas, límites de memoria, y run() que actualiza y limpia

class Emitter {
  constructor(x, y) {
    this.origin = createVector(x, y);
    this.particles = [];
    this.maxParticles = 1200; // límite de memoria
  }

  // Añade una sola partícula según probabilidades
  addParticle() {
    if (this.particles.length >= this.maxParticles) return;

    let r = random();
    if (r < 0.62) {
      // mayoría lava
      this.particles.push(new Lava(this.origin.x + random(-8, 8), this.origin.y + random(-6, 6)));
    } else if (r < 0.85) {
      // humo
      this.particles.push(new Smoke(this.origin.x + random(-12, 12), this.origin.y + random(-6, 6)));
    } else {
      // piedras
      this.particles.push(new Stone(this.origin.x + random(-10, 10), this.origin.y + random(-6, 6)));
    }
  }

  // Añadir un grupo (burst) para simular erupción más potente
  burst(n = 8) {
    for (let i = 0; i < n; i++) {
      this.addParticle();
    }
  }

  run(forces = []) {
    // recorre del final al inicio para splicing seguro (gestión memoria)
    for (let i = this.particles.length - 1; i >= 0; i--) {
      let p = this.particles[i];
      p.run(forces);
      if (p.isDead()) {
        this.particles.splice(i, 1);
      }
    }
  }

  // para debug / info
  count() {
    return this.particles.length;
  }
}

// lava.js
// Lava particle - hereda de Particle
// uso de ondas senoides para dar efecto de fuego (Unidad 4)
// colores en rango rojizo/naranja, tamaño proporcional al peso (Unidad 1 & 3)

class Lava extends Particle {
  constructor(x, y) {
    // initial upward velocity stronger and somewhat sinusoidally modulated
    let baseVx = random(-0.6, 0.6);
    let baseVy = random(-4.5, -2.5); // blast upward
    super(x, y, {
      velocity: createVector(baseVx, baseVy),
      weight: random(0.6, 2.2),
      lifespan: random(180, 260),
      ageRate: random(1.5, 3.0),
      color: Lava.randomColor()
    });

    // additional lava-specific props
    this.oscAmp = random(0.6, 2.2); // amplitude of sinusoid
    this.oscFreq = random(0.08, 0.18); // frequency
  }

  static randomColor() {
    // warm palette: yellow -> orange -> red
    let r = int(random(200, 255));
    let g = int(random(80, 200));
    let b = int(random(20, 80));
    return color(r, g, b, 255);
  }

  update() {
    // Unit 4: add sinusoidal modulation to the horizontal velocity to mimic flames
    let t = millis() * 0.001;
    let sinus = sin(t * this.oscFreq + this.phase) * this.oscAmp;
    // gently nudge the x velocity with the sinusoid (not too chaotic)
    this.velocity.x += sinus * 0.02;

    // also allow small random jitter
    this.velocity.x += random(-0.02, 0.02);

    // use Particle.update to integrate velocity/position and age
    super.update();
  }

  show() {
    // make lava glow: no stroke, radial-ish ellipse with alpha = lifespan
    noStroke();
    let alpha = constrain(this.lifespan, 0, 255);
    // draw two layered ellipses for a glowing look
    fill(red(this.color), green(this.color), blue(this.color), alpha * 0.9);
    ellipse(this.position.x, this.position.y, this.size * 1.4);

    fill(255, 200, 0, alpha * 0.6);
    ellipse(this.position.x, this.position.y, this.size * 0.8);
  }
}

// particle.js
// Base Particle class (The Nature of Code style)
// contiene propiedades comunes: posición, velocidad, aceleración, vida, peso, tamaño

class Particle {
  constructor(x, y, options = {}) {
    this.position = createVector(x, y);
    // velocidad inicial mínima; las subclases pueden modificar
    this.velocity = options.velocity || createVector(random(-0.5, 0.5), random(-2, -1));
    this.acceleration = createVector(0, 0);

    // weight influences how gravity affects the particle and its size
    this.weight = options.weight ?? random(0.5, 3.0); // Unidad 3: peso aleatorio
    this.size = options.size ?? map(this.weight, 0.5, 3.0, 6, 22); // tamaño proporcional al peso

    this.lifespan = options.lifespan ?? 255; // tiempo de vida (0..255)
    this.ageRate = options.ageRate ?? random(1.0, 3.0); // cuánto rápido se consume la vida

    // color settings (subclases pueden pasar colorRange)
    this.color = options.color || color(127, this.lifespan);

    // small phase offset for per-particle sinusoids
    this.phase = random(TWO_PI);
  }

  applyForce(force) {
    // heavier particles accelerate less (simple mass effect)
    let f = p5.Vector.div(force, this.weight);
    this.acceleration.add(f);
  }

  update() {
    // basic motion integration (frame 101)
    this.velocity.add(this.acceleration);
    this.position.add(this.velocity);
    // age the particle
    this.lifespan -= this.ageRate;
    // reset acceleration
    this.acceleration.mult(0);
  }

  // default display (can be overridden)
  show() {
    stroke(0, this.lifespan);
    strokeWeight(1);
    fill(this.color.levels[0], this.color.levels[1], this.color.levels[2], this.lifespan);
    ellipse(this.position.x, this.position.y, this.size);
  }

  isDead() {
    // también si cae fuera de la pantalla por abajo
    return this.lifespan <= 0 || this.position.y > height + 50;
  }

  // run: apply forces, update, show — subclases pueden override run() partially
  run(forces = []) {
    for (let f of forces) this.applyForce(f);
    this.update();
    this.show();
  }
}

// sketch.js
// Configuración general, viento variable con ruido, trapecio volcán,
// control por barra espaciadora (mientras la mantienes presionada emite partículas)

let emitter;
let volcanoTop; // punto central del lado superior del trapecio
let volcanoWidthTop = 120;
let volcanoWidthBottom = 260;
let volcanoHeight = 160;

let wind; // vector viento aplicado a partículas
let windNoiseOff = 0;
let globalForces = []; // fuerzas que aplicamos cada frame (ej. viento y gravedad)

function setup() {
  createCanvas(900, 600);
  colorMode(RGB);
  ellipseMode(CENTER);
  rectMode(CENTER);

  // posicionamos volcán en el centro horizontal y su base cerca de la parte baja
  let vx = width / 2;
  let vy = height * 0.65;
  volcanoTop = createVector(vx, vy - volcanoHeight / 2);

  // emitter justo en el centro del lado superior del trapecio
  emitter = new Emitter(volcanoTop.x, volcanoTop.y);

  wind = createVector(0, 0);

  // gravedad común; cada particula aplicará division por peso en applyForce()
  gravity = createVector(0, 0.12);

  // frame rate un poco alto para suavidad
  frameRate(60);
}

function draw() {
  background(30, 30, 38); // noche para que la lava resalte

  // actualizar viento con Perlin noise para que cambie suavemente
  windNoiseOff += 0.005;
  let wn = noise(windNoiseOff);
  let windStrength = map(wn, 0, 1, -0.08, 0.12); // viento horizontal leve
  wind.set(windStrength, 0);

  // fuerzas globales que pasaremos al sistema cada frame
  globalForces = [gravity, wind];

  // dibujar volcán (trapecio)
  drawVolcano();

  // Emisión controlada por barra espaciadora (mientras se mantenga presionada)
  if (keyIsDown(32)) { // 32 = space
    // mientras presionada: emite varias partículas cada frame
    // la cantidad puede depender del tiempo para dar picos sinusoidales
    let intensity = 6 + floor(map(sin(millis() * 0.004), -1, 1, -2, 10)); // variación con seno
    for (let i = 0; i < intensity; i++) {
      emitter.addParticle();
    }
    // además de emisiones regulares, cada cierto tiempo hacemos un burst mayor
    if (frameCount % 20 === 0) {
      emitter.burst(floor(random(3, 9)));
    }
  }

  // Ejecutamos el sistema (aplicando fuerzas)
  emitter.run(globalForces);

  // HUD pequeño: muestra cantidad de partículas y ayuda
  drawHUD();
}

function drawVolcano() {
  // trapecio con base abajo, top centrado en volcanoTop
  push();
  translate(volcanoTop.x, volcanoTop.y + volcanoHeight / 2);

  // coords del trapecio relativo a translate
  let topW = volcanoWidthTop;
  let bottomW = volcanoWidthBottom;
  let h = volcanoHeight;

  strokeWeight(0);
  // mountain fill
  fill(120, 80, 30);
  beginShape();
  vertex(-topW / 2, -h);
  vertex(topW / 2, -h);
  vertex(bottomW / 2, 0);
  vertex(-bottomW / 2, 0);
  endShape(CLOSE);

  pop();

  // actualizar emitter.origin según el trapecio top
  emitter.origin.x = volcanoTop.x;
  emitter.origin.y = volcanoTop.y - volcanoHeight + 10; // ligeramente dentro del cráter
}

function drawHUD() {
  noStroke();
  fill(255, 200);
  textSize(14);
  textAlign(LEFT, TOP);
  text("Mantén presionada la barra espaciadora para erupción", 12, 12);
  text("Partículas: " + emitter.count(), 12, 34);
}

function keyPressed() {
  // prevent scrolling page in browser when pressing space (useful en web editor)
  if (keyCode === 32) {
    return false;
  }
}

function keyReleased() {
  if (keyCode === 32) {
    return false;
  }
}

// smoke.js
class Smoke extends Particle {
  constructor(x, y) {
    // humo más ligero
    let m = random(0.8, 1.8);
    super(x, y, m);
    this.decay = random(0.6, 1.6); // se disipa lento
    this.velocity = createVector(random(-0.6, 0.6), random(-1.6, -0.2));
    this.size = random(8, 28);
    this.gray = random(40, 120);
    this.spread = random(0.2, 0.9);
  }

  update(wind) {
    // humo sube y se expande, es sensible al viento y a la turbulencia (ruido)
    let buoyancy = createVector(0, -0.02 * this.mass - random(0.001, 0.02));
    this.applyForce(buoyancy);
    if (wind) this.applyForce(p5.Vector.mult(wind, 0.35));
    // algo de ruido lateral para dar "remolino"
    let n = noise(this.position.x * 0.01, this.position.y * 0.01, frameCount * 0.003);
    this.applyForce(createVector(map(n, 0, 1, -0.2, 0.2) * this.spread, 0));
    // se agranda ligeramente con el tiempo
    this.size += 0.02 + this.mass * 0.01;
    super.update();
  }

  show() {
    noStroke();
    fill(lerpColor(color(200), color(80), random(0.0, 1.0)), this.lifespan * 0.7);
    // para asegurar tonos grises usamos rgba
    let alpha = map(this.lifespan, 0, 255, 0, 180);
    fill(120, 120, 120, alpha);
    ellipse(this.position.x, this.position.y, this.size);
  }
}

// stone.js
// Stone particle - hereda de Particle
// pedruscos que caen con más masa, menos afectados por viento, rotan y rebotan ligeramente

class Stone extends Particle {
  constructor(x, y) {
    super(x, y, {
      velocity: createVector(random(-1.2, 1.2), random(-5.0, -3.0)),
      weight: random(2.5, 6.0), // más pesados
      lifespan: random(260, 400),
      ageRate: random(1.0, 2.0),
      color: Stone.randomColor()
    });

    this.rotation = random(TWO_PI);
    this.rotSpeed = random(-0.08, 0.08);
    this.restitution = random(0.2, 0.5); // para rebotes con el suelo
  }

  static randomColor() {
    // tonos piedra: marrón/gris
    let v = int(random(80, 160));
    return color(v - 10, v - 6, v + 10, 255);
  }

  update() {
    // heavier: slight reduction of acceleration-to-velocity updates handled by Particle
    // rotation changes with velocity
    this.rotation += this.rotSpeed;
    super.update();

    // simple ground collision (bounce a bit and lose life)
    if (this.position.y >= height - 10) {
      // reflect vertical velocity and dampen
      this.position.y = height - 10;
      this.velocity.y *= -this.restitution;
      // reduce lifespan faster on impact
      this.lifespan -= 8 + abs(this.velocity.y);
      // friction on ground reduces horizontal movement
      this.velocity.x *= 0.6;
    }
  }

  show() {
    push();
    translate(this.position.x, this.position.y);
    rotate(this.rotation);
    stroke(30, this.lifespan);
    strokeWeight(1);
    fill(red(this.color), green(this.color), blue(this.color), this.lifespan);
    // draw as an irregular rectangle -> simulate rock
    rectMode(CENTER);
    rect(0, 0, this.size * 1.2, this.size * 0.9);
    pop();
  }
}
```

Captura de pantallas de tu obra con las imágenes que más te gusten

<img width="464" height="424" alt="image" src="https://github.com/user-attachments/assets/54a63d92-70e2-4984-b96a-2a3f51a4f185" />

