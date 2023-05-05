---
layout: post
title: "Awkward Handshake"
categories: alloy pointfree
---

Among Alloy's samples is a model of the *Halmos handshake puzzle*. This post presents my attempt to express the model in a pointfree style.

## Signatures and Relations

The model requires two relations over the set `P` of persons: I call them `spouse` and `Shake`.

```alloy
sig  P { spouse: P, Shake:  set P }
```

The first thing to notice is that `spouse` is a *function* (i.e. a *simple* and *entire* relation).

## The spouse Function

The comments in the original model make the translation into pointfree notation very easy. The requirement that "nobody is his or her own spouse" tells us that `spouse` should be irreflexive.

```alloy
fact  spouse_irreflexive { no iden & spouse }
```

That "a person is his or her spouse's spouse" is captured by saying that `spouse` is an involution.

```alloy
fact  spouse_involutive { spouse.spouse = iden:>P }
```

That `spouse` is symmetric ("if q is p's spouse, p is q's spouse") and "no spouse sharing" is a consequence of being an irreflexive and involutive function.

## The Shake Relation

We must ensure that "nobody shakes own or spouse's hand". The "nobody shakes own" part is described by irreflexivity.

```alloy
fact  Shake_irreflexive { no iden & Shake }
```

Symmetry encapsulates that "if p shakes q, q shakes p".

```alloy
fact  Shake_symmetric { Shake = ~Shake }
```

We now focus on the interaction between the `spouse` and `Shake` relations.

## The Protocol

We must ensure that "nobody shakes spouse's hand". This means we need the two relations to be *disjoint*.

```alloy
fact  spouse_Shake_disjoint { no spouse & Shake }
```

We now have a complete description of the handshaking protocol for the puzzle.

## The Puzzle

The pointfree Alloy version of the `puzzle` predicate is problematic.  I had to copy its definition from the original specification.

```alloy
one  sig  H,  J  extends  P {}

pred  puzzle {
  H->J in spouse
  all p,q: P - J | p!=q => #p.Shake != #q.Shake
}

```

It turns out that while the transform is straightforward on paper, its translation into Alloy results in a model that the analyser can't solve in a reasonable time. What's also funny is that we can rewrite the quantified constraint using universal double trading and again arrive at a less performant model. I'd love to know what's going on here.


I'm glad the transformation hasn't been a complete waste of time. To see what I mean, run the following commands.

```alloy
P10:  run puzzle for  exactly  10 P,  5 int expect  1
P12:  run puzzle for  exactly  12 P,  5 int expect  1
P14:  run puzzle for  exactly  14 P,  5 int expect  1
P16:  run puzzle for  exactly  16 P,  6 int expect  1
```

The pointfree constraints are less burdensome for the analyser to solve (the state space is smaller), and the results come more quickly.  The transformation has been worthwhile.