---
layout: post
title: "A Markdown-only Structured Derivation"
---

I am happy today.  I have written a structured derivation using only markdown.  Here it is:

```
● a ⊓ b = a  ⇒  a ⊑ b
⊩   Prove ⇒
  ● a ⇒ b
  1.  a ⊓ b = a
  ≡   Textual substitution using 1
    a ⊓ b ⇒ b
  ⇐   Meet elimination
    true
  ▢
▢
```

```
● a ⊑ b  ⇒  a ⊓ b = a
⊩   Prove ⇒
  ● a ⊓ b = a
  1.  a ⊑ b
  ⇐   ⊑ Antisymmetric
    a ⊓ b ⊑ a ⋀ a ⊑ a ⊓ b
  ⇐   ⊓ Elimination
    a ⊑ a ⊓ b
  ⇐   ⊓ Introduction
    a ⊑ a ⋀ a ⊑ b
  ⇐   ⊑ Reflexive
    a ⊑ b
  ⇐   Assumption 1.
    true
  ▢
▢
```

❤️