# Unidad 1

## 🤔 Fase: Reflect

### Actividad 09

#### Parte 1

Describe la diferencia fundamental entre la aleatoriedad generada por random() y la apariencia de aleatoriedad del Ruido Perlin (noise()). ¿En qué tipo de situación usarías cada una?

La principal diferencia entre ambos es que con random() generan números completamnete aleatorios, mientras que perlin agarra números cercanos entre sí, lo cual hace que resultados inmediatamente alejados del anterior no sean posibles.

Explica con tus palabras qué es una distribución de probabilidad. ¿Qué diferencia visual produce una caminata aleatoria con una distribución uniforme versus una con una distribución normal?

Una probabilidad es las posibilidades de ocurrencia para un suceso. Unaa distribución uniforme produce pasos de igual probabilidad, mientras que una normal crea trayectorias más agrupadas.

¿Cuál es el papel de la aleatoriedad en el arte generativo? Menciona al menos dos funciones distintas que cumple

Su papel es de poderle dar naturalidad a las creaciones, haciendo que se sienta más orgánica cada iteración.

Piensa en tu obra final (Actividad 08). Describe uno de los conceptos de aleatoriedad que usaste y explica por qué fue una elección adecuada para lograr el efecto que buscabas.

Uno de ellos fue perlin, debido a que quería hacer que el walker no se moviese de forma inorgánica, intenté usar levy, pero hacía movimientos antinaturales, por lo cual la más apropiada fue perlin.

¿Qué es un “paseo” o “caminata” (walk) en el contexto de la simulación? ¿Qué característica particular tiene una caminata de tipo “Lévy flight”?

Un walk es un proceso en el que un objeto se mueve paso a paso en direcciones aleatorias. En el contexto de vuelo de Levy la caminata toma ocasionalmente pasos largos y cortos, para recorrer sectores diferentes.

#### Parte 2: reflexión sobre tu proceso (Metacognición)

¿Cuál fue el concepto más abstracto o difícil de “visualizar” para ti en esta unidad? ¿Qué hiciste para finalmente comprenderlo?

En un inicio fue Levy, debido a que no comprendía la variabilidad de magnitudes de desplazamiento, pero fue gracias a consultar la guía The Nature of Code un poco y comprendí, pero nada fuera de lo ordinario, me interesó el tema y consulté de que sí sucede en ecosistemas de la vida real este patrón.

Describe un momento durante el desarrollo de tu obra final (Actividad 08) en el que un “error” o un resultado inesperado te llevó a una idea creativa interesante.

Por un momento el walker salía de la pantalla y no volvía por lo cual le hice un contenedor el cual fuese visible. Pero a parte de eso, no encontré usos curiosos a mis errores.

Al combinar diferentes técnicas de aleatoriedad, ¿Cuál fue el mayor desafío? ¿La interacción entre los sistemas, el control de los parámetros, el rendimiento?

Hacer principalmente que el Perlin no interfiriese con el walker fue problemático en gran medida, sobre todo para que no fuese extraños los cambios de movimiento.

Si tuvieras que empezar la Actividad 08 de nuevo, ¿Qué harías de manera diferente basándote en lo que sabes ahora?

Quisiera tratar de implementar un sistema de burbujas que salieran desde la base del canvas en vez de una serpiente.

### Atividad 10

Considero que la actividad de Isaac fue hermosa visualemtne, me encantó como aplicó los conceptos y que se ve llamativa, considero que un 5 es una nota más que adecuada para su calificación. COnsidero que su generación puede ser inspiradora, sobre todo con respecto a lo que mencioné de las burbujas que haría si volviese a hacer la Actividad 8.

[Generación de Isaac](https://github.com/jfUPB/simulacion-2025-20-isaacrisi/blob/unidad1/apply/unidad-1/apply.md)

### Actividad 11

#### Responde a las siguientes preguntas con total sinceridad. No hay respuestas correctas o incorrectas.

Continuar: ¿Qué actividad, ejemplo o explicación de esta unidad te resultó más reveladora o útil para tu aprendizaje? ¿Qué deberíamos mantener sí o sí?

Vuelo de Levy y Perlin por igual, bastante interesantes y útiles, además de permitir hacer conceptos bacanos.

Dejar de hacer: ¿Hubo alguna actividad o concepto que te pareció redundante, confuso o menos útil? ¿Hay algo que eliminarías o cambiarías por completo?

No, todo fue pertinente.

Empezar a hacer: ¿Qué te faltó en esta unidad? ¿Quizás más ejemplos de artistas, más desafíos técnicos, más teoría? ¿Qué idea tienes para hacerla mejor?

Me gustaría que hubiesen vídeos de múltiples usos de los conceptos, solo para inspirar la creatividad.

Balance Teoría-Práctica: ¿Cómo sentiste el equilibrio entre analizar los ejemplos del texto guía y ponerte a programar tus propios sketches? Explica tu respuesta.

Fue bien equilibrado, nad que añadir, excepto lo que mencioné de inspiraciones.

Comentario Adicional: ¿Hay algo más que quieras compartir sobre tu experiencia en esta unidad?

Bastante gratificante e intuitiva de por sí.
