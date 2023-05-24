---
layout: post
title: "Locomotion"
categories: alloy concurrency
---

# Locomotion

The name of this specification implies movement.  And the relations I declare in it also suggest movement.  But you'll find no traces here: it is a static model.

```alloy
module locomotion
```

I was inspired to write it after reading an [MSc Thesis](https://haslab.github.io/SpecRep/pubs/JCerqueira22.pdf), which included an introduction to Alloy's modelling capabilities.  It showed how we could use Alloy to describe how signals can help trains safely traverse a railway network.  I wanted to see how we can use relations to characterise the hazards of trains occupying a network.

## Supporting Definitions

I've used a couple of relation-algebraic and order-theoretic definitions in this model.  You can find them in my [relmath](https://haslab.github.io/SpecRep/) repo.

```alloy
open relmath/order
```

The `order` module includes the `ops` module from the same repository.  Take a look if you're interested.

## Signatures

I believe the term `Track` denotes any contiguous section of a rail network where we desire only a single occupant.  The `Nxt` relation models how `Tracks` can be connected.

```alloy
abstract sig Track {
  , Nxt: set Track
}
```

I wanted to keep this specification as simple as possible.  For that reason, I don't have a train signature.  Instead, I have signatures that tell whether a track is occupied.

```alloy
sig Occupied, Free extends Track {}
```

You can imagine a track is occupied when a train (or anything else) is on it.

## Constraints

The only *fact* I use restricts the transitive closure of the `Nxt` relation to *acyclicity*.

```alloy
fact {
  irreflexive[Track,^Nxt]
}
```

My opinion is that entirety and surjectivity are overused.

## Derived Relations

As I already mentioned, the purpose of this model is to describe the hazards that can arise from things occupying tracks.

### Hazard

There are two obvious hazards: 

1. the track ahead is occupied, or 
2. multiple confluent tracks (approaching a junction) are occupied.

```alloy
fun Hazard : Track->Track {
    Ahead
  + Confluence
}
```

`Ahead` is the restriction of the `Nxt` relation to `Occupied`.

```alloy
fun Ahead : Track->Track {
  Occupied<:Nxt:>Occupied
}
```

`Confluence` is neatly expressed when we use the *diversity* relation, i.e., the identity's complement.

```alloy
fun Confluence : Track->Track {
    Nxt
  & di[Occupied].Nxt
}
```

### Opening

An opening is when an `Occupied` track is adjacent to a `Free` one.

```alloy
fun Opening : Track->Track {
  Occupied<:Nxt:>Free
}
```

### Permit

Now for the interesting part.  The `Permit` relation uses the `shrink` relation combinator to tell which `Occupied` tracks may move to an adjacent `Free` position.  It's like displaying a green signal.

```alloy
fun Permit : Track->Track {
  shrink[Track,Track,~Opening,*Nxt]
}
```

You can see that I've used our `Nxt` relation to do the shrinking.  In principle, I could have used any ordering over the `Track` signature.  But I thought it would be nice to derive it from one we already have.

### Option

The `Option` relation tells which moves are permitted, i.e. it's the *converse* of `Permit`.

```alloy
fun Option : Track->Track {
  ~Permit
}
```

There may be several options.  There may be none.  I resisted the temptation to constrain `Option` to simplicity.  Choosing among permitted paths is the concern of *routing* and is a separate (albeit interesting) issue.  Also, allowing some openings not to be explicitly permitted is realistic.

## Visualisation

The most important relations we've declared are `Nxt` and `Option`.  It is sensible to **hide all but those** in any visualisation.  I recommend **selecting** `show as attributes` and **deselecting** `show as arcs` for the `Option` relation.

```alloy
run { #Permit > 1 } for 10
```

The instances should convince you that if there are `Occupied` tracks with the `Option` to move, all moves may be made safely and simultaneously. Although, how safe it is for one occupant to make two separate moves simultaneously is open to debate.
