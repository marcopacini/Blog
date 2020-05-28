---
title: "Go, Genetic Algorithm and Arts"
date: 2018-11-11T21:00:00+02:00
categories: [Computer]
tags: [Go, Genetic Algorithm, Arts]
---

![Monna Lisa](/img/monna-lisa.gif#center)

In the last years every time I decided to learn a new programming language I have
re-implemented an old project that mixes *genetic algorithms* with arts. This time
is the **Go** turn.

First of all I want to say that this idea is not mine. Full credit goes to [Roger
Alsing](https://rogerjohansson.blog/2008/12/07/genetic-programming-evolution-of-mona-lisa/)
for using genetic algorithms to approximate fine art.

There are different approaches out there for replicating images using genetic
algorithms. This time I decided to create a tiny package called `go-genetic` for
dealing with different aspects of genetic algorithms.
However you can achieve a similar result with a simpler implementation, in fact
the original idea used a population of just one individual competing with its
single offspring.

So why bother implementing another genetic algorithms library? Because my goal is
to learn Go getting my hands dirty and thus I wanted to tackle the complex task for
squeezing Go features. In this article I'm going to show you how to use my
package to produce (copy) artworks.

Before starting to write code I want to describe the main components of
`go-genetic`.

The core component is the `Gene` struct that wraps a sequence of float values.
A group of `Gene` compose the `Chromosome` struct and a `Chromosome` with its
fitness value compose a `Phenotype` struct. To solve a problem you
have to encode a solution using a `Chromosome` struct and provide a function to establish
how good is the solution (calculate its fitness value). So turning back to
mixing genetic algorithm and arts I want to replicate the famous Gioconda painting
using a finite number of circles (usually polygons are used). We can define a
circle using exactly 7 values: four values for the RGBA components, two for
the center coordinates and one for radius. Then we map a `Gene` struct with a
circle and a `Chromosome` struct with a group of circles: a solution.

Supposing to have a `Chromosome` instance with the specifics above how can we
*convert* it to an image? We can split the problem in two parts: first is
convert a **gene** to a circle and second one is convert a **chromosome** to an
image. Let's start with the last one.

First of all just for convenience we define the `Picture` struct that encapsulate
a `Chromosome`. Then we can define a method for `Picture` that takes three arguments,
respectively *width*, *height* and *background color* of the image where
we draw over the **chromosome**s. The **Draw** method just create an *empty* image
and iterate over every **gene** (aka circle) calling the function **DrawCircle** that
convert the **gene** s' values in circle properties.

<script src="https://gist.github.com/marcopacini/afb1b377e78cb21cbd694f4a85fdbd03.js"></script>

Before the next step, the fitness function, let me spend few words how
to convert a float value in interval `[0-1]` to custom interval. Suppose we have
the value `0.88` that represents the red component of a `RGBA` variable. Because
`RGBA` components are defined as an integer interval from `0` to `256` we can just
multiply `0.88` for `256`. However the general rule to convert a float `x` to the
interval `[lower,upper]` is `lower + (x * (upper - lower))`.

Now we can proceed to implement the fitness function, a method that, given two images,
returns a score that describes (gives an estimate metric) the difference. We can obtain this result
comparing every pixel components by components.
It's common knowledge in programming that reinventing the wheel is bad or *evil*
(I don't always agree) so I started by picking a functional
[implementation](https://rosettacode.org/wiki/Percentage_difference_between_images#Go)
from the Rosetta Code project. However, I did change the number of pixel iterations, also inserting a
bit of parallelization for speeding up computation time because with increasing (spatial) resolution this
function becomes the main bottleneck to the algorithm performance. Further I change difference evaluation
between pixel values replacing absolute distance with squared distance too.

Now we have all ingredients for starting the "shape" evolution and get to our artworks. Next step is to setup
an evolution configuration and finally lunch it. We can do this using the struct
`Configuration`:

<script src="https://gist.github.com/marcopacini/5c8bc9a959c3e792d15f32f57d7acc93.js"></script>

Now if you want to run the code above you can download `go-genetic` from
[GitHub](http://github.com) and run `example/image.go`.

After some attempts I discovered a better strategy for getting better results:
dynamic number of shapes. Until now I used a fixed number of circles but I
decided to start with just one and let it grows according to its needs. To achieve this behavior we need
another gene for every chromosome that indicates if it will be drawn. Then
we need to initialize this gene for every chromosome except one. This is can be
done passing a function when configuration struct is set. This is the final result
using 500 circles:

![Monna Lisa](/img/monna-lisa.png#center)

For any question you can send me an email. ;-)

P.S. `go-genetic` is not thought for production code, it is just a learning
projects so if you have any suggestion or correction would be appreciated.
