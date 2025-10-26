---
title: "Empujar bloques es... complicado"
date: 2025-10-20
draft: false
summary: "Comprendiendo la arquitectura de Game Boy"
tags: ["indidev", "hardware", "history", "gameboy"]
slug: "arquitectura-de-gameboy"
translationKey: "devlog-003"
series: ["Choco Charlie"]
series_order: 3
---

Entonces, a ver, lo que nos interesa: yo quiero mover bloques.  
¿La Game Boy puede mover **tiles**, no? ¿Cuántos a la vez?  
La respuesta con trampa es: **“todos”**.  

Como ya hemos comentado, la Game Boy puede hacer **scroll** de su **tilemap** (background); de este modo, mover lo que se dice mover puede mover casi 4 veces todos los tiles que le caben por pantalla (todo su tilemap). Eso sí, esos tiles han tenido que ser cargados previamente en dicha parte de la memoria, y aquí reside la trampa: esta carga, aun siendo muy rápida para los estándares de la época (ya que no vuelve a recrear píxel por píxel, sino que va a buscar cada tile entero del **tileset** para copiarlo eficientemente), para los estándares actuales es bastante lenta.

Esto de “bastante lenta” es, en efecto, relativo, pero hay que tener en cuenta que la memoria de video no puede ser manipulada todo el tiempo, sino solo en un periodo concreto del ciclo de renderizado de la pantalla (**vblank** o **hblank**).  
La Game Boy tiene un ciclo de renderizado fijo de 60 frames por segundo; si en ese periodo no se han copiado todos los tiles necesarios, habrá que esperar otro frame entero para acabar la tarea.

![PPU states and timings when displaying a single frame. Source: The Ultimate Game Boy Talk (44:39)](ppu-timing-slide.png)
> Ciclo de renderizado de la **PPU** de Game Boy durante un frame. Fuente: *The Ultimate Game Boy Talk* (44:39)

Esto se traduce, por ejemplo, en que la mayoría de los juegos que cargan nuevas zonas en el **scroll**, ya sea vertical, horizontal o ambos, utilizan algoritmos para cargar pequeñas partes del **tilemap** con nuevos tiles durante el corto periodo que dura el **vblank**, y así dar la ilusión de nuevos gráficos, limitando también la velocidad de scroll efectiva del juego.  

De hecho, algo tan “sencillo” como lograr un scroll al pasar a una nueva zona, como en el *Zelda: Link’s Awakening*, no es trivial y se sirve de un pequeño tiempo de espera en cada transición que se disimula con efectos visuales.  
La historia de cómo los prototipos de Nintendo y otras compañías consiguieron el milagro de llevar juegos “tipo Zelda” a la máquina es súper interesante; os recomiendo echarle un ojo. No sería hasta mucho más adelante que juegos como *Donkey Kong Land* consiguieran la proeza de hacer scroll bidireccional en tiempo real.

Entonces, algo tan inocente como “quiero empujar cualquier cantidad de bloques en una cuadrícula a cualquier posición” se puede transformar rápidamente en una quimera.

Vamos afinando la pregunta: ¿de cuántos tiles **nuevos** estamos hablando? ¿Se requiere cambiar todo el tablero de juego a la vez?  
Si se está hablando de mover cualquier cantidad de tiles, aunque sea de una posición A a la adyacente B, tenemos un problema.  
Volvamos a repasar cómo funciona la Game Boy. (En realidad, va a ser la primera vez que voy a tratar de explicarlo "bien".)

La Game Boy tiene una resolución de pantalla de **160x144 píxeles**; esto equivale a **20x18 tiles**.  
Para poder dar a los juegos con scroll el suficiente margen de tiempo para incluir nuevos contenidos, los ingenieros pensaron (y pensaron bien) que con un canvas (tilemap) en memoria de **32x32 tiles**, algo así como un 1.5-1.8 del tamaño de la pantalla, sería suficiente.

![Tilemap and screen](tilemap.png)
> Ejemplo de la parte visible del tilemap dadas unas posiciones determinadas del scroll SCX SCY. Fuente: Game Boy Graphics & How To Code Them 2:58 (NesHacker)

El **tilemap** puede rellenarse con tiles provenientes de otra zona de memoria de la PPU llamada **tileset**. Este actúa como una biblioteca de donde se obtienen los tiles que se dibujan en el background (tilemap).  
Esta biblioteca no es tampoco ilimitada y tiene espacio para unos **384 tiles**, de los cuales, a efectos prácticos, se pueden usar unos **256** en un momento determinado.  
Esto también limita el margen de maniobra en este punto; si dispusiéramos de tiles “infinitos” con gráficos precalculados en distintas posiciones, podríamos intercambiar tiempo de procesado por memoria, algo muy común en algoritmos de rendimiento. Pero no nos adelantemos.

Como mucho, y sin hacer nada más, en cada frame da tiempo a copiar unos **80 nuevos tiles** del **tileset** al **tilemap**, pudiendo copiar incluso más si la copia está alineada en memoria, pero eso solo serviría en escenarios muy concretos.  
Volviendo a mi juego, eso resultaría en un movimiento de bloques bastante poco fluido por dos motivos:  
el primero es que los bloques se moverían en intervalos de **8 píxeles (1 tile)** y, además, para cubrir la mayoría de los casos sin que el juego sufriera ralentizaciones, habría que establecer un loop de renderizado virtual de 30 frames por segundo que usara paquetes de renderizado reales de 2 frames para duplicar el número de tiles que podríamos copiar, unos 160.

Otra posibilidad también muy válida sería atacar al **tileset** en vez del **tilemap**, o una combinación de ambas cosas.  
Cuando intercambias un tile del **tileset** por otro, esto afecta automáticamente a todas las copias de ese tile en el **tilemap**.  
De este modo, la idea sería aumentar la fluidez actualizando el **tileset** con una copia de cada uno de los bloques en una posición intermedia de empuje, ya sea acompañado de un hueco o de su bloque vecino en esa dirección (optimización con precálculo en memoria);  
aun así, en la práctica, eso solo haría que el movimiento fuera más fluido, pero también mucho más lento.  
Ya que la carga de gráficos en el **tileset**, a pesar de poder actualizar todos los tiles referenciados en el **tilemap** a la vez, es extremadamente lenta de por sí, pudiendo llegar solo a los **4 tiles por frame** alineados en memoria.

Por otro lado, tenemos los **sprites**, que no tienen todas estas limitaciones.  
Son pequeños objetos independientes de **8x8** o **8x16 píxeles** (puedes escoger uno u otro modo, no los dos a la vez) o, dicho de otro modo, (1 tile o bien 2 tiles, uno encima del otro en vertical).  
Estos objetos pueden no estar alineados con ningún tile del background, es decir, moverse independientemente del mismo en posiciones entre tiles, sin necesidad de manipular la memoria del **tileset**;  
y en cualquier cantidad sin apenas impacto en el rendimiento, la **PPU** se encarga de todo usando una memoria reservada para ello, la **OAM (Object Attribute Memory)**; y pudiendo copiar gráficos a ella de manera mucho más eficiente a través del **OAM DMA**.  
Como decía, la Game Boy está específicamente diseñada para permitir dar vida a estos objetos independientes.

![OAM DMA](dma_oam.png)
> La operación de volver a copiar todos los datos de los 40 sprites a la OAM usando DMA (Direct Memory Access) y, por lo tanto, modificar la posición de los mismos es muy rápida y se puede efectuar en un solo frame con mucho margen de tiempo. Fuente Game Boy Graphics & How To Code Them 5:58 (NesHacker)

Si quisiera mover un solo bloque o incluso 40 bloques a la vez (el número máximo de **sprites**), ¿podría hacerlo usando **sprites**? ¿No sería eso suficiente para crear mi juego?

Imaginemos, para este caso, que los **sprites** son de **8x8 píxeles**.  
Ponemos todos los **sprites** en pantalla para crear mi juego en un tablero cuadrado, por ejemplo, de **5x5**.  
Eso crearía un tablero chiquitito de **40x40 píxeles**, y con el resto de la pantalla, ¿qué hacemos? No mucho más, no nos quedan **sprites**; además, 5x5 parecen pocos bloques para mi juego.  

Algunos juegos de puzle para Game Boy usan bloques pequeños de 1 tile, como el *Tetris* o el *Dr. Mario*, pero el diseño de esos juegos parte de la idea de tener una pequeña parte móvil interactiva (que usa **sprites**) y luego un tablero establecido dibujado en el **tilemap**.

Además de la falta de atractivo visual, la solución de bloques pequeños no me permitiría ninguna flexibilidad en cuanto a mecánicas de juego; si necesitara algún **sprite** más para alguna animación, no lo iba a tener; en definitiva, muchos problemas y pocas ventajas.

La idea de juego que tenía en mi cabeza era algo más cercano a *Yoshi’s Cookie* o *Panel de Pon (Tetris Attack)*.  
En estos juegos se usan **metatiles**. Un **metatile** es un conjunto de tiles que representa una única estructura, ya sea un objeto/sprite o una parte reutilizable del fondo.  
Tanto en *Yoshi’s Cookie* como en *Panel de Pon* se utilizan **metatiles de 16x16 píxeles (2x2 tiles)** para dar más detalle y mejor visibilidad al juego.  
Eso implica que, ahora, para tener un tablero de **5x5**, ni siquiera con todos los **sprites** por pantalla me iba a ir de rositas; necesitaría 4 veces más **sprites**, o con el modo de **sprites de 8x16**, 2 veces más **sprites**, y ya hemos dicho que con **5x5** las mecánicas de juego quedarían limitadas.  
Yo quería mínimo un tablero de **7x7 bloques** grandotes, bonitos y vistosos, que se movieran fluidamente en cualquier dirección y en cualquier cantidad.  
Y lo quería ya, (Y una paella, también.)

En la siguiente entrada veremos cómo esa obstinación me llevó de visita por otras partes de la arquitectura de la Game Boy, intentando esquivar todas estas limitaciones y hacer mi juego posible de las maneras más rocambolescas, olvidando que estaba dejando de lado lo más importante en un desarrollo de software serio: ¡la simplicidad!

Recursos mencionados:  
[The Ultimate Game Boy Talk - 33c3](https://www.youtube.com/watch?v=HyzD8pNlpwI) · [Game Boy Graphics & How To Code Them - NesHacker](https://www.youtube.com/watch?v=F2AXJgsrs90)
