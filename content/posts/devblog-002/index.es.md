---
title: "Game Boy, la NES portátil perfecta"
date: 2025-10-17
draft: false
summary: "Hacer juegos tipo puzzle en Game Boy, es difícil"
tags: ["indidev", "hardware", "history", "gameboy"]
slug: "los-graficos-en-gameboy"
translationKey: "devlog-002"
series: ["Choco Charlie"]
series_order: 2
---

En términos de arquitectura, la Game Boy es una máquina mucho más avanzada que la Nintendo NES. A pesar de ser una consola portátil en algunos aspectos menos potente por estar centrada también en la eficiencia energética, muchas de las cosas aprendidas en NES se trasladaron perfeccionadas a Game Boy, manteniendo al mismo tiempo los mismos esquemas de desarrollo, todo ello de manera premeditada, entre otras cosas, para facilitar el trabajo a los desarrolladores que habían evolucionado de la mano de la arquitectura de NES para formar parte de la nueva era del videojuego moderno.

Al contrario de otras máquinas anteriores más parecidas a los microordenadores, la arquitectura de gráficos de Game Boy contaba, como en NES, con su propia memoria de video reservada (llamada vram), totalmente enfocada en alojar tiles (paquetes gráficos de 8x8 píxeles). Bajo este tipo de esquema, la consola era capaz de crear juegos tan ricos y variados como Link's Awakening o Kirby's Dream Land con modos de scroll súper fluidos. Junto a ese fondo con scroll fino, la consola también tenía una parte reservada a los actores o sprites, representando al jugador, enemigos y demás elementos interactivos. E incluso un fondo alternativo pensado para albergar el HUD, facilitando así muchísimo el trabajo de los equipos de desarrollo.

<div style="
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 12px;
  flex-wrap: nowrap;
  overflow-x: auto;
">
  <figure style="
    flex: 1;
    max-width: 180px;
    aspect-ratio: 1 / 1;
    overflow: hidden;
    text-align: center;
  ">
    <img src="/gameboypputileset.png" alt="Tileset" 
         style="width: 100%; height: 100%; object-fit: cover; image-rendering:pixelated;"/>
    <figcaption><em>El tileset constituye los bloques de construcción de los gráficos</em></figcaption>
  </figure>
  <figure style="
    flex: 1;
    max-width: 180px;
    aspect-ratio: 1 / 1;
    overflow: hidden;
    text-align: center;
  ">
    <img src="/gameboyppubackground.png" alt="Tilemap" 
         style="width: 100%; height: 100%; object-fit: cover; image-rendering:pixelated;"/>
    <figcaption><em>La ppu reordena los tiles en el tilemap para formar los fondos</em></figcaption>
  </figure>
  <figure style="
    flex: 1;
    max-width: 180px;
    aspect-ratio: 1 / 1;
    overflow: hidden;
    text-align: center;
  ">
    <img src="/gameboyppuobjects.png" alt="Sprites" 
         style="width: 100%; height: 100%; object-fit: cover; image-rendering:pixelated;"/>
    <figcaption><em>La ppu también puede usar su tileset para incluir sprites</em></figcaption>
  </figure>
</div>

Además, la consola contaba con otra baza a su favor respecto a NES. Su baja resolución hacía que el número máximo de sprites por pantalla (40, 10 por linea), ligeramente menor que el de NES (64, 8 por linea), fuera comparativamente mucho mayor en términos de espacio en pantalla.

También contaba con mejoras de calidad de vida, como la capacidad de tener un control mucho más fino del render de video horizontal, lo que hacía que trucos gráficos que requerían mucho trabajo en NES, como fondos con efecto parallax, fueran casi features que la flamante nueva portátil de Nintendo podía efectuar sin despeinarse.

En definitiva, la consola era como una NES portátil, con menos potencia bruta en algunos aspectos, pero totalmente optimizada para realizar el tipo de juegos que estaban de moda en la época. Es por eso que los programadores de Game Boy desde el principio consiguieron hacer que pareciera fácil lo que en realidad había sido difícil en los días iniciales de NES. Llegando hacia el final de la vida de la consola a crear auténticas obras de arte de ingeniería de 8 bits.

Creo que fue eso lo que me llevó a pensar que mi idea de juego era posible; veía a juegos como Yoshie Cookie y Tetris Attack (o Panel de Pon) hacer cosas que parecían similares a las que yo quería hacer. Lo que no sabía es que esos juegos estaban llenos de trucos gráficos y aprovechaban la consola al 100% a pesar de parecer juegos de puzzles inofensivos, llevando algunas de las funcionalidades de la consola más allá de juegos que parecen mucho más ambiciosos, como los Pokémon o los Zelda.

Los juegos de puzzles nunca han sido tan populares como tras el lanzamiento de Tetris para Game Boy en 1989-1990, algo que, a mi modo de ver, también pilló a Nintendo a contra pie. Llegando incluso a crear en muy poco tiempo la franquicia Doctor Mario como un intento de competir con la fiebre tetrominica, o al menos tratar de marcarse otro pelotazo a ese nivel.

Al venir de máquinas mucho más antiguas y menos potentes, costó relativamente poco portear Tetris a la máquina, lo cual no quiere decir que Game Boy estuviera diseñada para este tipo de juegos.

Aun así, el mercado mandaba y la gente quería más puzzles para Game Boy; es por ello que podemos ver obras de ingeniería como las que he nombrado, que desafían en cierto modo la intención principal de la arquitectura de la máquina y, por otra parte, también están diseñados en torno a las limitaciones de esta con las lecciones aprendidas del mismo port de Tetris. Os dejo por aquí un maravilloso tutorial de cómo crear un juego de Tetris para Game Boy con GBDK de la mano de [Larold’s Retro Gameyard](https://laroldsretrogameyard.com/tutorials/gb/how-to-make-tetris-for-the-nintendo-gameboy/) con un diseño similar al que usaron en la época.

 ![Tetris](/tetrisex.png)

Total, que me había escogido un género difícil para desarrollar mi Homebrew, y por si fuera poco, la documentación y los ejemplos que hoy día puedes encontrar en internet, en su mayoría, se centran en juegos de esos géneros “fáciles”, rpgs o Action rpgs tipo Zelda o Pokémon, así como juegos de plataformas tipo Mario Land. Juegos para los que la plataforma fue diseñada en un principio, existiendo en la actualidad incluso motores centrados en facilitar mucho el desarrollo de este tipo de juegos, como Zgb o Gb Studio.

En este punto, un ingeniero sensato hubiera pensado algo así como: “esto no cabe aquí” y hubiera seguido con su vida, o al menos hubiera repensado su idea conforme a las limitaciones aprendidas, pero yo no soy un ingeniero sensato; para no ser ingeniero, no soy ni ingeniero.

Seguidme en esta loca aventura en la siguiente entrada del blog donde trataremos de hacer mi juego posible en Game Boy una vez más.

Recursos mencionados:  
[Larold’s Retro Gameyard](https://laroldsretrogameyard.com/tutorials/gb/how-to-make-tetris-for-the-nintendo-gameboy/) · [ZGB Engine](https://github.com/Zal0/ZGB) · [GB Studio](https://www.gbstudio.dev/)