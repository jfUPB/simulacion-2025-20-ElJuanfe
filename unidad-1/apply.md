# Unidad 1

## 🛠 Fase: Apply

### Actividad 08, Reporta en tu bitácora lo siguiente:

#### Un texto donde expliques el concepto de obra generativa.

En un inicio, quería partir de hacer un walker que se comportanse como un animal desplazandose, pero con naturalidad, careciendo de recorridos casi completamente rectos. Posteriormente con eso resuelto, quise darle para su parte interactiva algo que el usuario reconociese como un cambio con facilidad, por lo cual integré una variación de color jutno con una de tamaño, todo con una sola tecla, así que si el usuario preciona la barra espaciadora puede hacer al walker cambiar constantemente. 

#### Copia el código en tu bitácora.

``` js
let walker;
let xoff = 0, yoff = 1000;
let ancho = 1280, alto = 720;

function setup() {
  createCanvas(ancho, alto);
  walker = new Walker();
  background(0);

  noFill();
  stroke(255, 0, 0);
  strokeWeight(1);
  rect(ancho * 0.1, alto * 0.1, ancho * 0.8, alto * 0.8);
}

function draw() {
  walker.step();
  walker.show();
}

function keyPressed() {
  if (key === ' ') {
    let nuevoTamaño = randomGaussian(20, 5);
    // Color Gaussian natural con media 127 y sd 60
    let r = constrain(randomGaussian(120, 5), 0, 255);
    let g = constrain(randomGaussian(120, 5), 0, 255);
    let b = constrain(randomGaussian(120, 100), 0, 255);
    walker.setAppearance(max(nuevoTamaño, 5), color(r, g, b));
  }
}

class Walker {
  constructor() {
    this.x = width / 2;
    this.y = height / 2;
    this.prevX = this.x;
    this.prevY = this.y;
    this.diameter = 20;
    this.col = color(255);
  }

  setAppearance(dia, col) {
    this.diameter = dia;
    this.col = col;
  }

  show() {
    noStroke();
    fill(this.col);
    ellipse(this.x, this.y, this.diameter);
  }

  step() {
    this.prevX = this.x;
    this.prevY = this.y;

    let maxStep = 4;
    let stepX = map(noise(xoff), 0, 1, -maxStep, maxStep);
    let stepY = map(noise(yoff), 0, 1, -maxStep, maxStep);

    this.x = constrain(this.x + stepX, width*0.1, width*0.9);
    this.y = constrain(this.y + stepY, height*0.1, height*0.9);

    xoff += 0.01;
    yoff += 0.01;
  }
}
```

#### Coloca en enlace a tu sketch en p5.js en tu bitácora.

[Walker con Perlin, Rastro de Colores Gaussianos](https://editor.p5js.org/ElJuanfe/full/MRMrBVKLS)

#### Selecciona una captura de pantalla de tu sketch y colócala en tu bitácora.

<img width="1526" height="816" alt="imagen" src="https://github.com/user-attachments/assets/e9c10697-d585-49eb-adc1-fb5e877a4796" />


