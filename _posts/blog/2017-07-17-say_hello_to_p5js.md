---
layout: post
title: Say Hello to p5js
description: "p5js provides way to create complex interactions compatible across devices with remarkable ease"
category: blog
comments: true
tags: "p5.js"
excerpt: ""
share: true
readtime: true
---


### An Intro

p5.js provides way to create complex interactions compatible across devices with remarkable ease. The features p5js brings with such simplicity that it can be used by people with little or no coding knowledge. I saw the [introductory video](http://hello.p5js.org/), presented by [Lauren McCarthy](http://lauren-mccarthy.com/about) and [Daniel Shiffman](http://shiffman.net/about/), and liked the style and narrative, the library was presented with. The video was interactive, floating shapes in the video could be played with, while the video was still playing and I was also excited just by watching the excited presenters-- by the way, Dan is always [excited](https://www.youtube.com/channel/UCvjgXvBlbQiydffZU7m1_aw).

Well, if that's not enough for your excitement, here are some more characteristics to get your attention:

- Speeds up the creation of animations on your site
- Shrinks site size by replacing videos with animations
- Allows low-cost prototyping of game designs
- Allows beginners to create complex interactions compatible across devices
- It's open source, [hosted on github](https://github.com/processing/p5.js), and is built by [165 contributors](https://github.com/processing/p5.js/graphs/contributors)
- It also has [forthcoming](https://github.com/processing/p5.js-web-editor) Web Editor

While there are libraries like Processing Java, Processing.js, D3.js, Easel.js, Three.js, Raphael.js, Kinetic.js, Paper.js, Famo.us js, Impact.js -  which are subset, super-set (if combined) or precursors of p5js, having similar features but none so easy.

I've been using D3 for over 4 years, many times, it has facilitated me transforming some complex data in a meaningful way and it has helped me plotting the points on screen in a visually appealing way. It's a wonderful library with great capacity, armed with equally great documentation. However, there's a cognitive overhead of thinking in selections and update patterns. The selections and `data()`, `enter()`, `exit()` does add some cognitive overhead. Though, having used for so many times, I've been able to get the hang of it and now I like it a lot; the initial learning curve was very steep.

Processing.js is slow because it tries to interpret plain processing code, which is Java, not JavaScript.
It can be difficult sometimes to understand how it works or how to fix things when it doesn't. How to modify or extend the library. As Processing.js says on their website, *it's not magic, but almost.*

Now p5js seems to be the answer, the thrust has been to use native JavaScript in a transparent, intuitive way, in compliance with the [Processing](https://processing.org/)'s original goals. p5js extends beyond canvas drawing to allow people to create, access and manipulate other HTML elements. Moreover, it's beginner friendly and being in JavaScript, leveraging it's power, large ecosystem and huge community. You can use this library for:

- Drawing Shapes
- Creating Elements
- Mouse Interactions
- Simple Animations
- Fetching API Data
- Generating Sound

To whet your appetite, here are the full set of [examples](https://p5js.org/examples/).

There are many applications where you could put p5js to work, e.g. making interactive versions of the types of figures you'd normally find in textbooks, and there's no one good library for this kind of thing. Each case is unique and has a large illustrative element, p5.js just allows you to get stuff done. Processing, from which p5js takes its inspiration, has an explicit goal of being beginner friendly. I think, simplicity is the reason, Processing made a splash in the artistic community too.

p5js defaults to either canvas or WebGL and uses abstractions, barely adding any overhead to raw JS. Where sensible it uses typed arrays [internally](https://p5js.org/reference/#/p5/pixels).

On comparing notes, D3 uses SVG and DOM manipulation by default, and everything is trees of objects, consequently, creating data visualization is also done in an entirely different style. p5js uses a draw-loop for each frame, whereas d3 uses an enter/update/exit paradigm.

So unless you manually take steps to use the canvas (which is possible, but not the default), I doubt that d3 usually beats p5. But really, if you know how to write fast JS code, you can write fast p5 code.
Remember, the [examples](https://p5js.org/examples/)  presented are not necessarily the fastest possible code, since they are more about showing code examples.


p5js is an awesome library, If you're looking for ideas to play with, checkout [Daniel Shiffman](http://shiffman.net/about/)'s Youtube Channel - [Coding Train](https://www.youtube.com/user/shiffman), a series in which he build ML libraries, games and lots of fun mini-projects. The channel has plenty of content teaching programming, visualization and graphics. Earlier the channel was named *Coding Rainbow* but now its *Coding Train*.

Besides the Youtube channel, his books and website also contain great introductions/tutorials to general programming, creative programming, natural language analysis, machine learning and a whole bunch of other topics. He has the ability to make technical concepts and projects interesting, non-scary for beginners and non-math background people. He aims at encouraging *creative* people to explore and gain an exposure to programming. He's also ported the examples in the book [The Nature of Code](http://natureofcode.com/) from Processing to [p5js](https://github.com/shiffman/The-Nature-of-Code-Examples-p5.js)


## Praise to Hello, the first impressions was impressive

[This](http://hello.p5js.org/) is one of a novel way to introduce a library that I've seen in a long time and also very impressive. I'm tired of traditional textual approach:

```<x library> is a <word I don't know>```
```for <task I'm not aware of>```
```creating <word soup>, it's that easy!```

 Honestly, I think it's great way of showing what it can do as there were parts of the video that were interactive. The point is to show that p5js allows you to do interactive visual applications and an introductory video, showing that you can do that, is rather interesting. I checked out [this editor](http://hello.processing.org/editor/) and it is in the same spirit, this is how all instruction should be. There is no point in learning if doing is saved for a possible future.


## Word of Caution

Seems I put heaves of praise on p5, but everything has a flip side. Unfortunately, accessibility is going to be a concern. Rendering all the text on a canvas, even the links, makes it inaccessible to many people living with disability and since the learning domain is one of the playgrounds for this library, it won't cut the cake very nicely, Because any library or practice that is to be adopted by Schools or Universities has to consider the factors like *Will someone from having minor sight issues to not able to see at all, be able to use this?* Someone with minor hearing issues to someone not able to listen at all, all are users.

Then again, we have seen technologies standing on the shoulders of time, have a knack of overcoming limitations. Let's see if the excitement frizzles out or gains more momentum and spread.

------
