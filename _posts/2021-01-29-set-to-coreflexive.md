---
layout: post
title: "From sets to coreflexives in one easy step"
date: "2021-01-29"
author: "Adam W. Grant"
---
<script src="https://polyfill.io/v3/polyfill.min.js?features=es6"></script>
<script id="MathJax-script" async src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js"></script>

Given its comprehension, it is easy to turn a set into a coreflexive relation.  You simply change the body of the comprehension from a scalar to a pair. For example, the set comprehension:

$$\{ x : p.x : x\}$$

has the associated coreflexive relation

$$\{ x : p.x : (x,x)\}$$

And that's it.