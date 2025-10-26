---
title: "The Beginnings: The Project That Never Starts"
date: 2025-10-17
draft: false
summary: "How it took me 7 years to start making my Game Boy game"
tags: ["indidev", "lifehacks", "offtopic", "gameboy"]
slug: "the-beginnings-the-project-that-never-starts"
translationKey: "devlog-001"
series: ["Choco Charlie"]
series_order: 1
---

For seven years, an idea had been haunting me — a Game Boy puzzle game where a little character would push blocks freely across the screen to form shapes.  
Why had no one done something like that before? It seemed so obvious to me that it would work. I was naïve back then — and I still am. That’s what brought me here. I hope I’ll always stay naïve.

The most obvious question one might ask after reading such a simple premise is: why did I become so obsessed with this idea? It wasn’t the first time I’d come up with a game concept. Like any creative person with dreams of making games someday, I had built up an endless list of unmade projects over the years — all sorts of ideas, some even simpler or more promising than this one.  
So what made this particular idea survive for so long and, against all odds, eventually become real?  
I think I know the answer, and I call it — somewhat ironically — **motivational synergy**.

Procrastination master? Maybe this idea will “help” you 🙂 (it won’t, actually).

Ever since I was a kid, I dreamed of making video games — but there was another dream intertwined with that one: making them for **retro consoles**. My first console was a **NES**, bundled with **Blue Shadow** (*Shadow of the Ninja* in the US), **Super Mario Bros. 3**, and **Mega Man 3**. Three masterpieces that left my five-year-old self completely mesmerized. I still haven’t recovered.

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
    <img src="/mario3.png" alt="Super Mario Bros 3" 
         style="width: 100%; height: 100%; object-fit: cover; image-rendering:pixelated;"/>
    <figcaption><em>Super Mario Bros. 3</em></figcaption>
  </figure>
  <figure style="
    flex: 1;
    max-width: 180px;
    aspect-ratio: 1 / 1;
    overflow: hidden;
    text-align: center;
  ">
    <img src="/megaman3.jpg" alt="Mega Man 3" 
         style="width: 100%; height: 100%; object-fit: cover; image-rendering:pixelated;"/>
    <figcaption><em>Mega Man 3</em></figcaption>
  </figure>
  <figure style="
    flex: 1;
    max-width: 180px;
    aspect-ratio: 1 / 1;
    overflow: hidden;
    text-align: center;
  ">
    <img src="/blueshadow.jpg" alt="Blue Shadow (Shadow of the Ninja)" 
         style="width: 100%; height: 100%; object-fit: cover; image-rendering:pixelated;"/>
    <figcaption><em>Blue Shadow (Shadow of the Ninja)</em></figcaption>
  </figure>
</div>

To this day I’m still hooked on retro gaming — especially the 8–16 bit era. That’s how, after seeing games like *Mr. Driller* for Game Boy Color, I told myself my idea **had to be possible** on that console. It was perfect for it: a simple concept with no complex controls that fit perfectly with the Game Boy’s philosophy.  
I dreamed of blending ideas from *Bomberman*, *Mr. Driller*, *Tetris Attack*, and even *Candy Crush*, yet somehow creating something that felt new, fresh, almost inevitable. That absurd mix, paired with the direct and minimalistic aesthetic of the Game Boy, made my imagination run wild.  

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
    <img src="/mrdriller.png" alt="Mr. Driller" 
         style="width: 100%; height: 100%; object-fit: cover; image-rendering:pixelated;"/>
    <figcaption><em>Mr. Driller</em></figcaption>
  </figure>
  <figure style="
    flex: 1;
    max-width: 180px;
    aspect-ratio: 1 / 1;
    overflow: hidden;
    text-align: center;
  ">
    <img src="/yoshiecookie.jpeg" alt="Yoshi's Cookie" 
         style="width: 100%; height: 100%; object-fit: cover; image-rendering:pixelated;"/>
    <figcaption><em>Yoshi’s Cookie</em></figcaption>
  </figure>
  <figure style="
    flex: 1;
    max-width: 180px;
    aspect-ratio: 1 / 1;
    overflow: hidden;
    text-align: center;
  ">
    <img src="/tetrisattack2.png" alt="Tetris Attack" 
         style="width: 100%; height: 100%; object-fit: cover; image-rendering:pixelated;"/>
    <figcaption><em>Tetris Attack</em></figcaption>
  </figure>
</div>

For some reason, I was convinced the game **could be done** on that system. I just knew it. And yet, the more I learned about the Game Boy’s inner workings, the harder it seemed — a huge wall stood between my idea and me. But instead of discouraging me, it only made the challenge more enticing.

That’s where the so-called synergy comes in. I have a pretty intense ADHD-type brain; I need an almost absurd level of motivation to move forward with something, even if I love it. And even then, I always find an excuse not to do it. Over time, I’ve learned to spot those rare situations where I can *hack myself* — use my own distractions as fuel. This was one of those.  
I could channel all that restless energy into **[learning more about the platform](https://github.com/gbdev/awesome-gbdev)**, exploring the **[Game Boy dev community](https://gbdev.io/chat.html)**, and absorbing its ecosystem. Meanwhile, I could keep procrastinating. Not make the game yet. Because I “wasn’t ready,” right?

I talked to a few people in the community and clumsily explained my idea. Most told me it was nearly impossible, that I should forget it and start with something simpler. Which, of course, made it irresistible.  
*Oh, it can’t be done? Hold my beer.*

But they were right. I started testing different engines: **[GBDK](https://github.com/gbdk-2020/gbdk-2020)**, **[ZGB Engine](https://github.com/Zal0/ZGB)** (back then **[GB Studio](https://www.gbstudio.dev/)** didn’t exist yet). I quickly realized none of them were built for what I had in mind.  
I won’t dive into technical details just yet, but in my head, the game involved tons of blocks moving freely, pushing each other smoothly across the board.

Yeah, not exactly trivial. If I wanted it to work, I’d have to go down to **[assembly language](https://rgbds.gbdev.io/)**.  
What a nightmare. I hadn’t touched assembly since college, and even though I knew I *could* handle it, my procrastination skills are legendary.  
So I went back to my old routine: read more docs, watch more examples, more videos, think up new engine strategies… and still write zero lines of code.

And you know what? In the end, my method *did* pay off — at least if you ignore the tiny detail that it took **seven years** to start. Nobody said it had to be fast 😂.  
But honestly, it’s not that it worked slowly — it just **doesn’t work**.  
The core of my so-called “motivational synergy” (also known as *systematic avoidance*) is that you never actually get around to executing the idea that sparked your will in the first place.  
And yet, that endless loop eventually broke. Not because of me, but because of life itself.

During that time, my career as a *full stack developer* was going great, riding the wave of the cross-platform app boom. Project after project, paycheck after paycheck, I fell deeper into a kind of *workaholism* that left me with no time for anything else.  
Until, at the beginning of this year (2025 — for whoever’s reading this in the glorious age of AI, which I sincerely doubt), the bubble burst. And with it, my job.

Almost simultaneously, something happened that now feels providential.  
One day, during yet another one of my routine research sessions on how to make my idea possible, I stumbled upon a demo called **[Canyon Racer](https://github.com/bbbbbr/canyon-racer)**.  
It was made in **GBDK** — yay, no assembly! — and most importantly, it contained the very trick that could make my dream possible: varying the scroll speed *mid-scanline*.  

Why was that so important? I’ll explain it in detail in future posts, but to sum it up: after years of mentally sketching solutions, I’d realized that this graphical trick was the one thing that could make my “impossible” game possible.

That had to be the sign I’d been waiting for. I was out of excuses. I had to give it a shot.

And that’s how my journey began. But before diving into the development process itself, I’d like to dedicate the next entry to explaining, in my own way, the graphical limitations of the Game Boy.  
It won’t be a technical manual, but a personal account of how I see that wonderful little machine.  

And if this story helps someone else take the plunge — hopefully faster than I did — then I’ll consider it a success.

---

Resources mentioned:  
[Awesome Game Boy](https://github.com/gbdev/awesome-gbdev) · [Gbdev Community](https://gbdev.io/chat.html) · [GBDK 2020](https://github.com/gbdk-2020/gbdk-2020) · **[RGBDS](https://rgbds.gbdev.io/)** · [ZGB Engine](https://github.com/Zal0/ZGB) · [GB Studio](https://www.gbstudio.dev/) · [Canyon Racer](https://github.com/bbbbbr/canyon-racer)