# Evidencias de la unidad 8


### Actividad 01

Describe tus observaciones sobre la conexión sonido-imagen en al menos dos de las performances vistas.

* Dimension N: Hay algunos tipos de instrumentos que tuvieron comportamientos sobre la obra que alterban bastante lo que veiamos, por ejemplo el sintetizador con sonidos más etéreos daba colores pálidos y formas curiosas, también una campanilla alteraba con gran impacto algunas circunferencias, el xilófono también fue importantes, porque le daba color y movimiento a la obra cada vez que estaba presente.

* LE PARODY & ALBA G. CORRAL: A medida que entraban más instrumentos a la obra también entraban formas que los representaban, por lo general siendo muy abstractas dichas figuras, sin embargo lo que más me llamó la atención es que las voces fueron el principal instrumento, no solo auditivamente, sino que tambien aportaban una gran carga visual.

Explica qué elementos te parecieron generativos y por qué crees que cada visualización sería única.

Principalmente cuando se generaban partículas en algunos momentos, los colores que pienso yo variarían entre obra y obra sutilmente, tamaños, adicionalmente

Comparte tu reflexión sobre la sensación de “liveness”.

Siento que se siente muy abstracta y artística, además de que le da un cuerpo aún más físico a la música que podemos escuchar.

### Actividad 02

La pieza musical elegida (con enlace/archivo si es posible).

[Everything In Its Right Place - Radiohead](https://youtu.be/NUnXxh5U25Y?si=K56BWINGODXJkD11)

La descripción de tu concepto visual.

Quiero tener un algoritmo de flow fields para varias instancias de un pez, los cuales tengan ligeras oscilaciones mientras se muevan, a su vez, quiero que tengan movimientos que vayan cambiando según la intensidad y tono de la voz del cantante, siendo que para la voz cuando tenga mayor agudez los peces suban a la parte de arriba del canvas y más fuerte la voz hace que se muevan con mayor rapidez. me gustaría que los peces cada uno tenga un walker que se encargue del movimiento . Quiero tambien que según la suma de las intensidades de todas las frecuencias el color del fondo transicione de azul a rojo, donde mayor la suma más rojizo. quiero que con el sintetizador que se escucha de fondo en la canción se generen olas en el agua aleatoriamente. quiero que las reverberaciones de la voz tengan generen una aberración cromática cuando suenan. quiero que cuando la voz sea aguda los peces tengan caminatas de levy cortas, mientras que con voz grave sea grandes dichas levy.

Los inputs seleccionados y la justificación de por qué los elegiste.

Los inputs serán el teclado y la propia canción, porque quiero que se tenga cierta autonomía pero yo poder influir en la obra.

¿Qué algoritmos o técnicas planeas usar (ej: flow fields, flocking, física, partículas, etc.) y por qué?

Flow fields, oscilaciones, lerps, principalmente, porque quiero dar la naturalidad de un cardúmen a la par que algo que se sienta alienígena en cierta medida.

Tus bocetos y una explicación de cómo los inputs influirán en los visuales.

No tengo como tal bocetos, pero tengo este código con el cual he venido trabajando algunas cosas.

[Link a p5.js](https://editor.p5js.org/ElJuanfe/sketches/gneqNvckL)

Adicionalmente he hechos testeos de cosas que quise añadir con chatgpt.

Ahora, cómo influyen los inputs?

Los de la música, alteran flowfields y hacen que las gotas aparezcan.

Yo controlo la "electricidad"

### Actividad 03

El código fuente completo de tu sketch en p5.js.

``` js
// sketch.js (p5 global mode)
// Pez intacto (tomado de tu ejemplo) -> 30% más pequeño
// FlowField controla movimiento; LevyWalker maneja "walking" con jitter; sin flocking.

let song = null;
let songLoaded = false;
let fft = null;
let amp = null;
let energyHistory = [];

const NUM_FISH = 18;
const BASE_CONSTRAIN_LENGTHS = [22,28,29,28,27,25,22,20,17,14,11,9,6,5];
const SIZE_SCALE = 0.7; // reduce size 30%
const CONSTRAIN_LENGTHS = BASE_CONSTRAIN_LENGTHS.map(v => v * SIZE_SCALE);
const BASE_MAX_ANGLE = Math.PI/3;

let fishes = [];
let rippleManager;
let flowField;
let electricNetwork;


// Flow tuning
const FLOW_COLS = 40;
const FLOW_ROWS = 28;
const FLOW_BASE_SCALE = 0.01;
const FLOW_BASE_STRENGTH = 0.6;
const FLOW_TIME_SPEED = 0.0009;

function preload() {
  // loadSound con callbacks
  try {
    song = loadSound('ever.mp3',
      () => { songLoaded = true; console.log('✅ Audio cargado.'); },
      (err) => { console.error('❌ Error cargando audio:', err); song = null; songLoaded = false; }
    );
  } catch (e) {
    console.warn('loadSound fallo:', e);
    song = null; songLoaded = false;
  }
}

function setup() {
  createCanvas(1000, 700);
  frameRate(60);

  fft = new p5.FFT(0.9, 1024);
  amp = new p5.Amplitude(0.9);

  if (song && songLoaded) {
    try { fft.setInput(song); amp.setInput(song); } catch(e){ console.warn('FFT/AMP no conectados:', e); }
  } else console.warn('Audio no disponible en setup; continua sin él.');

  rippleManager = new RippleManager();
  flowField = new FlowField(FLOW_COLS, FLOW_ROWS, FLOW_BASE_SCALE, FLOW_BASE_STRENGTH);

  for (let i=0;i<NUM_FISH;i++){
    let x = random(width*0.12, width*0.88);
    let y = random(height*0.18, height*0.82);
    fishes.push(new Fish(createVector(x,y), CONSTRAIN_LENGTHS));
    
    electricChains = new ElectricChainManager();
electricNetwork = new ElectricNetwork(35);

  }
}

function draw() {
  let rms = 0;
  let centroid = width * 0.5;
  let sumNorm = 0;
  let synthEnergy = 0;

  if (song && songLoaded && fft && amp) {
    let spectrum = fft.analyze();
    rms = amp.getLevel();
    energyHistory.push(rms);
    if (energyHistory.length > 160) energyHistory.shift();

    centroid = spectralCentroid(spectrum);
    let sumEnergy = spectrum.reduce((a, b) => a + b, 0);
    sumNorm = constrain(sumEnergy / (spectrum.length * 255), 0, 1);
    synthEnergy = fft.getEnergy(600, 4000);

    // background sutil dinámico
    let bgR = lerp(10, 180, sumNorm);
    let bgG = lerp(15, 50, sumNorm * 0.8);
    let bgB = lerp(50, 25, sumNorm);
    background(bgR, bgG, bgB);
  } else {
    background(12, 18, 50);
    if (energyHistory.length > 160) energyHistory.shift();
  }

  // actualiza flow field con audio
  flowField.update(
    millis() * FLOW_TIME_SPEED,
    FLOW_BASE_SCALE * lerp(0.6, 1.6, sumNorm),
    FLOW_BASE_STRENGTH * lerp(0.6, 1.8, sumNorm)
  );

  rippleManager.update(rms, synthEnergy);
  rippleManager.draw();

  // movimiento peces
  for (let f of fishes) {
    let agudez = map(centroid, 0, width, 0, 1);
    let speedFactor = map(rms, 0, 0.25, 0.6, 2.2);
    let verticalBias = map(centroid, 0, width, height * 0.9, height * 0.12);
    let audioParams = { agudez, speedFactor, sumNorm, rms, verticalBias };
    f.updateAudioParams(audioParams);

    let walkerJitter = f.walker.update(1);
    let flowF = flowField.lookup(f.pos.x, f.pos.y).mult(lerp(0.3, 1.2, sumNorm));
    let steer = p5.Vector.add(flowF, walkerJitter.mult(0.6));

    const EDGE_THRESH = 160;
    const EDGE_STRENGTH = 0.26;
    if (f.pos.x < EDGE_THRESH)
      steer.add(createVector((EDGE_THRESH - f.pos.x) / EDGE_THRESH * EDGE_STRENGTH, 0));
    if (f.pos.x > width - EDGE_THRESH)
      steer.add(createVector(-(f.pos.x - (width - EDGE_THRESH)) / EDGE_THRESH * EDGE_STRENGTH, 0));
    if (f.pos.y < EDGE_THRESH * 0.6)
      steer.add(createVector(0, (EDGE_THRESH * 0.6 - f.pos.y) / (EDGE_THRESH * 0.6) * EDGE_STRENGTH));
    if (f.pos.y > height - EDGE_THRESH * 0.6)
      steer.add(createVector(0, -(f.pos.y - (height - EDGE_THRESH * 0.6)) / (EDGE_THRESH * 0.6) * EDGE_STRENGTH));

    f.applyForce(steer);
    f.update();
    f.draw();
  }

let intensity = pow(rms * 6, 1.4); // curva más agresiva
let baseAlpha = map(sumNorm, 0, 1, 0.1, 0.5);
let offsetR = 10 + intensity * 40;
let offsetG = 8 + intensity * 30;
let offsetB = 5 + intensity * 20;
let jitter = random(-2, 2);
applyChromaticAberration(offsetR + jitter, offsetG - jitter, offsetB + jitter*0.5, baseAlpha);




  // texto debug
  push();
  fill(255, 200);
  textSize(12);
  textAlign(LEFT, TOP);
  text(`Flow ${flowField.cols}x${flowField.rows}  Fish:${fishes.length}`, 10, 10);
  pop();
  
  electricChains.update();
electricChains.draw();
  
  electricNetwork.update();
electricNetwork.draw();


}


// ---------------- FLOW FIELD ----------------
class FlowField {
  constructor(cols, rows, baseScale, baseStrength) {
    this.cols = cols;
    this.rows = rows;
    this.baseScale = baseScale;
    this.baseStrength = baseStrength;
    this.field = new Array(cols * rows);
    this.zoff = 0;
    this.update(0, baseScale, baseStrength);
  }
  update(z, scale, strength) {
    this.zoff = z;
    this.scale = scale;
    this.strength = strength;
    let i = 0;
    for (let y=0;y<this.rows;y++){
      for (let x=0;x<this.cols;x++){
        let u = x / this.cols;
        let v = y / this.rows;
        let nx = u * this.scale * width;
        let ny = v * this.scale * height;
        let n = noise(nx, ny, this.zoff + x*0.001 + y*0.001);
        let ang = map(n, 0, 1, 0, TWO_PI);
        this.field[i++] = createVector(cos(ang), sin(ang)).mult(this.strength);
      }
    }
  }
  lookup(x, y) {
    let col = floor(constrain(x / width * this.cols, 0, this.cols-1));
    let row = floor(constrain(y / height * this.rows, 0, this.rows-1));
    return this.field[row * this.cols + col].copy();
  }
}

// ---------------- RIPPLE MANAGER (gotas) ----------------
class RippleManager {
  constructor(){ this.ripples = []; this.time = 0; }
  update(rms, synthEnergy) {
    this.time += deltaTime*0.001;
    let prob = map(synthEnergy,0,255,0.0008,0.12)*(1+rms*3);
    if (random() < prob) {
      let x = random(width*0.05, width*0.95);
      let y = height * random(0.76, 0.92);
      this.spawn(x,y,map(synthEnergy,0,255,6,38));
    }
    if (random() < 0.0025) this.spawn(random(width*0.1,width*0.9), height * random(0.76,0.92), random(6,30));
    for (let r of this.ripples) r.update();
    this.ripples = this.ripples.filter(r => !r.dead);
  }
  spawn(x,y,strength=18){ this.ripples.push(new Ripple(x,y,strength)); }
  draw(){ for (let r of this.ripples) r.draw(); }
}
class Ripple {
  constructor(x,y,str){ this.x=x; this.y=y; this.age=0; this.str=str; this.maxAge=random(1.4,3.2); this.dead=false; }
  update(){ this.age += deltaTime*0.001; if (this.age > this.maxAge) this.dead = true; }
  draw(){
    let t = constrain(this.age / this.maxAge, 0, 1);
    let radius = lerp(4, this.str*18, t);
    let alpha = lerp(200,0,t);
    noFill();
    strokeWeight(2);
    let rings = 3;
    for (let i=0;i<rings;i++){
      let r = radius * (1 + i*0.08);
      stroke(255,255,255, alpha * (0.6 - i*0.18));
      ellipse(this.x, this.y, r, r*0.36);
    }
    noStroke();
    fill(255,255,255, alpha*0.6*(1-t));
    circle(this.x, this.y, lerp(2,12, 1 - Math.pow(1-t,2)));
  }
}

// ---------------- LevyWalker (con ligeras oscilaciones) ----------------
class LevyWalker {
  constructor(seedPos) {
    this.pos = seedPos.copy();
    this.stepTimer = 0;
    this.stepDuration = 60;
    this.stepTarget = this.pos.copy();
    this.alpha = 1.6;
    this.scale = 40;
    this.oscPhase = random(1000);
  }
  setAlpha(a){ this.alpha = a; }
  setScale(s){ this.scale = s; }
  startStep(center, agudezFactor) {
    let base = Math.pow(random(), -1/this.alpha) * this.scale;
    let length = constrain(base * (1.5 - agudezFactor*0.9), 6, width * 0.55);
    let angle = random(TWO_PI);
    this.stepTarget = createVector(center.x + cos(angle)*length, center.y + sin(angle)*length);
    this.stepTimer = 0;
    this.stepDuration = int(map(length, 20, width*0.55, 20, 230));
  }
  update(dt) {
    this.stepTimer += dt;
    let t = constrain(this.stepTimer / max(1,this.stepDuration), 0, 1);
    // smoothstep easing
    let s = smoothstep(t);
    this.pos = p5.Vector.lerp(this.pos, this.stepTarget, s);
    // jitter + slight oscillation for liveliness
    let jitter = p5.Vector.random2D().mult(noise(frameCount*0.01 + this.oscPhase) * 0.6);
    let osc = createVector(cos(frameCount*0.01 + this.oscPhase), sin(frameCount*0.01 + this.oscPhase)).mult(0.6);
    return p5.Vector.add(jitter, osc);
  }
  isStepDone(){ return this.stepTimer >= this.stepDuration; }
}

// ---------------- PEZ (clase restaurada, con tamaños reducidos) ----------------
class Fish {
  constructor(initialPos, constrainLengths) {
    this.pos = initialPos.copy();
    this.constrainLengths = constrainLengths.slice(); // already scaled externally
    this.numSegments = this.constrainLengths.length + 1;
    this.rope = [];
    for (let i=0;i<this.numSegments;i++) this.rope.push(createVector(this.pos.x - i*2, this.pos.y));
    this.boneAngles = new Array(this.numSegments);
    this.ANGLE_LIMITS = [];
    let minL = Math.min(...this.constrainLengths);
    let maxL = Math.max(...this.constrainLengths);
    for (let l of this.constrainLengths) {
      let a = map(l, minL, maxL, BASE_MAX_ANGLE, Math.PI / 6);
      this.ANGLE_LIMITS.push(a);
    }
    this.vel = createVector(random(-1,1), random(-0.5,0.5));
    this.maxSpeed = random(0.9, 1.8) * 0.9;
    this.walker = new LevyWalker(this.pos);
    this.audioParams = { agudez:0.5, speedFactor:1, sumNorm:0, rms:0 };
    this.bodyColor = color(30,20,80);
    this.finColor = color(120,180,200);
    this.tailOsc = 0;
    this.oscOffset = random(1000);
    this.vel.mult(0.6);
  }

  applyForce(f) {
    // small acceleration
    this.vel.add(f);
    this.vel.limit(this.maxSpeed * (1 + 0.6 * (this.audioParams.speedFactor || 1)));
  }

  updateAudioParams(ap) {
    this.audioParams = ap;
    let a = lerp(1.2, 2.2, ap.agudez);
    this.walker.setAlpha(a);
    let sc = lerp(30, 180, 1 - ap.agudez);
    this.walker.setScale(sc);
    let t = ap.sumNorm;
    this.bodyColor = color(lerp(20,120,t), 20, lerp(80,40,t));
    this.finColor = color(lerp(110,220,t), lerp(180,60,t), lerp(200,80,t));
  }

  wanderStep() {
    if (this.walker.isStepDone()) {
      let center = this.pos.copy();
      center.y = constrain(center.y * 0.82 + this.audioParams.agudez * 90, 10, height - 10);
      this.walker.startStep(center, this.audioParams.agudez);
    }
  }

  update() {
    // integrate velocity + walker target influence done externally in draw main loop
    this.wanderStep();

    // keep inside canvas with wrap margins
    const HARD_MARGIN = 220;
    if (this.pos.x < -HARD_MARGIN) this.pos.x = width + HARD_MARGIN;
    if (this.pos.x > width + HARD_MARGIN) this.pos.x = -HARD_MARGIN;
    if (this.pos.y < -HARD_MARGIN*0.6) this.pos.y = height + HARD_MARGIN*0.6;
    if (this.pos.y > height + HARD_MARGIN*0.6) this.pos.y = -HARD_MARGIN*0.6;

    // update head position by velocity
    this.pos.add(this.vel);

    // update rope: head follows this.pos
    this.rope[0] = createVector(this.pos.x, this.pos.y);
    for (let i=1;i<this.rope.length;i++){
      let d = this.constrainLengths[i-1];
      this.rope[i] = constrainDistance(this.rope[i], this.rope[i-1], d);
    }

    // compute bone angles
    for (let i=1;i<this.numSegments;i++){
      this.boneAngles[i] = atan2(this.rope[i].y - this.rope[i-1].y, this.rope[i].x - this.rope[i-1].x);
    }

    // iterative constraints for rigidity (restored)
    let passes = 3;
    for (let pass=0; pass<passes; pass++){
      for (let i=2;i<this.numSegments;i++){
        let prevAngle = this.boneAngles[i-1];
        let curAngle = this.boneAngles[i];
        let maxA = this.ANGLE_LIMITS[i-2] || BASE_MAX_ANGLE;
        let rel = normalizeAngle(curAngle - prevAngle);
        if (rel > maxA) curAngle = prevAngle + maxA;
        else if (rel < -maxA) curAngle = prevAngle - maxA;
        this.boneAngles[i] = curAngle;
        let len = this.constrainLengths[i-1];
        this.rope[i].x = this.rope[i-1].x + cos(curAngle) * len;
        this.rope[i].y = this.rope[i-1].y + sin(curAngle) * len;
      }
      for (let i=this.numSegments-2;i>=1;i--){
        let a = atan2(this.rope[i+1].y - this.rope[i].y, this.rope[i+1].x - this.rope[i].x);
        this.boneAngles[i+1] = a;
      }
    }

    this.tailOsc = sin((frameCount + this.oscOffset) * 0.08) * map(this.audioParams.rms || 0, 0, 0.2, 0.08, 1.0);
  }

  draw() {
    // construct contour exactly like example (kept intact)
    let leftSide = [];
    let rightSide = [];
    for (let i=0;i<this.constrainLengths.length;i++){
      let current = this.rope[i];
      let next = this.rope[i+1];
      let radius = this.constrainLengths[i];
      let dir = p5.Vector.sub(next, current);
      if (dir.mag() < 0.0001) dir = createVector(1,0);
      dir.normalize();
      let perp = createVector(-dir.y, dir.x);
      let left = p5.Vector.add(current, p5.Vector.mult(perp, radius));
      let right = p5.Vector.add(current, p5.Vector.mult(perp, -radius));
      leftSide.push(left);
      rightSide.push(right);
    }

    let head = this.rope[0];
    let nnext = this.rope[1];
    let headRadius = this.constrainLengths[0];
    let angleHead = atan2(nnext.y - head.y, nnext.x - head.x) + PI;
    let angles = [PI/6, 0, -PI/6];
    let frontPoints = [];
    for (let a of angles){
      let rotatedAngle = angleHead + a;
      let x = head.x + cos(rotatedAngle) * headRadius;
      let y = head.y + sin(rotatedAngle) * headRadius;
      frontPoints.push(createVector(x,y));
    }
    let tmp = frontPoints[0]; frontPoints[0] = frontPoints[2]; frontPoints[2] = tmp;

    let contorno = [];
    for (let i=leftSide.length-1;i>=0;i--) contorno.push(leftSide[i]);
    for (let pnt of frontPoints) contorno.push(pnt);
    for (let i=0;i<rightSide.length;i++) contorno.push(rightSide[i]);

    let smoothContour = chaikinClosed(contorno, 2);
    let bodyCenter = createVector(0,0);
    for (let pt of smoothContour) bodyCenter.add(pt);
    bodyCenter.div(max(1, smoothContour.length));

    // fins behind (kept)
    drawFinAtContour_behind_instance(contorno, 5, 1.0, 0.9, 1.0, this.finColor, this.tailOsc * 0.2);
    drawFinAtContour_behind_instance(contorno, 25, 1.0, 0.9, 1.0, this.finColor, -this.tailOsc * 0.2);
    drawFinAtContour_behind_instance(contorno, 10, 1.1, 1.2, 1.2, this.finColor, this.tailOsc * 0.5);
    drawFinAtContour_behind_instance(contorno, 20, 1.1, 1.2, 1.2, this.finColor, -this.tailOsc * 0.5);

    // body
    fill(this.bodyColor);
    stroke(255);
    strokeWeight(2);
    beginShape();
    for (let pnt of smoothContour) vertex(pnt.x, pnt.y);
    endShape(CLOSE);

    // dorsal
    {
      let segStart = 5, segEnd = 8;
      segStart = max(0, min(segStart, this.rope.length-1));
      segEnd = max(0, min(segEnd, this.rope.length-1));
      if (segEnd <= segStart) segEnd = min(segStart+1, this.rope.length-1);
      let dorsalBase = [];
      for (let i=segStart;i<=segEnd;i++) dorsalBase.push(this.rope[i].copy());
      let smoothBase = chaikinOpen(dorsalBase, 2);
      let avgR = this.constrainLengths.reduce((a,b)=>a+b,0)/this.constrainLengths.length;
      let offsetTop = [];
      let totalCurvature = 0;
      for (let i = 2; i < this.boneAngles.length; i++) totalCurvature += normalizeAngle(this.boneAngles[i] - this.boneAngles[i - 1]);
      let curvatureInfluence = min(abs(totalCurvature) * 0.6, 2.0);
      for (let i=0;i<smoothBase.length;i++){
        let prev = smoothBase[max(0,i-1)];
        let next = smoothBase[min(smoothBase.length-1,i+1)];
        let tangent = p5.Vector.sub(next, prev);
        if (tangent.mag() < 0.0001) tangent = createVector(1,0);
        tangent.normalize();
        let normalVec = createVector(-tangent.y, tangent.x);
        normalVec.normalize();
        let tnorm = smoothBase.length > 1 ? i/(smoothBase.length-1) : 0.5;
        let taper = sin(tnorm * PI);
        let baseHeight = avgR * 0.22;
        let height = baseHeight * (1 + curvatureInfluence * 0.55) * (0.35 + 0.9 * taper);
        let top = p5.Vector.add(smoothBase[i], p5.Vector.mult(normalVec, height));
        offsetTop.push(top);
      }
      let dorsalContour = [];
      for (let pt of offsetTop) dorsalContour.push(pt.copy());
      for (let i = smoothBase.length - 1; i >= 0; i--) dorsalContour.push(smoothBase[i].copy());
      let dorsalSmooth = dorsalContour.length >= 4 ? chaikinClosed(dorsalContour, 1) : dorsalContour;
      push();
      noStroke();
      fill(this.finColor);
      beginShape();
      for (let pt of dorsalSmooth) vertex(pt.x, pt.y);
      endShape(CLOSE);
      pop();
      stroke(255); strokeWeight(2); noFill();
      beginShape();
      for (let pt of dorsalSmooth) vertex(pt.x, pt.y);
      endShape(CLOSE);
    }

    // tail
    {
      let segStart = this.rope.length - 6, segEnd = this.rope.length - 1;
      segStart = max(0, segStart);
      segEnd = max(segStart+1, segEnd);
      let tailBase = [];
      for (let i=segStart;i<=segEnd;i++) tailBase.push(this.rope[i].copy());
      let dir = p5.Vector.sub(tailBase[tailBase.length - 1], tailBase[tailBase.length - 2]);
      if (dir.mag() < 0.0001) dir = createVector(1,0);
      dir.normalize();
      dir.mult(35 * SIZE_SCALE);
      tailBase.push(p5.Vector.add(tailBase[tailBase.length - 1], dir));
      let smoothTailBase = chaikinOpen(tailBase, 2);
      let avgR = this.constrainLengths.reduce((a,b)=>a+b,0)/this.constrainLengths.length;
      let offsetLeft = [], offsetRight = [];
      for (let i=0;i<smoothTailBase.length;i++){
        let prev = smoothTailBase[max(0,i-1)];
        let next = smoothTailBase[min(smoothTailBase.length-1,i+1)];
        let tangent = p5.Vector.sub(next, prev);
        if (tangent.mag() < 0.0001) tangent = createVector(1,0);
        tangent.normalize();
        let normalVec = createVector(-tangent.y, tangent.x);
        let t = i / (smoothTailBase.length - 1);
        let taper = pow(sin(t * PI), 0.9);
        let height = avgR * 0.25 * (0.6 + 0.8 * taper);
        let oscillation = sin((frameCount + this.oscOffset) * 0.08 + i * 0.5) * this.tailOsc * 8;
        offsetLeft.push(p5.Vector.add(smoothTailBase[i], p5.Vector.mult(normalVec, height + oscillation)));
        offsetRight.push(p5.Vector.add(smoothTailBase[i], p5.Vector.mult(normalVec, -height - oscillation)));
      }
      let tailContour = [];
      for (let pt of offsetLeft) tailContour.push(pt.copy());
      for (let i=offsetRight.length-1;i>=0;i--) tailContour.push(offsetRight[i].copy());
      let tailSmooth = tailContour.length >= 4 ? chaikinClosed(tailContour, 3) : tailContour;
      let tailOsc = sin(frameCount * 0.1 + this.oscOffset) * PI / 90 * (1 + this.audioParams.sumNorm * 2);
      let pivot = this.rope[this.rope.length - 4].copy();
      push();
      translate(pivot.x, pivot.y);
      rotate(tailOsc);
      noStroke();
      fill(120, 180, 200);
      beginShape();
      for (let pt of tailSmooth) vertex(pt.x - pivot.x, pt.y - pivot.y);
      endShape(CLOSE);
      stroke(255); strokeWeight(2); noFill();
      beginShape();
      for (let pt of tailSmooth) vertex(pt.x - pivot.x, pt.y - pivot.y);
      endShape(CLOSE);
      pop();
    }

    // eyes
    drawEyeAtContour_internal_instance(contorno, 13, bodyCenter, 0.1, -PI/18);
    drawEyeAtContour_internal_instance(contorno, 17, bodyCenter, 0.1, PI/18);
  }
}



// ---------------- helpers (copied/kept from your example) ----------------
function drawEyeAtContour_internal_instance(contorno, idx, centerVec, offsetInteriorRatio, angleOffset = 0) {
  idx = clampIndex(idx, contorno.length);
  let pnt = contorno[idx];
  let baseRadius = estimateRadiusForContourIndex(idx, contorno.length);
  let towardCenter = p5.Vector.sub(centerVec, pnt);
  if (towardCenter.mag() < 0.0001) towardCenter = createVector(0, 1);
  towardCenter.normalize();
  towardCenter.rotate(angleOffset);
  let inset = baseRadius * (offsetInteriorRatio || 0.25);
  let pos = p5.Vector.add(pnt, p5.Vector.mult(towardCenter, inset));
  let eyeR = max(3, baseRadius * 0.18);
  noStroke();
  fill(255);
  circle(pos.x, pos.y, eyeR * 2);
}

function drawFinAtContour_behind_instance(contorno, idx, sizeMul, wMul, hMul, fillColor, rotExtra) {
  idx = clampIndex(idx, contorno.length);
  let pnt = contorno[idx];
  let prev = contorno[(idx - 2 + contorno.length) % contorno.length];
  let next = contorno[(idx + 2) % contorno.length];
  let tangent = p5.Vector.sub(next, prev);
  if (tangent.mag() < 0.0001) tangent = createVector(1, 0);
  tangent.normalize();
  let normalVec = createVector(-tangent.y, tangent.x);
  normalVec.normalize();
  let baseRadius = estimateRadiusForContourIndex(idx, contorno.length);
  let anchor = pnt.copy();
  anchor.add(p5.Vector.mult(normalVec, baseRadius * 0.28 * sizeMul));
  let w = max(6, baseRadius * wMul * 1.8);
  let h = max(6, baseRadius * hMul * 1.2);
  let baseAngle = atan2(normalVec.y, normalVec.x);
  let backAngle = baseAngle + PI * 0.5;
  let biasBack = 0.35;
  let angleTowardsBack = lerpAngle(baseAngle, backAngle, biasBack);
  let finalAngle = angleTowardsBack + (rotExtra || 0);
  push();
  translate(anchor.x, anchor.y);
  rotate(finalAngle);
  fill(fillColor);
  stroke(255);
  strokeWeight(2);
  ellipse(0, 0, w, h);
  pop();
}

function estimateRadiusForContourIndex(idx, contLen) {
  let n = CONSTRAIN_LENGTHS.length;
  let segIdx = 0;
  if (idx < n) segIdx = n - 1 - idx;
  else if (idx >= n && idx <= n + 2) segIdx = 0;
  else {
    segIdx = idx - (n + 3);
    if (segIdx < 0) segIdx = 0;
    if (segIdx >= n) segIdx = n - 1;
  }
  return CONSTRAIN_LENGTHS[max(0, min(segIdx, CONSTRAIN_LENGTHS.length - 1))];
}

function clampIndex(i, len) {
  return ((i % len) + len) % len;
}

function normalizeAngle(a) {
  while (a > PI) a -= TWO_PI;
  while (a < -PI) a += TWO_PI;
  return a;
}

function lerpAngle(a, b, t) {
  let diff = normalizeAngle(b - a);
  return a + diff * t;
}

function constrainDistance(point, anchor, targetLength) {
  let dir = p5.Vector.sub(point, anchor);
  let d = dir.mag();
  if (d < 0.0001) dir = createVector(1, 0);
  dir.normalize();
  return p5.Vector.add(anchor, p5.Vector.mult(dir, targetLength));
}

function chaikinClosed(points, iterations) {
  let result = points;
  for (let it = 0; it < iterations; it++) {
    let newPts = [];
    for (let i = 0; i < result.length; i++) {
      let p0 = result[i];
      let p1 = result[(i + 1) % result.length];
      let Q = p5.Vector.lerp(p0, p1, 0.25);
      let R = p5.Vector.lerp(p0, p1, 0.75);
      newPts.push(Q);
      newPts.push(R);
    }
    result = newPts;
  }
  return result;
}

function chaikinOpen(points, iterations) {
  let result = points;
  for (let it = 0; it < iterations; it++) {
    let newPts = [result[0]];
    for (let i = 0; i < result.length - 1; i++) {
      let p0 = result[i];
      let p1 = result[i + 1];
      let Q = p5.Vector.lerp(p0, p1, 0.25);
      let R = p5.Vector.lerp(p0, p1, 0.75);
      newPts.push(Q);
      newPts.push(R);
    }
    newPts.push(result[result.length - 1]);
    result = newPts;
  }
  return result;
}

function smoothstep(t) {
  t = constrain(t, 0, 1);
  return t * t * (3 - 2 * t);
}

function spectralCentroid(spectrum) {
  let num = 0;
  let den = 0;
  for (let i = 0; i < spectrum.length; i++) {
    let mag = spectrum[i];
    num += i * mag;
    den += mag;
  }
  if (den === 0) return width * 0.5;
  return (num / den) / spectrum.length * width;
}

function detectReverbLike(history) {
  if (!history || history.length < 10) return false;
  let last = history[history.length - 1];
  let prev = history[max(0, history.length - 5)];
  if (last > prev * 1.6 && last > 0.03) {
    let avg = history.reduce((a, b) => a + b, 0) / history.length;
    return last > avg * 1.25;
  }
  return false;
}

// stronger chromatic aberration snapshot (más intensa por defecto)
function applyChromaticAberration(rOffR = 34, rOffG = 24, rOffB = 16, alpha = 0.32) {
  let img = get();
  push();
  blendMode(ADD);
  tint(255, 0, 0, 255 * alpha);
  image(img, -rOffR, 0);
  tint(0, 255, 0, 255 * alpha);
  image(img, rOffG, -2);
  tint(0, 0, 255, 255 * alpha);
  image(img, rOffB, 2);
  pop();
  noTint();
}

// ---------------- ELECTRIC CHAINS ----------------
class ElectricChain {
  constructor(x, y, numPoints = 6, segmentLength = 40) {
    this.points = [];
    this.numPoints = numPoints;
    this.segmentLength = segmentLength;
    for (let i = 0; i < numPoints; i++) {
      this.points.push(createVector(x + random(-20, 20), y + i * segmentLength * 0.4));
    }
    this.noiseSeed = random(1000);
  }

  update() {
    this.noiseSeed += 0.02;
    for (let i = 1; i < this.points.length; i++) {
      let prev = this.points[i - 1];
      let cur = this.points[i];
      let nAng = noise(this.noiseSeed + i * 0.2, frameCount * 0.01) * TWO_PI * 2;
      let dir = p5.Vector.fromAngle(nAng);
      dir.mult(random(2, 6));
      cur.add(dir);
      // constrain distance like rope
      let toPrev = p5.Vector.sub(cur, prev);
      toPrev.setMag(this.segmentLength);
      cur.set(p5.Vector.add(prev, toPrev));
    }
  }

  draw(col, alpha) {
    noFill();
    strokeWeight(2);
    stroke(red(col), green(col), blue(col), 255 * alpha);
    beginShape();
    for (let p of this.points) vertex(p.x, p.y);
    endShape();
  }
}

class ElectricChainManager {
  constructor() {
    this.chains = [];
    this.active = false;
    this.alpha = 0;
    this.fadeSpeed = 0.05;
    this.heat = 0; // 0 -> blanco, 1 -> rojo
  }

  update() {
    // Fading logic
    let targetAlpha = this.active ? 1 : 0;
    this.alpha = lerp(this.alpha, targetAlpha, this.fadeSpeed);

    // Color heating (W key)
    let targetHeat = keyIsDown(87) ? 1 : 0; // 87 = W
    this.heat = lerp(this.heat, targetHeat, 0.1);

    // Update chains
    for (let c of this.chains) c.update();

    // Keep around 5-8 chains
    if (this.chains.length < 6 && this.active) {
      if (random() < 0.1) {
        this.chains.push(
          new ElectricChain(
            random(width * 0.2, width * 0.8),
            random(height * 0.3, height * 0.8),
            int(random(4, 8)),
            random(35, 60)
          )
        );
      }
    }
    // Fade out cleanup
    if (!this.active && this.alpha < 0.05) {
      this.chains = [];
    }
  }

  draw() {
    if (this.alpha <= 0.01) return;
    push();
    blendMode(ADD);
    let col = lerpColor(color(255, 255, 255), color(255, 40, 40), this.heat);
    for (let c of this.chains) c.draw(col, this.alpha * random(0.7, 1));
    pop();
    
    
  }
}

// ---------------- ELECTRIC PARTICLES ----------------
class ElectricParticle {
  constructor(x, y) {
    this.pos = createVector(x, y);
    this.vel = p5.Vector.random2D().mult(random(0.5, 2));
    this.size = random(5, 12);
  }

  update() {
    this.pos.add(this.vel);
    this.vel.add(p5.Vector.random2D().mult(0.2));
    this.vel.limit(2.5);

    // wrap around edges
    if (this.pos.x < 0) this.pos.x = width;
    if (this.pos.x > width) this.pos.x = 0;
    if (this.pos.y < 0) this.pos.y = height;
    if (this.pos.y > height) this.pos.y = 0;
  }

  draw(col, alpha) {
    noStroke();
    fill(red(col), green(col), blue(col), 255 * alpha);
    ellipse(this.pos.x, this.pos.y, this.size);
  }
}

class ElectricNetwork {
  constructor(num = 30) {
    this.particles = [];
    for (let i = 0; i < num; i++) {
      this.particles.push(new ElectricParticle(random(width), random(height)));
    }
    this.active = false;
    this.alpha = 0;
    this.fadeSpeed = 0.05;
    this.heat = 0; // 0 -> blanco, 1 -> rojo
  }

  update() {
    // Fade control
    let targetAlpha = this.active ? 1 : 0;
    this.alpha = lerp(this.alpha, targetAlpha, this.fadeSpeed);

    // Color heating (W key)
    let targetHeat = keyIsDown(87) ? 1 : 0; // 87 = W
    this.heat = lerp(this.heat, targetHeat, 0.1);

    for (let p of this.particles) p.update();
  }

  draw() {
    if (this.alpha <= 0.01) return;

    push();
    blendMode(ADD);
    let col = lerpColor(color(255, 255, 255), color(255, 50, 50), this.heat);

    // draw connecting lines
    stroke(red(col), green(col), blue(col), 100 * this.alpha);
    strokeWeight(1.5);
    for (let i = 0; i < this.particles.length; i++) {
      for (let j = i + 1; j < this.particles.length; j++) {
        let d = p5.Vector.dist(this.particles[i].pos, this.particles[j].pos);
        if (d < 120) {
          let a = map(d, 0, 120, 1, 0);
          stroke(red(col), green(col), blue(col), 200 * a * this.alpha);
          line(this.particles[i].pos.x, this.particles[i].pos.y,
               this.particles[j].pos.x, this.particles[j].pos.y);
        }
      }
    }

    // draw particles
    for (let p of this.particles) p.draw(col, this.alpha);
    pop();
  }
}


function keyPressed() {
  if (keyCode === 32) { // Space
    electricNetwork.active = true;
  }
}

function keyReleased() {
  if (keyCode === 32) {
    electricNetwork.active = false;
  }
}


// play/pause on mouse pressed (user interaction required to play audio)
function mousePressed() {
  if (song && songLoaded) {
    try {
      if (!song.isPlaying()) {
        song.loop();
        try { fft.setInput(song); amp.setInput(song); } catch(e){ console.warn('Reconectar fft/amp falló', e); }
      } else song.pause();
    } catch(e) {
      console.warn('Control playback falló', e);
    }
  } else {
    console.warn('No hay audio cargado para iniciar reproducción.');
  }
}

```

Un enlace a tu sketch en el editor de p5.js.

[Link](https://editor.p5js.org/ElJuanfe/sketches/2a2eowMDd)

Capturas de pantalla mostrando tu pieza en acción.

autoevaluacion
1.) 5
2.) 5
3.) 5
 
Total
5
 

