---
layout: post
title: "Evolving Without a Trace"
categories: alloy tla
---

I have another model I want to share.

I was inspired to write it while watching one of Leslie Lamport's [videos](https://lamport.azurewebsites.net/video/smintla.html).  I love the Professor's video series because he is open about the mathematical nature of specification and model checking.  And his tools are even more powerful than they are popular.  The PlusCal algorithmic language, in particular, has been a hit with programmers and sees regular use in projects at companies like Amazon.

I don't write much TLA+ or PlusCal.  I prefer tools like Alloy and FDR because they appeal to my taste for relation algebra, order theory and type theory.

I've read a lot of online opinions comparing TLA+ and Alloy. Usually written by TLA+ practitioners, their punchline is often that Alloy is best suited to "relational structures".  The implication is that Alloy is inadequate for exploring evolving systems.  

I find that sentiment amusing.  Perhaps because I have invested a lot of time studying relational approaches to modal logic, I can see most systems as "relational structures".  Daniel Jackson was right when he said that Alloy is _time-agnostic_.  But the _time-as-a-linearly-ordered-signature_ (aka trace) pattern he used in his book ultimately detracts from that brilliant feature.

Lamport is a big fan of abstraction and encourages people to think abstractly.  In honour of that, I present a generalisation of the example he explains in the abovementioned video.

```alloy
open relmath/order
open relmath/function

sig W {
  , S: set W
  , i: I
}

sig I { R: set I }

fact "Accessibility relation" {
  S = i.R.~i
}

fact "W identity"{
  id[W] = ColumnEquivalence[W,W,S] & i.~i
}

run {} for 5

run {
  strict_order[I,R]
} for 5

run {
  linear_strict_order[I,R]
} for 5

run {
  univalent[I,I,R]
  linear_strict_order[I,^R]
} for 5
```

I won't explain how it generalises Lamport's example for a couple of reasons.  First, I want you to watch the video and the two before it.  Second, if you need help understanding the correspondence, I want you to read about relation algebra (Tarski's, not Codd's!) and modal logic.  When you do, you will understand how the properties of the relation `R` in the Alloy I wrote influence the *shape* of the accessibility relation `S`.

Then maybe you'll appreciate that facilitating the modelling of "relational structures" is a mighty capability.