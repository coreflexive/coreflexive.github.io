---
layout: post
title: "Pointfree Bicycles"
categories: alloy pointfree
---

Michał Sitko made a two-part video introduction to Alloy.  In his [second video](https://www.youtube.com/watch?v=UMViSWiFwKE&t=2s), he demonstrated using Alloy's quantifiers and navigation expressions to describe bicycles.

It's always lovely to see familiar examples when learning a modelling language.  Everyone knows what a bicycle is, don't they?  And everybody knows what we mean when we refer to a bicycle's front and rear wheels.  It turns out that explaining bikes to Alloy isn't as simple as declaring a signature with two attributes.

I wanted to see how Michał's specification would look after translating it into pointfree notation.  Here is what I came up with:

```alloy
sig Bicycle {
  front, rear: Wheel
}

sig Wheel {}

fact {
  front.~front in iden -- injective
  rear.~rear in iden   -- injective

  no front.~rear       -- co-separated
}

run {} for 6
```

I was pleased with this translation.  It's interesting from a relation algebraic perspective.  A knowledgeable friend of mine put it this way when I showed it to him:

> The model is interesting because it starts off as a **product** (**front** and **rear** as mandatory attributes of bicycle) but ends up having a **coproduct** flavour.

The version you see above isn't what I sent him, though.  My earlier version included an explicit constraint telling Alloy that `front` and `rear` should be disjoint.  That constraint was unnecessary because it follows from *co-separation* as demonstrated by the following `check`:

```alloy
check { no front & rear }
```

The model could be further simplified by exploiting the universal property of relational join.  Meaning that we could have replaced the two injectivity constraints with one.  I'll leave that for you.

Thanks to Michał for the inspiration.