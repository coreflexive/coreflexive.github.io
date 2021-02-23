---
layout: post
title: "The Covering Relation"
date: "2021-02-23"
author: "Adam W. Grant"
---
<script src="https://polyfill.io/v3/polyfill.min.js?features=es6"></script>
<script id="MathJax-script" async src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js"></script>

Below are two diagrams of the same partial order:
![A Partial Order](/assets/2021/02/23/cover-relation/partial-order.png)
![A Covering Relation](/assets/2021/02/23/cover-relation/cover.png)

The first depicts the graph of a relation (called R), whereas the second shows R's _covering relation_.  It should be clear that the second is a pared-down version of the first.  The two are precisely connected by the following definition:

> Let $$P$$ be an ordered set and let $$x,y \in P$$.  We say that $$x$$ is **covered by** $$y$$ (or $$y$$ **covers** $$x$$), and write $$x \prec y$$ or $$y \succ x$$, if $$x <y$$ and $$x \leq z < y$$ implies $$z = x$$.  The latter condition is demanding that there be no element $$z$$ of $$P$$ with $$x < y < z$$.

As accurate as the definition is, it does not (in my opinion) give any sense for how a covering relation is actually constructed.  I have written a [note](/assets/2021/02/23/cover-relation/cover_relation.pdf) which explains how we can derive an alternative definition of the covering relation which is not only more concise, but also serves as a recipe for constructing one.

In case you don't want to read the note, I'll give you the nicer definition:

$$\succ \; := \; > - > \cdot >$$

And the recipe:

1. Take a partial order $$\geq$$ and remove all of its reflexive arrows.  Call the result $$>$$.
2. Now take $$>$$ and compose it with itself to make another relation $$> \cdot >$$.
3. Remove from $$>$$ all of the arrows you find in $$> \cdot >$$.  The result is the covering relation associated with the partial order $$\geq$$.

Bon Appétit!
