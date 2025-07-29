# Unidad 2

## 🔎 Fase: Set + Seek

### Actividad 01

#### ¿Cómo funciona la suma dos vectores en p5.js?

Para un solo vector, tenemos componentes en x e y (y otros componentes en otros excenarios, como z o w). Ahora, si sumamos dos vectores, siendo A=(x1, y1) y B=(x2, y2), tendrémos que se suman sus componentes entre si, de forma que A + B = (x1 +x2, y1 + y2).

#### ¿Por qué esta línea position = position + velocity; no funciona?

Debido a que javascript no sabe como usar el + para sumar vectores, debido a que son objetos.

### Actividad 02

#### ¿Qué tuviste que hacer para hacer la conversión propuesta?

En resumen, las dos principales cosas que hice fue poner en el draw() la adición (add.) y modifiqué en el step() los resultados que da cada condicional.

#### Muestra el código que utilizaste para resolver el ejercicio.

``` js
let walker;
let position;
let velocity;

function setup() {
  createCanvas(640, 240);
  walker = new Walker();
  background(255);

  position = createVector(width/2, height/2);
  velocity = createVector(5, 5);
}

function draw() {
  position.add(velocity)
  walker.step();
  walker.show();
}

class Walker {
  constructor() {
  }

  show() {
    stroke(0);
    point(position.x,position.y)

    //point(this.x, this.y);
  }

  step() {
    const choice = floor(random(4));
    if (choice == 0) {
    velocity.x = velocity.x * 1;
    } else if (choice == 1) {
    velocity.x = velocity.x * -1;
    } else if (choice == 2) {
    velocity.y = velocity.y * 1;
    } else {
    velocity.y = velocity.y * -1;
    }
  }
}
```
