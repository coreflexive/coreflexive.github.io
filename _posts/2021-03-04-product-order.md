---
layout: post
title: "Product Order"
date: "2021-03-04"
author: "Adam W. Grant"
---
<script src="https://polyfill.io/v3/polyfill.min.js?features=es6"></script>
<script id="MathJax-script" async src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js"></script>

I mentioned in [my last post]({% post_url 2021-03-03-cartesian-product %}) that explicitly modelling pairs facilitates writing quantifier-free Alloy.  In this post, I'll show you an example of when I've done just that.  Below is some Alloy that generates instances of the direct product of two partial orders using a relation on pairs:
```alloy
sig Pair {
  fst: A,
  snd: B,
	X: set Pair
}

fact { X = fst.R.~fst & snd.S.~snd}

check { partialOrder[X,Pair] }

run { some X }

sig A { R: set A}

sig B { S: set B }

fact { partialOrder[R,A] }

fact { partialOrder[S,B] }

fact { iden:>Pair = fst.~fst & snd.~snd }

fact { B->A in ~snd.fst }

pred partialOrder(r:univ->univ, s: set univ) {
	reflexive[r,s]
	transitive[r]
	antisymmetric[r]
}

pred reflexive(r:univ->univ, s: set univ) {
	iden:>s in r
}

pred transitive(r:univ->univ) {
	r.r in r
}

pred antisymmetric(r:univ->univ) {
	r & ~r in iden
}
```
The first fact in the model constrains the relation `X` such that it becomes the direct product of the two partial orders `A` and `B`.  (The check is there to convince us that this is the case). That the constraint doesn't need to mention points is a consequence of having modelled a `Pair` object equipped with projections `fst` and `snd`.  The fact was derived using the Pointfree Transform and a bit of Relation Algebra, as explained in [this note](/assets/2021/03/04/product-order/product_order.pdf).
