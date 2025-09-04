# Evidencias de la unidad 4

## Explicación conceptual de la obra

* ¿Qué concepto de la unidad 4 y cómo lo aplicaste en la obra?
> Tu respuesta aquí:
> Funciones sinusoides para mover la cola de los peces.

* ¿Qué concepto de la unidad 3 y cómo lo aplicaste en la obra?
> Tu respuesta aquí:
> Tengo atracción gravitacional que atrae hacia el cursor a los peces.

* ¿Qué concepto de la unidad 2 y cómo lo aplicaste en la obra?
> Tu respuesta aquí:
> Usé operaciones básicas para vectores, Motion 101 y Aceleraciones, todo esto con el fin de que se pueda mover cada pez.

* ¿Qué concepto de la unidad 1 y cómo lo aplicaste en la obra?
> Tu respuesta aquí:
> Random walker para generar direcciones aleatorias.

## ¿Cómo resolviste la interacción?
> Tu respuesta aquí:
> Pues hice que tanto botones del 1 al 3 tengan funciones distintas cada uno, además, el mouse tambien funciona con dos de los tres modos para interactuar, atrayendo o ahuyentando peces.

## Enlace a la obra en el editor de p5.js

[Aquí está mi obra](https://editor.p5js.org/ElJuanfe/full/s5uyEf9NF)

## Código de la obra 

``` js
//Pez
class Pez {
  constructor(x, y) {
    this.pos = createVector(x, y);
    this.vel = p5.Vector.random2D();
    this.acc = createVector(0, 0);
    this.maxSpeed = random(2, 3);
    this.oscAngle = random(TWO_PI);
  }

  applyForce(f) {
    this.acc.add(f);
  }

  interact(peers, target, modo) {
    if (modo === 2) {
      // Jitter más fuerte para caos
      let jitter = p5.Vector.random2D();
      jitter.mult(0.5);
      this.applyForce(jitter);

      // Huir del cursor
      let flee = p5.Vector.sub(this.pos, target);
      flee.setMag(0.2);
      this.applyForce(flee);
    }

    if (modo === 3) {
      // Atraerse al cursor
      let attraction = p5.Vector.sub(target, this.pos);
      attraction.setMag(0.05);
      this.applyForce(attraction);
    }

    // Separación entre peces
    for (let other of peers) {
      let d = p5.Vector.dist(this.pos, other.pos);
      if (other !== this && d < 30) {
        let repel = p5.Vector.sub(this.pos, other.pos);
        repel.setMag(0.1);
        this.applyForce(repel);
      }
    }

    // Velocidad según modo
    if (modo === 1) this.maxSpeed = 1.5;
    if (modo === 2) this.maxSpeed = 5;
    if (modo === 3) this.maxSpeed = 3;
  }

  update() {
    this.vel.add(this.acc);
    this.vel.limit(this.maxSpeed);
    this.pos.add(this.vel);
    this.acc.mult(0);
    this.oscAngle += 0.3;
  }

  edges() {
    if (this.pos.x < -20) this.pos.x = width + 20;
    if (this.pos.x > width + 20) this.pos.x = -20;
    if (this.pos.y < -20) this.pos.y = height + 20;
    if (this.pos.y > height + 20) this.pos.y = -20;
  }

  display() {
    push();
    translate(this.pos.x, this.pos.y);
    rotate(this.vel.heading());
    noStroke();
    fill(255,150,0);
    ellipse(0, 0, 20, 10); // cuerpo

    let tail = sin(this.oscAngle) * 5;
    triangle(-10, 0, -18, -tail, -18, tail); // cola
    pop();
  }
}

//Sketch
let peces = [];
let numPeces = 500;
let modo = 1;

function setup() {
  createCanvas(windowWidth, windowHeight);
  for (let i = 0; i < numPeces; i++) {
    peces.push(new Pez(random(width), random(height)));
  }
}

function draw() {
  if (modo === 1) {
    background(135, 206, 235); // calma
  } else if (modo === 2) {
    background(200, 50, 50); // alerta
  } else if (modo === 3) {
    background(20, 30, 50); // agrupamiento
  }

  let mouse = createVector(mouseX, mouseY);

  for (let pez of peces) {
    pez.interact(peces, mouse, modo);
    pez.update();
    pez.edges();
    pez.display();
  }

  mostrarModo();
}

function keyPressed() {
  if (key === '1') modo = 1;
  if (key === '2') modo = 2;
  if (key === '3') modo = 3;
}

function mostrarModo() {
  fill(255);
  noStroke();
  textSize(14);
  text(`Modo: ${modo} (Presiona 1 para calma, 2 para asustar, 3 para seguir al cursor)`, 10, height - 10);
}

```

## Captura de pantalla representativa
<img width="1687" height="809" alt="image" src="https://github.com/user-attachments/assets/c8872dcf-3ba4-461b-a49a-d9466d0b1aec" />







