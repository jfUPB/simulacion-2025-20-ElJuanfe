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

### Actividad 03

#### ¿Qué resultado esperas obtener en el programa anterior?

Que tengamos un vector con posición que cambia en dos ocasiones, al cual se imprime en dos momentos.

#### ¿Qué resultado obtuviste?

Se crea un vector (6, 9), se imprime, una función cambia sus posiciones, y se imprime nuevamente en la consola.

#### Recuerda los conceptos de paso por valor y paso por referencia en programación. Muestra ejemplos de este concepto en javascript.

<img width="888" height="439" alt="imagen" src="https://github.com/user-attachments/assets/3213a421-e5e0-4ee7-80be-9794fcff99bb" />

* Paso por valor

``` js
let position;

function setup() {
  createCanvas(400, 400);
  position = createVector(6, 9);
  
  console.log("Antes:", position.toString());
  
  let copia = position.copy();
  modificarVector(copia);
  
  console.log("Después:", position.toString());
  
  noLoop();
}

function modificarVector(v) {
  v.x = 300;
  v.y = 300;
}
```

* Paso por referencia

``` js
let position;

function setup() {
  createCanvas(400, 400);
  position = createVector(6, 9);
  
  console.log("Antes:", position.toString());
  
  modificarVector(position);
  
  console.log("Después:", position.toString());
  
  noLoop();
}

function modificarVector(v) {
  v.x = 100;
  v.y = 200;
}
```

#### ¿Qué tipo de paso se está realizando en el código?

Se está realizando un paso por referencia, y al modificarlo dentro de esa función, los cambios afectan directamente a position.

#### ¿Qué aprendiste?

En js, los objetos se pasan por referencia, y si modificas un objeto dentro de una función, modificas el objeto original.

De cierta forma un paso por referencia es como si se tratase de cambios dentro de github en una rama la cual aplica cambios al main.

### Actividad 04


¿Para qué sirve el método mag()? Nota que hay otro método llamado magSq(). ¿Cuál es la diferencia entre ambos? ¿Cuál es más eficiente?
¿Para qué sirve el método normalize()?
Te encuentras con un periodista en la calle y te pregunta ¿Para qué sirve el método dot()? ¿Qué le responderías en un frase?
 El método dot() tiene una versión estática y una de instancia. ¿Cuál es la diferencia entre ambas?
Ahora el mismo periodista curioso de antes te pregunta si le puedes dar una intuición geométrica acerca del producto cruz. Entonces te pregunta ¿Cuál es la interpretación geométrica del producto cruz de dos vectores? Tu respuesta debe incluir qué pasa con la orientación y la magnitud del vector resultante.
¿Para que te puede servir el método dist()?
¿Para qué sirven los métodos normalize() y limit()?


