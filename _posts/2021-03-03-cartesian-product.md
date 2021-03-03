---
layout: post
title: "Ordered Pairs"
date: "2021-03-02"
author: "Adam W. Grant"
---
<script src="https://polyfill.io/v3/polyfill.min.js?features=es6"></script>
<script id="MathJax-script" async src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js"></script>
The concept of a Cartesian Product is a basic one in Alloy.  If you want to generate the Cartesian Product of two sets `A` and `B`, all you need write is `A->B`.  Similarly, given two elements `a` and `b`, you can form their pair by writing `a->b`.  That's all well and good, but when you want to define relations between products it is useful to explicitly model the pairs with a signature.  This is especially the case if you prefer to write quantifier-free Alloy.  There is more to generating Cartesian Products in Alloy than you might think, though.

The following listing demonstrates how you can model a Cartesian Product with a `Pair` signature:
```alloy
sig Pair {
  fst: A,
  snd: B,
}

sig A {}

sig B {}

fact { iden:>Pair = fst.~fst & snd.~snd }

fact { B->A in ~snd.fst }
```
The model contains two facts.  The first defines an identity relation for the pair signature. As  obvious  as  it  may  seem,  Alloy  needs  to  be  told  that  if  one  pair  has  the same  first  and  second  coordinates  as  another,  then  those  two  pairs  are  to  be considered equal.  The second fact tells the analyser that every possible combination of `A` and `B` should have a corresponding `Pair` object.

The derivations of the facts can be found in [this short note](/assets/2021/03/03/cartesian-product/cartesian_product.pdf).