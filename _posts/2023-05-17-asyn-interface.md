---
title: "Asynchronous Interface"
categories: alloy tla
---

# Asynchronous Interface

I wanted to investigate the state transition relation for the *asynchronous interface* Leslie Lamport described in his book  [Specifying Systems](http://lamport.azurewebsites.net/tla/book.html).

```alloy
module async_interface
```

I won't need sophisticated operators—only the *diversity* relation from my `relmath` repo.

```alloy
open relmath/ops
```

Onto the model.

## Signature

People are mainly used to describing transitions between *states*.  I am biased towards modal logic, so I will call my "state" signature `W` after "World".

```alloy
sig W {
  , Next: set W
  , ack
  , rdy: Flag
  , val: Data
}
```

We can simulate the set `2` with an `up` and a `down` flag.  (We could have used `zero` and `one` just as easily).

```alloy
enum Flag { up, down }
```

Lamport points out the nature of the data being transmitted isn't essential.  We express the same lack of emphasis by using a signature with no relations we call `Data`.

```alloy
sig Data {}
```

### Identity

Two states (sorry, Worlds) are the same when they share the same `ack`, `rdy`, and `val` values.

```alloy
fact {
  id[W]
    = ack.~ack
    & rdy.~rdy
    & val.~val
}
```

I've done it this way because I want to highlight that an accessibility relation can always take us right back to where we started.

## Transitions

Lamport describes two transition relations: Send and Receive.  He specifies them using TLA+.  I present a pointfree translation of each below.  One thing to notice is that many TLA practitioners complain about how Alloy burdens the modeller by not having an `UNCHANGED` keyword.  That might be a problem when working with pointwise specifications, but it isn't so much of an issue when working pointfree.  If you want to say that an attribute remains unchanged by a transition, you intersect with that attribute's kernel, e.g. `rdy.~rdy`.

### Send

What I found particularly entertaining about `Send` is that it can be expressed using composition.  Its *enabling condition* (that `ack` and `rdy` have the same value) is described using a coreflexive.

```alloy
fun Send : W->W {
  (id[W] & (ack.~rdy)).(
      rdy.(di[Flag]).~rdy
    & ack.~ack 
  )
}
```

### Rcv

`Rcv` is very similar to `Send` and is the only place in this specification where I have to use the *diversity* relation, i.e. the complement of the *identity*.  I use it to express the enabling condition that `ack` and `rdy` must differ.  Again the whole relation is expressed compositionally.

```alloy
fun Rcv : W->W {
  (id[W] & ack.(di[Flag]).~rdy).(
      ack.(di[Flag]).~ack
    & val.~val
    & rdy.~rdy
  )
}
```

### Next

The `Next` relation is defined as the union of the `Send` and `Rcv` relations.

```alloy
fact { Next = Send + Rcv }
```

Stuttering might be accommodated by further adding the *identity*.

## Visualisation

Now we can run the model and get a bird's eye view of how this asynchronous interface might behave.

```alloy
run {} for 10 but exactly 5 Data
```

I recommend adjusting your theme to use attributes instead of arcs for the `rdy`, `ack`, and `val` relations.
