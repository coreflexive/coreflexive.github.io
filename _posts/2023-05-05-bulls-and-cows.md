---
layout: post
title: "Bulls and Cows"
categories: alloy
---

There's a pencil and paper game called *Bulls and Cows*.  Its rules (according to [wikipedia](https://en.wikipedia.org/wiki/Bulls_and_Cows)) are simple:

> On a sheet of paper, the players each write a 4-digit secret number. The digits must be all different. Then, in turn, the players try to guess their opponent's number who gives the number of matches. The digits of the number guessed also must all be different. If the matching digits are in their right positions, they are "bulls", if in different positions, they are "cows".

It bears a striking similarity to the *Lock Challenge* discussed in this [question](https://stackoverflow.com/questions/60951163/lock-challenge-in-alloy) on Stack Overflow.

I'm not going to write a solver for the game.  Instead, I want to use relations to characterise guesses.  I'm going to use definitions from my `relmath` [repository](https://github.com/coreflexive/relmath).

```alloy
open relmath/function
```

I'll get straight to it.

## Solutions and Guesses

Solutions and guesses are functions over domain `I` to a set of digits `D`.

```alloy
sig I { sln,gss: D }

sig D {}
```

Neither the solution nor the guess is allowed to contain duplicates.  That means the functions `sln` and `gss` must be *injective*.

```alloy
fact {
  injective[I,D,sln]
  injective[I,D,gss]
}
```

I had previously ordered the domain `I` but since realised doing so was not necessary.

## Agreement and Disagreement

We need to know which part of the guess agrees with the solution.  We can achieve that using *intersection*.

```alloy
fun Agree : I->D { gss & sln }
```

We also need to know where there is disagreement i.e. the *difference* between `gss` and `sln`.

```alloy
fun Disagree : I->D { gss - sln }
```

Now we focus on the `Disagree` relation.

## Misplaced vs Unknown Digits

The guess may be wrong for two reasons.  Digits may be misplaced.

```alloy
fun Misplaced : I->D { Disagree:>(I.sln) }
```

Or digits might not occur in the solution i.e. they are unknown.

```alloy
fun Unknown : I->D { Disagree:>(D - I.sln) }
```

A few checks tell whether we've missed anything about the structure of `gss`.

```alloy
check {
  gss = Agree + Disagree
} for 10 expect 0

check {
  Disagree = Misplaced + Unknown
} for 10 expect 0

check {
  no Unknown & Misplaced
} for 10 expect 0

check {
  no Disagree
  implies
  sln = gss
} for 10 expect 0
```

None of them generate counterexamples.

## Visualisation

Now we are in a position to visualise what it means for our two functions to (dis)agree.

```alloy
run { no Disagree } for 4

run { some Disagree } for 4
```

You can find the number of Bulls and Cows by counting the ranges of the `Agree` and `Misplaced` relations.
