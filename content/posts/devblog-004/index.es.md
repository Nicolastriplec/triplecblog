---
title: "Buscando una salida, la ventana, los sprites y el scroll"
date: 2025-10-23
draft: false
summary: "De cómo me complique la vida aun más"
tags: ["indidev", "hardware", "history", "gameboy"]
slug: "gameboy-window-scroll"
translationKey: "devlog-004"
series: ["Choco Charlie"]
series_order: 4
---

Lo de mover **sprites** resultaba prometedor, pero de momento no funcionaba; necesitaba reducir el uso de **sprites** de algún modo.
Es entonces cuando, leyendo más sobre la arquitectura de Game Boy, me topé con algo prometedor, algo que los ingenieros habían dejado allí para salvar mi vida (en realidad, no): **la ventana**, un segundo **tilemap** alternativo.

La **ventana** parecía todo lo que había podido soñar. ¿Otra capa de gráficos que se mueve independientemente de los **sprites** y el fondo? ¿Y encima se puede colocar sobre la otra? 
De este modo podría empujar una parte de la pantalla independientemente del resto. Solo tenía que poner la ventana estratégicamente en una parte de mi tablero y, entre el scroll del fondo y los **sprites**… Malo será que no consiga moverlo todo por separado arbitrariamente.

Pero a medida que empezaba a aprender más sobre la **ventana**, me di cuenta de que esto no sería tan fácil. La **ventana** tiene dos limitaciones serias: la primera es que realmente no es una capa como tal; no tiene transparencias ni se puede elegir el orden en el que se dibuja —siempre va a tener prioridad respecto al **background**—; la segunda es que tampoco se puede colocar en cualquier lugar de la pantalla. 

![Window](gbwindow.png)
> Los juegos de Game Boy solían usar la ventana para el HUD y menús de pausa. Fuente: Game Boy Graphics & How To Code Them 2:17 (NesHacker)

La **ventana** realmente sobrescribe la parte de la pantalla donde está activa, y esa parte no puede ser cualquier parte: tiene que ser todo el espacio desde una cierta posición **X,Y** hasta el final de la pantalla de la Game Boy.
Esto hace imposible, a efectos prácticos, colocarla cubriendo solo la parte izquierda de la pantalla; sin embargo, manipulando los registros a medio frame, sí es posible hacer que cubra parcialmente regiones en sentido vertical, es decir, mostrarla solo en la parte superior o dejar un hueco entre la parte superior y la inferior.

Tomando todas estas limitaciones en cuenta, empecé a hacer experimentos (mentales) para ver cómo podía cubrir todos los casos de empuje de bloques.
Un consejo: no hagáis esto y mejor poneros a programar. Vais a llegar a una conclusión mucho más rápida.

Pero siguiendo con mi delirio, empecé a imaginar *corner cases* de lo más problemáticos. Por ejemplo: ¿qué pasaría si solo quisiera mover los bloques de la parte central del tablero, pero no los de los extremos en sentido horizontal? Bueno, en ese caso los **sprites** podrían volver al rescate. Pero ¿Y si solo quisiera mover uno o dos bloques? Entonces habría que hacer lo contrario, **sprites** móviles y fondo estático, claro, pero entonces…

Aquí me empezaba a explotar la cabeza. 
Si bien sería técnicamente posible cubrir la mayoría de los casos usando estratégicamente los tres recursos (**Sprites**, **Background** y **Ventana**), nadie me aseguraba que no hubiera algún *corner case* imposible de cubrir.
Y en el mejor de los casos, acabaría con un motor gráfico hecho a base de parches y excepciones imposible de mantener.

No… La cosa tenía que ser más sencilla, más consistente; necesitaba coherencia interna.
Una vez más, la máquina no parecía estar hecha para mi juego; me sentía como un niño tratando de construir una Game Boy con piezas de LEGO.

¿Estaba pidiendo demasiado? Quizás sí, pero había una idea que me había estado rondando por la cabeza desde hacía tiempo; tenía un plan: **el scroll parcial al rescate.**

Hay que detenerse un momento aquí para explicar otra vez cómo funciona el modo de renderizado de la **PPU** de Game Boy.
Los gráficos en Game Boy se dibujan línea por línea de píxeles, de arriba a abajo, tal como lo haría una **CRT** de la época.
Esto se hace, una vez más, para mantener el mismo esquema de desarrollo que en NES, imitando también el tiempo de espera entre líneas llamado **hblank** y el tiempo de espera tras el dibujado total de la pantalla, que ya hemos mencionado en el anterior post: **vblank**.

En cada uno de estos momentos, la consola lanza una interrupción para indicar que está procediendo a cada una de estas fases, tanto en **hblank** como en **vblank**. 
Esto es útil porque en esos periodos es cuando es seguro copiar nuevos **tiles** a la memoria de video de la consola, ya sea al **tilemap** o al **tileset**. 
Pero también se puede usar para crear efectos que generen cambios a mitad de pantalla, como cambiar la velocidad de **scroll** en una determinada *scanline* para generar efectos de **profundidad** o **parallax**.

![Parallax](parallaxgif.gif)
> Ejemplo: en BATMAN: Return Of The Joker para NES se usan estas técnicas midframe para crear un efecto parallax imponente

El caso es que mi juego, donde se mueven filas o columnas de bloques, se vería ampliamente beneficiado por un efecto así, pudiendo usar simplemente **scroll** en unas cuantas líneas en vez de tener que copiar nuevos **tiles** al background cada vez que el usuario arrastrara una columna.
El problema es que eso solo iba a servir para filas de bloques y no para columnas, porque la Game Boy no tiene modo de generar interrupciones en posiciones verticales arbitrarias en cada frame.

Aun así, en teoría sería posible sincronizarse con el tiempo de dibujado de cada una de las líneas para cambiar los valores del scroll en una posición determinada en el eje horizontal.
La documentación disponible hoy en día menciona de manera muy resumida esta posibilidad, pero deja muy claro que intentar manipular gráficos durante el dibujado de cada línea es **muy peligroso**.

Los [PanDocs](https://gbdev.io/pandocs/Rendering.html#ppu-modes) de Game Boy ofrecen además información detallada sobre el timing de la PPU e incluso contienen información de cómo varía el timing. Porque sí, amigos, aquí radica el problema: el tiempo de renderizado de cada línea por la PPU es variable, y ese es uno de los motivos principales de su dificultad a la hora de sincronizarse con él.

Me costó algún tiempo comprender que los registros que controlan el scroll no formaban parte de esta restricción; aun así, también se insistía en que intentar manipular esos registros en ese punto era **tremendamente complicado** e incluso **inestable**.
Más adelante comprendería el porqué, pero no nos adelantemos.

El caso es que, en este punto, aún seguía pensando en una **combinación de técnicas**.
Tenía pendiente hacer tests para ver hasta qué punto podía controlar totalmente los registros de **scroll**, pero entre que aquello había que hacerlo sí o sí en **ensamblador**, y que ni siquiera había creado mi primer *hello world* para la máquina en ese lenguaje, lo fui dejando un largo tiempo más.

Y así pasaron años sin ningún avance, hasta que llegaron los desencadenantes que comenté en mi primer post: mi despido y el hallazgo de ese código ([Canyon Racer](https://github.com/bbbbbr/canyon-racer)) que demostraba que sí era posible manipular un **scroll parcial horizontal**, aunque fuera en un contexto muy limitado.

En el siguiente post veremos cómo finalmente me lancé a programar, intentando en un principio solo calmar mi obsesión y ver si realmente era posible mi idea, pero terminando con una demo técnica de lo más resultona.

Recursos mencionados:  
[Game Boy Graphics & How To Code Them - NesHacker](https://www.youtube.com/watch?v=F2AXJgsrs90) · [PanDocs](https://gbdev.io/pandocs/Rendering.html#ppu-modes) · [Canyon Racer](https://github.com/bbbbbr/canyon-racer)