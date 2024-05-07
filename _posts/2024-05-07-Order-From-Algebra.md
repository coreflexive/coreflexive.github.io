---
layout: post
title: "Deriving Orders from Algebras"
---

# Deriving Orders from Algebras

It's well-known that lattices can be defined using either algebra or order relations. I wanted to see for myself the correspondence between the properties of an algebra and the properties of an order derived from it. First, I wrote an Alloy model that encodes those correspondences, and then I proved them. I've included my Alloy below. The proofs, on the other hand, live in [my proof repo](https://github.com/coreflexive/proof) on GitHub.  The proofs are named similarly to the checks.

## Alloy Model

### Signature

```alloy
sig A {
	, o: A->A
	, R: A
}
```

### Facts

For convenience, we constrain the `o` relation to be a magma, i.e., a binary operator that is closed over set `A`.  The main consequence of this is that the operation it represents is defined for all `x` and `y` in `A`.

```alloy
fact {
  Magma[A,o]
}
```

Here, we derive the order relation from our binary operator.

```alloy
fact {
  all x,y: A {
		x->y in R iff o[x,y] = x
	}
}
```

### Checks

These checks demonstrate which algebraic properties give rise to 

```alloy
Reflexive_Ex_Idempotent: check {
  { Idempotent[A,o]
  } implies {
    Reflexive[A,R]
  }
} for 10

Transitive_Ex_Associative: check {
  { Associative[A,o]
  } implies {
    Transitive[A,R]
  }
} for 10

Antisymmetric_Ex_Symmetric: check {
  { Symmetric[A,o]
  } implies {
    Antisymmetric[A,R]
  }
} for 10
```

### Supporting Definitions

#### Algebras

```alloy
pred Magma(A: set univ, o: univ->univ->univ) {
	o in (A->A)-> one A
}

pred Idempotent(A: set univ, o: univ->univ->univ) {
  all x: A | o[x,x] = x
}

pred Associative(A: set univ, o: univ->univ->univ) {
	all x,y,z: A | o[x,o[y,z]] = o[o[x,y],z]
}

pred Symmetric(A: set univ, o: univ->univ->univ) {
  all x,y: A | o[x,y] = o[y,x]
}
```

#### Endorelations

```alloy
pred Reflexive(A: set univ, R: univ->univ) {
	A<:iden in R
}

pred Transitive(A: set univ, R: univ->univ) {
  R.R in R
}

pred Antisymmetric(A: set univ, R: univ->univ) {
  R & ~R in iden
}
```
