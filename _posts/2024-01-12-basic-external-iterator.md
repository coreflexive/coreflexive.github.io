---
layout: post
title: "Basic External Iterator"
categories: "alloy"
---

This model aims to illustrate the behaviour of an external iterator.

The `Next` relation represents the linked structure we want to traverse.

```alloy
sig Node { Next: lone Node }
```

The `current` relation tells which `Node` (if any) the `Iterator` is pointing to.

```alloy
sig Iterator {
  var current: lone Node
}
```

The `advance` predicate describes the "movement" of the Iterator from one `Node` to the next.  There might not be a next node, though, in which case we say that the iterator is `done`.

```alloy
pred advance(e: Iterator) {
  not done[e]

  set_current[e, e.current.Next]
}

pred done(e: Iterator) {
  no e.current.Next
}
```

The actual "movement" is achieved by setting an Iterator's current node.

```alloy
pred set_current(e: Iterator, n: Node) {
  current' = current ++ e->n
}
```



## Examples

### Just one Iterator

It's easier to see how the transitions work when we restrict our attention to one Iterator.

```alloy
run advance for 3 but exactly 1 Iterator
```

### Multiple Iterators

Different Iterators may simultaneously enumerate the same structure.

```alloy
run advance
```

This model assumes that the `Next` relation won't change.  If we allow it to change, we can create a real mess.

## Terminating Iteration: Our Fixation on Lines

If we start with an Iterator that is not done, and all we can do is advance, then eventually, that Iterator will be `done`.  Providing, of course, that our structure is a `line` with an end.  If the `line` has no end, the Iterator will never become `done`. 

> Note: I recognise that I should formalise that assertion and illustrate it with Alloy, but I'll leave that for now.

Trying to convince Alloy to produce examples of lines is motivated by our need to create structures whose enumeration is guaranteed to terminate. This is not to say that infinite enumerations don't have their uses.

