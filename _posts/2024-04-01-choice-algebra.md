---
layout: post
title: "Choice Algebra"
categories: alloy csp
---

# Choice Algebra

In this model, I demonstrate how to derive a binary operator on `Choice` objects and check some of its algebraic properties. We find that the `OpChoice` operator is an idempotent, symmetric monoid. This is encouraging because these are precisely the properties required of CSP's choice operator.

## Dependencies

My last [post](https://coreflexive.github.io/alloy/csp/2024/03/29/choice-processes.html) presented a specification demonstrating how Prefix and Choice objects form a composite. I have refined [that specification](/assets/CspChoice/Domain.md) by adding a couple of predicates, and I rely on this refined version in what follows.

```alloy
module Algebra[Event]

open Domain[Event]
```

## Predicates

### MkChoice

The `MkChoice` predicate characterises how a `Choice` object can be formed from well-formed `Prefix` and `Choice` objects.

```alloy
pred MkChoice(p,q: Prefix, o: Choice) {
  PrefixWf[p]
  PrefixWf[q]

  o.Child = p + q
  o.Menu  = p.Menu + q.Menu
}
```

```alloy
pred MkChoice(p: Prefix, c: Choice, o: Choice) {
  PrefixWf[p]
  ChoiceWf[c]
  
  o.Child = c.Child ++ p
  o.Menu  = c.Menu  ++ p.Menu
}
```

```alloy
pred MkChoice(c: Choice, p: Prefix, o: Choice) {
  MkChoice[p,c,o]
}
```

```alloy
pred MkChoice(c,d: Choice, o: Choice) {
  ChoiceWf[c]
  ChoiceWf[d]

  o.Child = c.Child + d.Child
  o.Menu  = c.Menu + d.Menu
}
```

## Derived Relations

We define a `MkChoice` function based on the predicate of the same name to make checks easier to read.

```alloy
fun MkChoice(x,y: Choice) : Choice {
  { z: Choice | MkChoice[x,y,z] }
}
```

We use the `MkChoice` predicate to define a binary operator called `OpChoice`.

```alloy
fun OpChoice : Choice -> Choice -> Choice {
  {x,y,z: Choice | MkChoice[x,y,z] }
}
```

We will verify the algebraic properties of this operator in the next section.

## Checks

### Properties of `MkChoice`

#### Well-formedness

`MkChoice` constructs well-formed `Choice` objects.

```alloy
check {
  all p,q: Prefix, o: Choice { MkChoice[p,q,o] implies ChoiceWf[o] }
  all p: Prefix, c,o: Choice { MkChoice[p,c,o] implies ChoiceWf[o] }
  all p: Prefix, c,o: Choice { MkChoice[c,p,o] implies ChoiceWf[o] }
  all c,d,o: Choice { MkChoice[c,d,o] implies ChoiceWf[o] }
} for 10
```

#### Simplicity

`MkChoice` is simple.

```alloy
MkChoice_simple: check {
  { composite
  } implies {
     all w,x: Choice {
       all y,z: Choice {
         MkChoice[w,x,y] and MkChoice[w,x,z] implies y = z
       }
     }
   }
} for 10
```

### Algebraic Properties of `OpChoice`

#### Idempotent

`OpChoice` is idempotent.

```alloy
OpChoice_idempotent: check {
  { all x,y: Choice | some MkChoice[x,y]
  } implies {
    Idempotent[Choice, OpChoice]
  }
} for 10
```

#### Symmetric

`OpChoice` is symmetric (aka commutative).

```alloy
OpChoice_symmetric: check {
  { all x,y: Choice | some MkChoice[x,y]
  } implies {
    Symmetric[Choice, OpChoice]
  }
} for 10
```

#### Monoid

`OpChoice` is a monoid.

```alloy
OpChoice_monoid: check {
  { all x,y: Choice | some MkChoice[x,y]
    some Stop
  } implies {
    Monoid[Choice, OpChoice, Stop]
  }
} for 10
```

## Commands

I include the following command not because it will generate any instances (it won't) but because it offers a slightly different view from the above checks.  

```alloy
nope: run {
  no p: Prefix | PrefixWf[p]
  some c: Choice-Stop | ChoiceWf[c]
} for 10 expect 0
```

In plainer terms, it says: "If there are no well-formed `Prefix` objects, then the only `Choice` object that can exist is `Stop`".

## Supporting Definitions

```alloy
pred Magma(A: set univ, op: univ->univ->univ) {
  op in (A->A)-> one A
}

pred Idempotent(A: set univ, op: univ->univ->univ) {
  Magma[A,op]
  all a: A | op[a,a] = a
}

pred LeftUnit(A: set univ, op: univ->univ->univ, I: univ) {
  Magma[A,op]
  I in A
  all x: A | op[I,x] = x
}

pred RightUnit(A: set univ, op: univ->univ->univ, I: univ) {
  Magma[A,op]
  I in A
  all x: A | x = op[x,I]
}

pred Unital(A: set univ, op: univ->univ->univ, I: univ) {
  LeftUnit[A,op,I]
  RightUnit[A,op,I]
}

pred Symmetric(A: set univ, op: univ->univ->univ) {
  Magma[A,op]
  all x,y: A | op[x,y] = op[y,x]
}

pred Semigroup(A: set univ, op: univ->univ->univ) {
  Magma[A,op]
  all x,y,z: A | op[op[x,y],z] = op[x,op[y,z]]
}

pred Monoid(A: set univ, op: univ->univ->univ, I: univ) {
  Semigroup[A,op]
  Unital[A,op,I]
}
```

