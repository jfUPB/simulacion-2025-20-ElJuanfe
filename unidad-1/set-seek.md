# Unidad 1

## 🔎 Fase: Set + Seek

### Actividad 01

#### Piensa y describe en una sola frase y en tus propias palabras cómo la aleatoriedad influye en el arte generativo.

La aleatoriedad es aquella que le da vida a cada generación de una generación, haciendo que no toda generación sea igual, pero dandole vida a través de las condiciones "ambientales".

### Actividad 02

#### Luego de ver el trabajo de Sofía piensa y escribe en TUS PROPIAS palabras:

    1.) Cuál es el papel de la aleatoriedad en su obra.
    2.) Según tu perfil profesional, cómo se aplica el concepto de aleatoriedad en el tipo de proyectos que desarrollas. Ilustra tu respuesta con ejemplos concretos.

1. El papel de la aleatoriedad es aquel de hacer que cada "rama" tenga una identidad propia, la cual se pueda distinguir de las demás, dándole diferenciación, pero no disonancia. Todo esto hace que el arte no se vea tan plano ni insipido, le otorga la naturalidad que le resta artificialidad.

2. En los proyectos que he desarrollado usé brochas que varían ligeramente de tonos, ruido gaussiano que pongo en los colores de mis dibujos y texturas de los mismos, esculpido para modelos 3D y también en cierta medida los "errores" (si así se le pueden llamar) humanos que cometo y le dan identidad a mis proyectos.

### Actividad 03

##### Realiza el siguiente experimento y reporta los resultados en tu bitácora:

Modifica el código del ejemplo Example 0.1: A Traditional Random Walk.
``` js
let walker;
let Red, Green, Blue;

function setup() {
  createCanvas(1920, 1080);
  walker = new Walker();
  background(255);
  Red = 0;
  Green = 0;
  Blue = 0;
}

function draw() {
  walker.step();
  walker.show();
}

class Walker {
  constructor() {
    this.x = width / 2;
    this.y = height / 2;
  }

  show() {
    stroke(Red, Green, Blue);
    fill(Red, Green, Blue); // Opcional: relleno del círculo
    circle(this.x, this.y, 10); // Dibuja círculo con tamaño
  }

  step() {
    const choice = floor(random(4));
    if (choice == 0) {
      this.x += 5;
      Red = 255;
      Green = 0;
      Blue = 0;
    } else if (choice == 1) {
      this.x -= 5;
      Red = 0;
      Green = 255;
      Blue = 0;
    } else if (choice == 2) {
      this.y += 5;
      Red = 0;
      Green = 0;
      Blue = 255;
    } else {
      this.y -= 5;
      Red = 0;
      Green = 0;
      Blue = 0;
    }
  }
}
```

#### Antes de ejecutar el código, escribe en tu bitácora qué esperas que suceda.
Espero que cambie de color aleatoriamente y se mueva a una mayor velocidad, además de que en vez de que sea un punto se trate de un círculo con un radio determinado.

#### Ejecuta el código y escribe en tu bitácora qué sucedió realmente.
Sucedió lo que predije anteriormente, donde se genera un circulo que cambia de colores aleatoriamente.

<img width="590" height="548" alt="image" src="https://github.com/user-attachments/assets/d96eb4e1-7047-4eaa-a27d-10faed6020fd" width="400"/>

#### Ocurrió lo que esperabas? ¿Por qué crees que sí o por qué crees que no?
Sí, debido a que los cambios no fueron muy complicados, además, son conceptos que ya conozco anteriormente

### Actividad 04

#### En tus propias palabras cuál es la diferencia entre una distribución uniforme y una no uniforme de números aleatorios.
Una distribución uniforme es aquella que se encarga de distribuir a todos los valores posibles de forma en que todos reciban una distribución similar, mientras que una no uniforme se encarga de hacer que ciertos valore sean más probables de salir que otros, siendo parametrizados.

#### Modifica el código de la caminata aleatoria para que utilice una distribución no uniforme, favoreciendo el movimiento hacia la derecha.
Aquí modifiqué el código original, donde reemplacé la función random por RandomGaussian, donde deje las 4 posibilidades diferentes intactas, pero también modifiqué la desviación estándar por 2.
``` js
let walker;

function setup() {
  createCanvas(640, 240);
  walker = new Walker();
  background(255);
}

function draw() {
  walker.step();
  walker.show();
}

class Walker {
  constructor() {
    this.x = width/2;
    this.y = height;
  }

  show() {
    stroke(0);
    point(this.x, this.y);
  }

  step() {
    const choice = floor(randomGaussian(4,2));
    if (choice == 0) {
      this.x++;
    } else if (choice == 1) {
      this.x--;
    } else if (choice == 2) {
      this.y++;
    } else {
      this.y--;
    }
  }
}
```

Además, también modifiqué el código para probar lo que pasaría si la desviación estándar fuese de cero. Observé que solo se generaba una línea recta, debido a que no puede desviarse.
``` js
let walker;

function setup() {
  createCanvas(640, 240);
  walker = new Walker();
  background(255);
}

function draw() {
  walker.step();
  walker.show();
}

class Walker {
  constructor() {
    this.x = width/2;
    this.y = height;
  }

  show() {
    stroke(0);
    point(this.x, this.y);
  }

  step() {
    const choice = floor(randomGaussian(4,0));
    if (choice == 0) {
      this.x++;
    } else if (choice == 1) {
      this.x--;
    } else if (choice == 2) {
      this.y++;
    } else {
      this.y--;
    }
  }
}
```

