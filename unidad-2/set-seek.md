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

#### ¿Para qué sirve el método mag()? Nota que hay otro método llamado magSq(). ¿Cuál es la diferencia entre ambos? ¿Cuál es más eficiente?

Calculates the magnitude (length) of the vector squared.

* mag() calcula la magintud de un vector.

* magSq() calcula la magnitud elevandola al cuadrado. (||v||² = x² + y², dandose de esta forma)

Me atrevo a decir que no hay uno "más eficiente" que el otro, debido a que según lo queramos usar uno o otro pueden ser más convenientes. Ahora, lo que pasa es que cuando sacamos el resultado de suma de componentes de vector el teorema de pitágoras con su raíz puede hacer que tengamos resultados diferentes si usamos mag o magSq.

#### ¿Para qué sirve el método normalize()?

normalize() convierte un vector en un vector unitario, de magnitud 1 con su dirección original.

#### Te encuentras con un periodista en la calle y te pregunta ¿Para qué sirve el método dot()? ¿Qué le responderías en un frase?

Cálcula el producto punto entre dos vectores. En simples palabras: mide dos vectores, para saber si apuntan en la misma dirección, opuestas o perpendiculares.

#### El método dot() tiene una versión estática y una de instancia. ¿Cuál es la diferencia entre ambas?

Ambas hacen lo mismo. Solo cambia cómo se escriben.

#### Ahora el mismo periodista curioso de antes te pregunta si le puedes dar una intuición geométrica acerca del producto cruz. Entonces te pregunta ¿Cuál es la interpretación geométrica del producto cruz de dos vectores? Tu respuesta debe incluir qué pasa con la orientación y la magnitud del vector resultante.

El producto cruz entre dos vectores:

Devuelve un nuevo vector perpendicular al plano definido por los dos vectores originales. La magnitud del nuevo vector es igual al área del plano formado.

<img width="526" height="530" alt="imagen" src="https://github.com/user-attachments/assets/28bae3a8-d775-408c-a09e-52191c0a9952" />

#### ¿Para que te puede servir el método dist()?

Para calcular la distancia entre dos vectores.

#### ¿Para qué sirven los métodos normalize() y limit()?

normalize()	Cambia la magnitud del vector a 1, sin cambiar su dirección.
limit()	Limita la magnitud máxima del vector a un valor dado.

### Actividad 05

#### El código que genera el resultado que te pedí.

``` js
let x = 0;
let dx = 0.01;

function setup() {
  createCanvas(500, 500);
}

function draw() {
  background(200);

  x += dx;
  if (x > 1 || x < 0) {
    dx *= -1;
  }

  let v0 = createVector(50, 50);
  let v1 = createVector(300, 0);
  let v2 = createVector(0, 300);
  let v3 = p5.Vector.lerp(v1, v2, x);
  let v4 = createVector(-300, 300);
  let i = p5.Vector.add(v0, v1);
  drawArrow(v0, v1, "red");
  drawArrow(v0, v2, "blue");
  drawArrow(v0, v3, "purple");
  drawArrow(i, v4, "green");
  drawArrow(v0, v3, lerpColor("red", "blue", x));
}

function drawArrow(base, vec, myColor) {
  push();
  stroke(myColor);
  strokeWeight(3);
  fill(myColor);
  translate(base.x, base.y);
  line(0, 0, vec.x, vec.y);
  rotate(vec.heading());
  let arrowSize = 7;
  translate(vec.mag() - arrowSize, 0);
  triangle(0, arrowSize / 2, 0, -arrowSize / 2, arrowSize, 0);
  pop();
}
```

#### ¿Cómo funciona lerp() y lerpColor().

En resumidas cuentas ambos funcionan parecido, tomando un valor A y valor B, los cuales se interpolan por un diferencial dado, en el caso del ejercicio: "x". Basicamente, calcula números en un intervalo por determinados incrementos.

* lerp()

<img width="97" height="93" alt="imagen" src="https://github.com/user-attachments/assets/8da42499-e22f-41dc-962d-1cea7d1f726f" />

* lerpColor()

<img width="98" height="103" alt="imagen" src="https://github.com/user-attachments/assets/6b59fd05-e627-44ef-8654-b88f6c8cad11" />

#### ¿Cómo se dibuja una flecha usando drawArrow()?

La función necesita dos "componentes" base para funcionar, siendo vector de partida y vector dirección, siendo su distancia entre ellos lo que dará su magnitud, además, le agregaremos un componente que dicta su color.

### Actividad 06

#### Cuál es el concepto del marco motion 101 y cómo se interpreta geométricamente.



#### ¿Cómo se aplica motion 101 en el ejemplo?



### Actividad 07



