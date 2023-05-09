---
layout: post
title: "Lines, Pipelines and Presentations"
categories: alloy
---

It's never long before a new Alloy user wants to put their atoms in a line. I found a question on Stack Overflow where a  user wants to [model a presentation as a sequence of slides](https://stackoverflow.com/questions/75799390/modeling-a-presentation-with-ordered-slides). And [here](https://www.youtube.com/watch?v=XaCNBpCSQVk) is a recording of a collaborative modelling session where the developers are exploring their pipeline design.

I was no different. One of the first things I tried with Alloy was modelling a linked list, and I could have done a better job. I'd love to show the mess I made, but I can no longer find it.

I want to show you how I make "straight lines" in Alloy.

## Straight Lines

Of course, I am being informal when I say I will put my atoms in a straight line. The following model explains what I mean.

```alloy
open relmath/order
open relmath/function

sig V { R: set V }

pred line(V: set univ, R: V->V) {
  univalent[V,V,R]
  linear_strict_order[V,^R]
}

run { line[V,R] } for 5
```

It says a relation is a "line" when it is *univalent* and its transitive closure is a *linear strict order*.  I keep those definitions in my [relmath repo](https://github.com/coreflexive/relmath).

## Presentations

Suppose we want to model a presentation as a "line" of slides.  Then we can write:

```alloy
open relmath/function
open relmath/order

sig Presentation {
  Sld: set Slide,
  Nxt: Sld -> Sld
}

sig Slide {}

pred line(V: set univ, R: V->V) {
  univalent[V,V,R]
  linear_strict_order[V,^R]
}

pred presentation(p: Presentation) {
  line[p.Sld,p.Nxt]
}

run { all p: Presentation | presentation[p] } for 3
```

What about pipelines?

## Pipelines

Here's a model that I think captures the essence of the video's pipeline:

```alloy
open relmath/order
open relmath/function

sig Pipeline {
  , Stg: set Stage
  , Nxt: Stg -> Stg
}

sig Stage {
  , consume
  , produce: Format
}

sig Format {}

pred pipeline(p: Pipeline) {
  coherent[p]
  line[p.Stg,p.Nxt]
}

pred coherent(p: Pipeline) {
  p.Nxt in produce.~consume
}

pred line(V: set univ, R: V->V) {
  univalent[V,V,R]
  linear_strict_order[V,^R]
}

run { all p: Pipeline | pipeline[p] } for 3
```

Simply put, a pipeline is a "coherent line".  It is "coherent" in the sense that adjacent stages agree on the format they share.

## No Sharing!

Both modellers mentioned another requirement that I still need to address.  Neither of them wants their slides/stages shared between presentations/pipelines.  I don't fully understand their motivation for wanting this, but it is captured nicely by the concept of *injectivity*.

```alloy
pred no_sharing(V: set univ, R: V->V) {
  injective[V,R]
}
```

A related issue is when modellers want none of their atoms to be isolated w.r.t a relation.  That's captured by *surjectivity*.
