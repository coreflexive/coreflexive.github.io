---
layout: post
title: "Visualising Difunctionals"
categories: alloy relmath
---

I've just finished translating the definition of *difunctional* relations into Alloy for my [`relmath` repo](https://github.com/coreflexive/relmath).  It took me longer than I expected because I kept misassigning types.  But it was worth the effort!

To understand why I'm happy with this one, you need to read this snippet from [the book](https://doi.org/10.1017/CBO9780511778810) I'm taking the definitions from:

> $Q$ has block-diagonal form when suitably rearranging rows and columns independently.

That piece of intuition is beneficial when you're using matrices to visualise your difunctional relation.  It doesn't help much when using Alloy with all its arrows.  For example, here's a picture of a nice difunctional courtesy of Alloy:

![a difunctional relation](assets/difunctional.png)

Can you spot what Schmidt means by "block-diagonal form"?  If not, try Professor Oliveira's description:

> First, some intuition about what “regularity” means: a regular (difunctional) relation is such that, wherever two inputs have a common image, then they have exactly the same set of images. In other words, the image sets of two different inputs are either disjoint or the same.

That last sentence perfectly describes the difunctional shown above.