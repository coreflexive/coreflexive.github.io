---
title: Domain
---

# Domain

## Dependencies

```alloy
module Process[Event]
```

## Signatures

### Process

```alloy
abstract sig Process {
  Menu: set Event
}
```

### Prefix

```alloy
sig Prefix extends Process {
  , head: Event
  , body: Process
}
```

### Choice

```alloy
sig Choice extends Process {
  , Child: set Prefix
}
```

## Facts

I'm going to take both `prefx_identity` and `choice_identity` as facts.

```alloy
fact { prefix_identity }

fact { choice_identity }
```

## Predicates

### Well-formedness

#### PrefixWf

```alloy
pred PrefixWf(o: Prefix) {
  some e: Event, p: Process {
    o.head = e
    o.body = p
    o.Menu = e
  }
}
```

#### ChoiceWf

```alloy
pred ChoiceWf(c: Choice) {
  all p: c.Child | PrefixWf[p]

  c.Menu = c.Child.Menu
}
```

### Identity

```alloy
pred prefix_identity {
  Prefix<:iden =
    head.~head & 
    body.~body
}
```

```alloy
pred choice_identity {
  all z,y: Choice {
    z = y iff z.Child = y.Child
  }
}
```

### Menu

```alloy
pred prefix_menu {
  Prefix<:Menu = head
}
```

```alloy
pred choice_menu {
  Choice<:Menu = Child.Menu
}
```

### Composite

```alloy
pred composite {
  prefix_menu
  choice_menu
}
```

## Derived Relations

### Stop

```alloy
pred stop(c: Choice) {
  no c.Child
}

fun Stop : set Choice {
  { c: Choice | stop[c] }
}
```

## Checks

```alloy
pw_vs_pf: check {
  { all p: Prefix | PrefixWf[p]
    all c: Choice | ChoiceWf[c]
  } iff {
    prefix_menu
    choice_menu
  }
} for 20
```

## Commands

### Chaos

```alloy
chaos: run {}
```

### Order

```alloy
order: run {
  prefix_menu
  choice_menu
  some Choice
  #Event > 1
} for 5
```

---

