---
layout: post
title: "Tournament"
categories: alloy concurrency
---

First and foremost, this specification reminds me why I can't ever hope to be any good at combinatorics.  Problem 1 in Kenneth Bogart's book [Combinatorics Through Guided Discovery](https://bogart.openmathbooks.org/ctgd/ctgd.html) asks:

> Five schools are going to send their baseball teams to a tournament, in which each team must play each other team exactly once. How many games are required?

I've tried and failed to get good at combinatorics many times.  I wondered whether Alloy could be of any help.  After some hacking, I discovered that the number of games in a tournament has something to do with the size of the *diversity* relation on the set of teams.

Then I got carried away.  

I saw an opportunity to model a concurrent system.

## The Specification

There are three signatures in this specification: `World`, `Match` and `Team`.   We won't need to worry about the structure of a `Team,` so we'll relegate it (no pun intended) to a module parameter.

```alloy
module tournament[Team]
```

I'll put my `relmath` [repo](https://github.com/coreflexive/relmath) through its paces.

```alloy
open relmath/order
open relmath/function
```

I don't know what each tournament stage is called (I'm a logic nerd, not a sports fan!), so I've defaulted to my modal logic staple signature: `World`.

```alloy
sig World {
  , P: set World
  , M: set Match
}
```

The `M` relation associates a `World` with the set of `Match` es that have already been played.  (Is there sports terminology for a played match?). The `P` (for Progress) relation is the accessibility relation.  Progress is made by playing matches.  Once a match has been played, it stays played.

```alloy
fact {
  P = ColumnIsContained[Match,World,~M] & di[World]
}
```

If you are familiar with the modal perspective on *intuitionistic* logic, you will recognise that "staying played" is essentially the same as the *monotonicity* or *persistence* of evidence.  Notice, though,  that I decided that the `P` relation should be *irreflexive*.  We need to define an *identity* for the `World` signature.

```alloy
fact {
  id[World] = RowEquivalence[World,Match,M]
}
```

In effect, two worlds are the same when associated with precisely the same matches via the `M` relation.

Now let's look at the structure I've given to the `Match` signature.

```alloy
sig Match {
  , T: some Team
}
```

I am not so naive that I don't know that matches are usually between two teams.  There's a *home* side and an *away* side.  Or a *red* team and a *blue* team.  But there are also matches where there are *multiple* competing teams. Think *Fortnite*.  I wanted to describe all of those.  That meant that I couldn't reach for a relational product.  This led me to derive some interesting constraints.  Before I get into those, I need to get the boring one out of the way: a `Match` cannot exist without a `World`.

```alloy
fact {
  surjective[World,Match,M]
}
```

I wanted to allow the possibility for teams to play each other more than once.  That is, I wanted to allow rematches.  But in doing that, I had to be careful to prevent a team from playing in two matches simultaneously.  To facilitate such a constraint, I defined a relation `N` to indicate which matches are *new* to a particular world.

```alloy
fun N : World -> Match {
  M - ~P.M
}
```

I also needed to tell when matches are disjoint.

```alloy
fun Dis : Match->Match {
  co[Match,Match,T.~T]
}
```

With those two relations in place, I can define the constraint which precludes simultaneous multiparticipation (surely that's sports terminology?)

```alloy
fact {
  (~N.N & di[Match]) in Dis
}
```

That's more exciting than the real thing!

## Visualisation

To get a picture of the weird and wonderful tournaments my brain accepts as *realistic* I recommend that you run the model without adding further constraints.  You should probably use a [custom theme](/assets/tournament.thm), too.

```alloy
run {} for 5
```

If you insist that matches must be played by *two* teams, you'll need the following:

```alloy
pred MatchesHaveTwoTeams {
  multivalent[Match,Team,T]
  involution[Team,di[Team] & ~T.T]
}
```

That's how you make a heterogeneous relation (singular) model a set of *unordered* pairs.  You might also want to restrict tournaments such that they start with no matches having been played.  Altogether, run the following.

```alloy
run {
  MatchesHaveTwoTeams
  some w:World | no w.M
} for 5
```

I'm sure you'll see nothing unexpected.

## Suggestions for Further Hacking

I haven't written a predicate which characterises the worlds in which a tournament has been played.  To be honest, I'm so bored with this model that I can't be bothered writing it myself.  If any *actual people* are reading this who want me to complete the model as such, you can tell me: my contact details are advertised on my GitHub profile.