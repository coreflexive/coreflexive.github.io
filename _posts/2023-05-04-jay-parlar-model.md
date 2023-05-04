---
layout: post
title: "Transforming Jay Parlar's Resource, Account and User Model"
categories: alloy pointfree
---

I watched Jay Parlar's talk [Alloy for TLA+ users](https://www.youtube.com/watch?v=tZywZc04lJg) on YouTube.

The first part was a demonstration of how Alloy can help us to explore designs.  Jay used Alloy to explore a design for an Accounts, Users, and Resources system.  He showed us how Alloy models are developed in stages and had taken the trouble to preserve his development in a sequence of files.  He has made them available to us on [GitHub](https://github.com/parlarjb/finding_bugs_no_code).

He explained that Alloy has an intuitive syntax and that anybody with a basic grasp of set theory and quantifiers can quickly get valuable results.  I agree, but Alloy shines when you embrace its *relational* capabilities.  In what follows, I will present an alternative specification using relation algebra instead of predicate logic.

## Relation Algebra

Relation algebra is vast, and much has been written about it.  Alloy has utility modules encapsulating relation-algebraic concepts, e.g., `util/relation`.  I won't be using them.  Instead, I'll use my library of definitions which you can find on [GitHub](https://github.com/coreflexive).  I'll need files from my `relation` and `order` repositories for this model. 

```alloy
open relation/common
open order/common
```

Now we can begin.

## Signatures

The signatures I declare have the same names as in Jay's model, but I chose different names for the relations.  I've used `P` for `parent` and `U` for `users`.  He has two relations called `resources`, whereas I've used different names.

```alloy
sig Resource { P: set Resource }

sig Account { AR: set Resource, U: set User }

sig User { UR: set Resource }
```

As mentioned earlier, Alloy models are developed in stages.  Mine was, too, although I've not preserved the steps as a sequence of files.  I started the same way by declaring only the signatures.  Although the code I've written in this post suggests otherwise, I didn't declare all the relations at once.  I started with the one with the most structure.

## Parent

Three facts are sufficient to describe the parent/child relationships among resources.

```alloy
fact { simple[Resource,Resource,P] }

fact { irreflexive[Resource,P] }

fact { strict_order[Resource,^P] }
```

Note that the hierarchical structure is mainly captured by constraining `P`'s transitive closure as a *strict_order*.  Declaring `P` as *irreflexive* captures the requirement that no `Resource` can be its parent.  Relation *simplicity* is more widely known as *determinism*, i.e. a `Resource` has at most one parent.

## Account Resources

One of the problems with Alloy's intuitive syntax is that there is a danger you will reinvent the wheel.  I think this happened a bit in Jay's model.  The facts constraining the cardinality of the relationship between `Resource` and `Account` can be summed up easily, provided you know the math!

```alloy
fact { function[Resource,Account,~AR] }
```

The definition of *function*  here excludes partial ones: I subscribe to the convention that functions are total (I call a *partial* function a *simple* relation).  I don't know whether there is a name for the following constraint.

```alloy
fact { P in img[AR] }
```

But it captures the constancy of an `Account` association across parent/child pairs.  I don't want to go into detail about how it achieves that.  I took Jay's pointwise-expressed fact and removed the quantifiers using a technique called "the pointfree transform".


## Account Users

"no shared users" can be better expressed using conversion and functions.

```alloy
fact { function[User,Account,~U] }
```

The converse of `U` needs to be a function.

## User Resources

I enjoyed transforming the constraint that `User` resources need to be `Account` resources.  Jay named his fact: "only permit owning resources in same account".  The following constraint says the same but in far fewer symbols than the corresponding quantified formula.

```alloy
fact { U.UR in AR }
```

I arrived at that via another pointfree transform but with another step exploiting the Galois connection between composition and left division.

## Run

We've written enough now to run our model.

```alloy
run {} for 5
```

The instances *look* acceptable to me.

## Can access?

The last part of Jay's specification was about the `can_access` predicate.  I've chosen to define this as a relation.

```alloy
fun CA : User -> Resource { UR + UR.~P }
```

That's the result of another pointfree transformation starting this time from the natural language statement:

>  A User can access a Resource if they have direct access to it or if they have direct access to the Resource's parent

The comments in the body of failing `check` tell us that:

> For all combinations of User `u` and Resource `r` if `u` can access Resource `r` it implies that `u` can access every child of `r`

That transforms into algebra as follows:

```alloy
check { CA.~P in CA } for 3 expect 1
```

Like in the original model, we get a counterexample.  The problem isn't that we are asking for something unreasonable; it's just that our definition of `CA` ignores the transitive nature of the `P` relation.  Here's a definition of `CA` that will pass the check:

```alloy
fun CA" : User -> Resource { UR + UR.^~P }

check { CA".~P in CA" } for 3 expect 0
```

I can't remember whether he made a similar suggestion in his talk or that he left it for us to think about.

## Closing

This post demonstrates that Alloy has more than quantifiers and cardinality constraints.  With some relation algebra, you can write very concise specifications.  I didn't show how exactly I derived the pointfree versions because typesetting calculations is a pain.  I would be happy to provide them for anybody interested in seeing them.

Thanks, Jay, for your talk.
