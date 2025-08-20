+++
title = "Rice's Theorem"
date = 2025-07-17
draft = false
slug = 'rices-theorem'
tags = []
+++

In my post about [diagonalization](/blog/proof-by-diagonalization) we proved that the language $A_{TM} := \lbrace \langle M, x \rangle \in A_{TM} | \text{ turing machine } M \text { accepts string } x \rbrace $ is undecidable. But what what else is undecidable? And do we need to use diagonalization to prove it every time? The answers: a lot and no. 

In this post, we'll take a look at a really interesting concept in computer science: Rice's Theorem, which says that any non-trivial property of a language is undecidable. Now, instead of diagonalization, we can simply appeal to Rice's Theorem and greatly shorten our proofs.

As always, we'll start with some background, then move into the proof of Rice's Theorem, before closing with some examples.

## Reduction

Before jumping into Rice's Theorem, it's very important to understand the concept of *reduction*. I think it's best explained with an example: we'll prove the undecidability of [The Halting Problem](https://en.wikipedia.org/wiki/Halting_problem) by *reducing* it to $A_{TM}$. 

To start, we'll define language $L_{HALT} := \lbrace \langle M, x \rangle | \text{ turing machine } M \text { halts on input } x \rbrace$ and assume it's decidable by $TM$ $D$. Next, we'll use $D$ to decide $A_{TM}$, thus reducing the Halting Problem into $A_{TM}$.

Define $TM$ $A :=$ On input $\langle M, x \rangle$:
1. Run $D$ on $\langle M, x \rangle$
2. If $D$ accepts ($M$ halts on $x$), run $M$ on $x$ and respond as $A$.
3. If $D$ rejects, reject

We know that this machine $A$ decides $A_{TM}$ because if $M$ halts on $x$, $M$ will either accept or reject and if $M$ doesn't halt on $x$, we know that it would not accept. 

Hopefully this demonstrates the concept of reduction. We want to prove that one problem is undecidable by proving that it can be used to solve another undecidable problem. Note that now you can also reduce to the halting problem as well to prove undecidability.

## Rice's Theorem

Informally, Rice's Theorem states that all non-trivial properties of a program are undecidable. This means that, given some langugage $L_p := \lbrace \langle M \rangle \in L | M \text{ is a turing machine that only accepts inputs that have non-trivial property } p \rbrace $, $L$ will always be undecidable. We say that a property is non-trivial if it is not true/false for every program. In other words, a trivial property is one that is either true or false for every program while a non-trivial property classifies Turing Machines into ones that satisfy or don't satisfy. 

One way to prove this is by reduction to $A_{TM}$. 

**Proof:**

First, for every non-trivial property $P$, we'll define $P_{TM} := \lbrace \langle M \rangle \in P_{TM} | L(M) \text{ has property } P \rbrace$.

For contradiction, we'll asssume it's decided by $TM$ $D$. Because $P$ is non-trivial, we can define the language $L$ such that if $\emptyset \in P$, then $L \not\in P$. And if $\emptyset \not\in P$, then $L \in P$. Let $R$ be the recognizer for $L$. We'll assume that $\emptyset \in P$ but you can adapt the proof to work the other way as well.

Now, we'll use $R$ and $D$ to create some $TM$ $B$ that decides $A_{TM}$:

$B :=$ On input $\langle M, x \rangle$:
1. Define the $TM$ $M' :=$ On input $y$:
    1. Run $M$ on $x$
    1. If $M$ rejects, reject. Else run $R$ on y.
2. Run $D$ on $M'$
3. If $D$ accepts, reject, else accept.

First, don't be alarmed by the creation of another turing machine within a turing machine. Next, pay attention to what $M'$ is actually doing. The only way it can run $R$ on $y$ is if $M$ halts on $x$. If $M$ doesn't halt, then $M'$ accepts nothing ($\emptyset$). Remember that $\emptyset \in P$, so if $D$ accepts $M'$, it must be the case that $M$ doesn't halt on $x$, and we reject.

The other case, where $M$ halts on $x$, is handled as well. If $M$ rejects, we reject as well. Thus, $M'$ will only generate $\emptyset$, causing $D$ to accept and $B$ to reject. 

If $M$ accepts, we start to simulate $R$ on $y$. Reminder that $y$ is the input to $M'$. This means that $M'$ will generate $L \not\in P$ and $D$ will reject, causing $B$ to accept.

These kind of arguments can be really tricky at first. The important thing to remember is that we never actually run $M'$, it's only used as input to $D$. As a result, we don't need to care about it's running time. 

## A Small Example

Consider a language $EMPTY_{TM} := \lbrace \langle M \rangle | L(M) = \emptyset$. It's clear that this is a non-trivial property because we can have a language only containing $\emptyset$ and a language that contains strings of $0$s of length $5$. By Rice's Theorem, this language is undecidable.

See how much shorter that is than using diagonalization! I hope this post provided a good explanation of Rice's Theorem as well as why it might be useful. Until next time!