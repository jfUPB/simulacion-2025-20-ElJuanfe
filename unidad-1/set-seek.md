# Unidad 1

## 🔎 Fase: Set + Seek

### Actividad 07

#### Explica el concepto qué resultados esberabas obtener.

Originalmente quería hcar un efecto complejo en el cual se crearan patrones aleatorios de movimiento para figuras geometricas como círculos, cuadrados y triangulos, pero debido a que no se veía bien fui mejor por "Polka Dots" que hicieran algo simple con el perlin, por lo cual tomé cambio de tamaño de cada uno.

#### Copia el código en tu bitácora.

``` js
let columnas, filas;
let ancho = 20;
let t = 0;
let k = 0.1

function setup() {
  createCanvas(600, 400);
  columnas = floor(width / ancho);
  filas = floor(height / ancho);
  noStroke();
  fill(100, 150, 255, 150);
}

function draw() {
  background(0);
  for (let y = 0; y < filas; y++) {
    for (let x = 0; x < columnas; x++) {
      let xoff = x * k;
      let yoff = y * k;
      let ruido = noise(xoff, yoff, t);
      let r = map(ruido, 0, 1, 2, ancho);

      ellipse(x * ancho, y * ancho, r, r);
    }
  }

  t += 0.01;
}
```

#### Coloca en enlace a tu sketch en p5.js en tu bitácora.

[Pola Dots Perlin](https://editor.p5js.org/ElJuanfe/full/BJxcWdqNf)

##### Selecciona una captura de pantalla de tu sketch y colócala en tu bitácora.

<img width="618" height="403" alt="imagen" src="https://github.com/user-attachments/assets/ce011d84-9984-4b87-968b-c6d870ce2bdd" />
