---
title: "Go, draw2d and antialiasing"
date: 2018-09-12T12:13:30+02:00
categories: [Computer]
tags: [Go, draw2d]
---

![Goopher](/img/goopher-pixel-art.png#center)

**tl;dr** If you don't want waste your time reading how I have lost a few hours with your same problem **just replace `RGBA` colour type with `NRGBA`!**

Several days ago I decided to start learning Go. After skimming through [Introduction to Go](https://www.golang-book.com/books/intro) (I suggest it) I got my hands dirty implementing my custom *"Hello World"* program: a tiny genetic algorithm that draw (ehm... it just copies) the famous Gioconda painting.

After a quick search on the web I picked **[draw2d](https://github.com/llgcode/draw2d)** package for drawing. First of all I tried to draw some basic shapes but I suddenly got in trouble because my shapes had terrible antialiasing. The problem was mainly related to some colours with low alpha value. I spent a few hours searching without success (for a solution) until I decided to looking for (some hints) where someone had probably already reported the same error: the "Issue" section on GitHub project.

I found that Google [reported](https://github.com/llgcode/draw2d/issues/12) the same problem on 2015 and there was already a solution: using `NRGBA` colour instead of `RGBA`. `NRGBA` differs from `RGBA` for the alpha component representation: straight vs pre-multiplied. If you want to understand better what this difference means I suggest you to read wikipedia article about [alpha composing](https://en.wikipedia.org/wiki/Alpha_compositing)
