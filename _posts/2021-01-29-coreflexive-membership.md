---
layout: post
title: "Coreflexive membership"
date: "2021-01-29"
author: "Adam W. Grant"
---
<script src="https://polyfill.io/v3/polyfill.min.js?features=es6"></script>
<script id="MathJax-script" async src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js"></script>

Associated with each unary predicate $$p$$ is a coreflexive relation, which can be described using a set comprehension of the form

$$\{ z : p.z : (z,z)\}$$

I have written a [note](/assets/2021/01/29/coreflexive-membership/coreflexive_membership.pdf) showing that each of

$$(y,x) \in \{ z : p.z : (z,z)\}$$

$$y=x \wedge p.x$$

$$y \Phi_{p} x$$

have the same meaning.  That is, each of them says that the pair $$(y,x)$$ is a member of the coreflexive relation associated to the unary predicate $$p$$.