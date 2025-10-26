---
title: "Pushing Blocks Is... Complicated"
date: 2025-10-20
draft: false
summary: "Understanding the Game Boy’s architecture"
tags: ["indiedev", "hardware", "history", "gameboy"]
slug: "gameboy-architecture"
translationKey: "devlog-003"
series: ["Choco Charlie"]
series_order: 3
---

Alright then — here’s what really matters: I want to move blocks.  
The Game Boy can move tiles, right? So... how many at once?  
The trick answer is: *“all of them.”*  

As we’ve already discussed, the Game Boy can scroll its tilemap (the background). That means it can technically “move” almost four times as many tiles as are visible on screen — essentially the entire tilemap. But here’s the catch: those tiles must already be loaded into that section of memory. And this is where the trick lies — the loading process, while extremely fast by the standards of its time (since it copies entire tiles rather than individual pixels), is rather slow by modern standards.

“Slow” is, of course, relative — but you have to remember that the video memory can’t be manipulated all the time. It can only be written to during very specific periods in the rendering cycle: **vblank** or **hblank**.  
The Game Boy runs at a fixed 60 frames per second. If you don’t finish copying all the necessary tiles during that window, you’ll have to wait for the next frame to complete the task.

![PPU states and timings when displaying a single frame. Source: The Ultimate Game Boy Talk (44:39)](ppu-timing-slide.png)
> Game Boy PPU rendering cycle for a single frame. Source: *The Ultimate Game Boy Talk* (44:39)

This is why most games that load new map areas — whether the scroll is vertical, horizontal, or both — use algorithms that load **small parts** of the tilemap during the brief vblank period, creating the illusion of new graphics while keeping the effective scrolling speed limited.  

Even something as “simple” as a screen transition in *The Legend of Zelda: Link’s Awakening* isn’t trivial. It uses short pauses disguised with visual effects to mask loading time. The story of how Nintendo and other studios managed to pull off “Zelda-style” games on the Game Boy is fascinating — definitely worth a read. It wouldn’t be until much later that games like *Donkey Kong Land* achieved the holy grail: true bidirectional scrolling in real time.

So yeah, something as innocent as *“I want to push any number of blocks freely on a grid”* can quickly turn into a pipe dream.

Let’s refine the question: how many **new tiles** are we talking about? Do we need to update the entire game board at once?  
If we’re talking about moving *any number* of tiles — even from one cell (A) to its neighbor (B) — we already have a problem. So let’s go back and review how the Game Boy actually works.  
(Well, this will actually be the first time I try to explain it *properly*.)

The Game Boy’s screen resolution is **160×144 pixels**, which equals **20×18 tiles**. To give scrolling games some breathing room for loading new data, the engineers decided (wisely) that a tilemap of **32×32 tiles** — about 1.5–1.8 times the visible screen area — would be enough.

![Tilemap and screen](tilemap.png)
> Example of the visible part of the tilemap given specific SCX SCY scroll positions. Source: Game Boy Graphics & How To Code Them 2:58 (NesHacker)

The tilemap can be filled with tiles from another section of PPU memory called the **tileset**. Think of it as a library where tiles are stored before being drawn on the background.  
This library isn’t infinite either — it can hold about **384 tiles**, though only around **256** are usable at a given time. This limits our flexibility: if we had an “infinite” tileset with precomputed tiles for every position, we could trade CPU time for memory — a classic performance trick. But let’s not get ahead of ourselves.

In practice, the Game Boy can copy about **80 new tiles per frame** from the tileset to the tilemap — maybe a bit more if memory alignment is perfect, but that only helps in specific cases.  
Back to my game: that would make block movement feel very choppy for two reasons.  
First, blocks would only move in **8-pixel steps** (1 tile).  
And second, to cover most cases without slowdown, I’d need to run a **virtual 30 FPS render loop** that uses real 2-frame render batches to double the number of tiles copied per logical frame — around **160 tiles**.

Another valid option would be to target the **tileset** instead of the tilemap — or both. When you swap one tile in the tileset for another, it instantly affects every instance of that tile on the map.  
So the idea would be to smooth movement by precomputing intermediate push positions for each block — maybe including the empty space or its neighbor, depending on direction. That’s an optimization by *precalculation in memory*.  
In practice, though, it only makes movement smoother while also much slower, since updating the tileset itself is extremely slow. Even at its best, it can only update about **4 tiles per frame**, assuming aligned memory.

On the other hand, we have **sprites** — which don’t suffer from these tilemap limitations.  
Sprites are small independent objects, **8×8** or **8×16 pixels** (you can pick one mode or the other, but not both). In other words, one or two stacked tiles.  
Sprites can be positioned anywhere on screen — they don’t need to align to the background grid — and they can move freely between tile positions without touching tile memory. You can also have plenty of them on screen with very little performance cost, since the **PPU** handles them automatically using a reserved area called **OAM (Object Attribute Memory)**. Sprites can be updated efficiently via **OAM DMA** transfers. In short, the Game Boy was specifically designed to bring these little independent objects to life.

![OAM DMA](dma_oam.png)
> Copying all the data from the 40 sprites back to the OAM using DMA (Direct Memory Access) and thus modifying their position is very fast and can be done in a single frame, allowing plenty of time. Source: Game Boy Graphics & How To Code Them 5:58 (NesHacker)

So, back to my obsession with pushing blocks:  
Imagine a *Zelda*-like scenario. If I wanted to move a single block — or even 40 blocks at once (the maximum number of sprites) — could I use sprites for that? Wouldn’t that be enough for my game?

The answer is no.

Let’s imagine sprites are 8×8 pixels. If I fill the screen with them to create my playfield — say, a 5×5 grid — that gives me a tiny **40×40-pixel** board. And then what? I’ve run out of sprites, and I still have the rest of the screen to fill!  
Besides, 5×5 blocks would make for a very small, limited puzzle game.  

Some puzzle games like *Tetris* or *Dr. Mario* use single-tile blocks, but those designs are built around a small active area (the falling pieces) rendered as sprites, while the rest of the board is a static tilemap background.

Apart from the lack of visual appeal, small sprite blocks wouldn’t give me much flexibility for gameplay mechanics. If I needed even one extra sprite for an animation — too bad. In short: lots of problems, few advantages.

The kind of game I had in mind was closer to *Yoshi’s Cookie* or *Panel de Pon (Tetris Attack)*.  
Those games use **metatiles** — groups of tiles that form a single visual element, like a reusable part of the background or a multi-tile sprite. Both *Yoshi’s Cookie* and *Panel de Pon* use 16×16-pixel (2×2 tile) metatiles for better detail and visibility.  
That means to create even a 5×5 board, I’d already be in trouble — I’d need **four times** as many sprites (or double that if using 8×16 mode). And as we said, even a 5×5 grid limits the gameplay.  
What I really wanted was at least a **7×7 grid** of big, chunky, colorful blocks — moving smoothly in any direction, in any number. And I wanted it *now*. (And maybe a paella, too.)

In the next entry, we’ll see how that stubbornness led me to explore other corners of the Game Boy’s architecture — bending every rule I could to make my game possible in the most convoluted ways imaginable, while conveniently forgetting the golden rule of serious software development: **keep it simple!**

Resources mentioned:  
[The Ultimate Game Boy Talk - 33c3](https://www.youtube.com/watch?v=HyzD8pNlpwI) · [Game Boy Graphics & How To Code Them - NesHacker](https://www.youtube.com/watch?v=F2AXJgsrs90)