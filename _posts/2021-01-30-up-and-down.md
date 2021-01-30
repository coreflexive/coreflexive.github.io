---
layout: post
title: "Up and down, relationally"
date: "2021-01-30"
author: "Adam W. Grant"
---
<script src="https://polyfill.io/v3/polyfill.min.js?features=es6"></script>
<script id="MathJax-script" async src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js"></script>

Order theory's up and down constructions are defined using set comprehensions

$$\downarrow Q := \{y \in P : \exists(x : x \in Q : y \leq x)\}$$

$$\uparrow Q := \{y \in P : \exists(x : x \in Q : y \geq x)\}$$

Their associated coreflexive relations are, respectively,

$$\delta(\Phi_{Q} \cdot \geq)$$, and

$$\rho(\geq \cdot \Phi_{Q})$$

I explain how in this [note](/assets/2021/01/30/up-and-down/up_and_down.pdf).