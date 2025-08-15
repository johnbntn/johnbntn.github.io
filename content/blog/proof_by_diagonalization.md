+++
title = 'Proof by Diagonalization'
date = 2025-06-12T18:46:36-04:00
draft = false
slug = 'proof-by-diagonalization'
tags = []
+++

If you've taken a discrete math class, you've likely covered the common methods of proof: direct, contrapositive, contradiction, and induction. What you may not know is that there's another, less intuitive way of proving something true: proof by diagonalization. 

This post will start with some background, briefly go over the intuition of the diagonalization method, use it prove there is more than one kind of infinity, before concluding with a proof of undecidability.

A discrete math background is recommended for the majority of this post and some knowledge of [Turing Machines](https://en.wikipedia.org/wiki/Turing_machine) is required for the final proof on decidability.

## Background

Before jumping into diagonalization, it's important we go over the concept of countability. Mathmatician Georg Cantor came up with the concept of countability in order to classify infinite sets. He claimed that a set was countable if it was either finite or had a bijection with the natural numbers.

Remember that a bijection is a function $f: A \rightarrow B$ that is both one-to-one and onto. A function is 1-1 if every element in $A$ the domain maps to exactly *one* element in the $B$ ($f(a) \neq f(b) \text{ whenever } a \neq b$). A function is onto if every element in $B$ is mapped to by an element in $A$ ($\forall b \in B, \exists a \in A, f(a) = b$). 

In other words, a bijective function is one that has a unique mapping between all its elements. 

This allows countable sets to be infinite, which seems like a contradiction, but it's just how countable sets are defined. Play with it a bit and I think it will start to make sense. After all, I think it's reasonable to call the natural numbers countable, even though there are an infinite amount.

Some examples of countable sets include: 

1) The set of even numbers: $f(x) = 2x \; \forall x \in \mathbb{N}$
2) The set of all odd numbers: $f(x) = 2x + 1 \; \forall x \in \mathbb{N} $
3) The set of all rational numbers: *left as an exercise*
    - Hint: You can't start with $p/q$ and increase $q$ because you'll never run out of natural numbers for $p$.

All this talk of countable sets raises an interesting question about whether there exists a set that isn't countable. Thankfully Cantor thought of this as well. What followed was a mathmatical concept so groundbreaking that it got him in trouble with the Catholic Church.

## Diagonalization

In essence, diagonalization proofs are just fancy contradictions, the difficult lies in finding that contradiction. We'll start with an example: proving the real numbers are uncountable[^1].

**Proof:** 

Assume the real numbers $\mathbb{R}$ are countable. By definition, there is a function $f$ that maps every $x \in \mathbb{R}$ with every $y \in \mathbb{N}$. We will use diagonalization to prove that the set $S$ of all infinite binary sequences ($\lbrace x | x \in \lbrace 0, 1 \rbrace ^* \rbrace$) is uncountable, then form a bijection between $S$ and \mathbb{R}. This demonstrates $R$ is as big as an uncountable set, proving it uncountable as well.

First, assume that the set $S$ is countable. Consider an enumeration of all infinite binary strings:

$s_1 = 000 \dots$

$s_2 = 111 \dots$

$s_3 = 010 \dots$

$\dots$


We'll construct an element $x \in S$ that *cannot* be in the enumeration. The construction is as follows:

First imagine the enumeration as a matrix of infinite binary strings. String one on the first row, string two on the second, and so on. Our new infinite binary string is constructed by tracking through the *diagonal* of our matrix and picking the opposite number every time. So if there's a $1$ in [0, 0], our new string will have a $0$ at position 0. 

Thus, we have constructed a string that differs from every single string in our enumeration by at least 1 index, because that's how it was constructed.

If we look at the beginning of the enumeration above, the first three elements of our new string would be $101$.

To form a bijection bijection between $S \text{ and } \mathbb{R}$, we'll first form one between $S$ and $A = \rbrace x | x \in [0, 1) \lbrace $.

Intuitively, for every number right of the decimal, you can create a binary string that represents it. The standard encoding $0_{10} = 0_2, 1{10} = 1_{2}, 2{10} = 10_2, \dots$ works.

Next, you need to prove that $|A| = |\mathbb{R}|$. This is out of the scope of this post, for some info on how to do this, see [this](https://math.stackexchange.com/questions/1896320/cardinality-of-0-1-and-mathbbr) post on Math Overflow.

I hope this served as a useful introduction to the diagonalization method. Don't worry if you found it confusing, I did as well (along with everyone in my Theory of Computation class). Now, we'll move into another application of the method: proving a langugae is undecidable.

## Undecidability

For those unititiated, an undeciable language $L$ is one that, given any turing machine $M$:
- if $x \in L, M \text{ accepts } x$
- if $x \not \in L, M \text{ rejects } x$ 

In other words, $M$ always terminates correctly. Contrast this with *recognizable* languages which may not terminate, but will always be correct if they do.

Proving undecibility is difficult because you can't just try really hard and fail to find a correct machine $M$, you need to prove that an arbitrary machine $M$ will fail. Thankfully (or maybe not, if you don't like it) diagonalization provides one way to do this. We'll start with the langauge $A_{TM}$:

$A_{TM} = \lbrace \langle M, x \rangle | M \text{ is a turing machine and accepts } x \rbrace $

**Proof:**

We'll again start by assuming that $A_{TM}$ is decidable, say by $TM$ $D$. We'll use $D$ to create another $TM$ $A$ that creates a contradiction.

$A =$ On input $M$ where $M$ is a $TM$.
1. Run $D$ on $\langle M, M \rangle $
2. If $D$ accepts, reject
3. Otherwise, accept

In other words, see if $M$ accepts itself and return the opposite. It's easy to forget that Turing Machines can be encoded as strings and can thus be used as input to another $TM$ (including itself).

The contradiction arises when you try to run $A$ on itself. Remember that $A$ always does the opposite of $D$ so if $M$ accepts $\langle M \rangle $, $A$ rejects $M$. 

However, if $A$ accepts $\langle A \rangle $ then $A$ must have rejected $\langle A \rangle $ (and vice versa), which is a contradiction.

But where is the diagonalization?

Imagine a matrix of all Turing Machines. Position $[i, j]$ represents running the $TM$ at row $i$ with input $TM$ column $j$. Normally, the result would be either acceptance, rejection, or a loop but a contradiction must occur at some point along the diagonal, when we run $A$ on $\langle A \rangle$. 

## Conclusion

And that's it! Hopefully you now feel a little more comfortable with diagonalization.

[^1]: If you're interested, Cantor's idea that there were sets larger than infinity is what got him in hot water with some priests.
