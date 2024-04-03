---
layout: post
title: Back to the Drawing Board
---

# Back to the Drawing Board

In my previous two posts, I explained what I thought was a model of CSP's choice operator. On reflection, I realized that I had made a mistake. I tried to model choice as a composite of prefix processes, which is not correct. 

My effort wasn't entirely wasted. What I got was a specification that did, in fact, demonstrate how the composite design pattern could be translated into Alloy. I also illustrated the algebraic properties of that composite. Namely, I discovered it to be an idempotent, symmetric monoid, i.e., a quantifier monoid. It was those properties that led me to believe that I was on the right track.  But the choice operator is more than a quantifier.

What I need to do is revisit the operator's definition and write another model that stays as close as possible to the theory.

The approach I intend to take this time around is to focus more on the evaluation semantics of the choice operator in the hope that I am more likely to arrive at an accurate specification.

