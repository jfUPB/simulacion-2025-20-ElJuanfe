# Unidad 2

## 🛠 Fase: Apply

### Actividad 08

#### Describe el concepto de tu obra generativa.

En resumen, quiero construir algo que se viese ordenado pero a la vez que presentase un flujo naturalmente aleatorio, por lo cual tomaré "ondas" que se muevan en patrones aleatorios y coloridos, para representar en cierta forma los sonidos, los cuales tienen propiedades (al igual que cualquier onda) como la amplitud y longitud, las cuales son bastante interesantes, adicionalmete se dice que cierto sonidos presenta "color", por lo cual quiero incorporarlo literalmente. Generaré varias ondas por toda la pantalla que se muevan con dicha aleatoriedad y también el usuario puede invertir sus velocidades en X con la barra espaciadora.

#### ¿Cómo piensas aplicar el marco MOTION 101 y por qué?

Motion 101 es en esencia dos fórmulas:

* v = v + a
* x = x + v

Por lo cual las implementaré posición que cambia con la velocidad, velocidad que se altera aceleración.

#### ¿Qué algoritmo de aceleración vas a utilizar? ¿Por qué?

Haré uno basado en aceleraciones aleatorias, pero solo en el eje X, que si bien no varía mucho de los originales, siento que es suficientemente útil para el caso.

#### El contenido generado debe ser interactivo. Puedes utilizar mouse, teclado, cámara, micrófono, etc, para variar los parámetros del algoritmo en tiempo real.
#### El código de la aplicación.

``` js
const count = 100;
let bubbles = [];
let bg = '#140a4d';

function setup() {
  createCanvas(1920, 1080);
  background(bg);
  colorMode(RGB);
  for (let i = 0; i < count; i++) {
    bubbles.push(new Bubble(i));
  }
}

function draw() {
  fill(bg + '10');
  noStroke();
  rect(0, 0, width, height);

  for (let b of bubbles) {
    b.update();
    b.display();
    b.checkEdges();
  }
}

class Bubble {
  constructor(idx) {
    this.idx = idx;
    this.position = createVector(random(width), random(height));
    this.velocity = createVector(random(1, 3), 0);
    this.acceleration = createVector(0, 0);
    this.topSpeed = random(2, 5);

    this.phase = random(TWO_PI);
    this.amplitude = random(20, 100);
    this.frequency = random(0.005, 0.02);
    this.speedPhase = random(0.01, 0.03);

    this.color = color(random(50, 255), random(50, 255), random(50, 255));
    this.size = random(8, 16);
  }

  update() {
    let accChange = random(-0.1, 0.1);
    this.acceleration.x += accChange;
    this.acceleration.x = constrain(this.acceleration.x, -0.05, 0.05);


    this.velocity.add(this.acceleration);
    this.velocity.x = constrain(this.velocity.x, -this.topSpeed, this.topSpeed);
    this.position.add(this.velocity);

    this.phase += this.speedPhase;
    
    this.amplitude += sin(this.phase * 0.5) * 0.005;
    this.frequency += sin(this.phase * 0.3) * 0.01;

    this.amplitude = constrain(this.amplitude, 10, 120);
    this.frequency = constrain(this.frequency, 0.0003, 0.002);
  }

  display() {
    let yOffset = sin(this.phase + this.position.x * this.frequency * TWO_PI) * this.amplitude;
    let x = this.position.x % width;
    let y = (this.position.y + yOffset + height) % height;

    noStroke();
    fill(this.color);
    ellipse(x, y, this.size);
  }

  checkEdges() {
    if (this.position.x > width) 
      this.position.x = 0;
    else if (this.position.x < 0) 
      this.position.x = width;

    if (this.position.y > height) 
      this.position.y = 0;
    else if (this.position.y < 0) 
      this.position.y = height;
  }
}

function keyPressed() {
  if (key === ' ') {
    for (let b of bubbles) {
      b.velocity.x *= -1;
    }
  }
}
```

#### Un enlace al proyecto en el editor de p5.js.

[Enlace al editor](https://editor.p5js.org/ElJuanfe/sketches/T6GO7NouK)

[Ondas aleatorias](https://editor.p5js.org/ElJuanfe/full/T6GO7NouK)

#### Una captura de pantalla representativa de tu pieza de arte generativo.

<img width="1919" height="817" alt="imagen" src="https://github.com/user-attachments/assets/7b876476-cfa3-4687-ad3d-0deb9573ff3d" />
