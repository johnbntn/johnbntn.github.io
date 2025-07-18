+++
title = 'Parikhs Theorem'
description = 'Proving a language context free'
date = 2025-05-25T20:23:31-04:00
draft = true
+++

Don't let the lobbyists at big PDA have you thinking there's only one way to prove a language is context free. Parikh's theorem is a helpful alternative when a pda seems too complex to draw. In this post we'll go over Parikh's Theorem, two (!) proofs, and how to use it. Strap in because it's gonna be a wild ride.

### Background 
Like any good TCS concept, Parikh's Theorem starts with a slightly intimidating amount of definitions. They are as follows:

Let $ \Sigma = \lbrace a_1, a_2, \dots, a_k\rbrace$  be a language. Then the *parikh vector* of a word $w \in\Sigma^* $ is the function $p: \Sigma^* \rightarrow \mathbb{N}^k $ where $p(w) = (|w_{a_1}|,\dots, |w_{a_k}|) $

Seems a bit messy but all we're really saying is that, given some valid word, we can count the occurences of every symbol in that word. 

Now a subset of $\mathbb{N}^k$ is *linear* if you can represent it as

 $$ \lbrace \mu_0 + t_1\mu_1 + \dots + t_m\mu_m \: | \:t_1 \dots t_m \in \mathbb{N} \rbrace $$ 
 
 where $\mu_1 ... \mu_m $ are vectors. And a set is called *semi linear* if it is made up of finitely many linear subsets. 

The phrasing semi linear initially threw me off. A semi linear set is not somehow "partially linear", it is just a collection of linear subsets. In fact, just one linear subset is considered to be semi linear. Just another victim of the horrid naming choices made by computer scientists.

### The Theorem

Parikh's Theorem states that for any context free language $L$, let $P(L)$ be the set of it's Parikh vectors. Then $P(L)$ is semi linear. Also, if $S$ is a semi linear set, then there is a regular lanuage that has Parikh vectors $S$.

To prove it, we'll first need to strengthen the [pumping lemma for CFLs](https://en.wikipedia.org/wiki/Pumping_lemma_for_context-free_languages), similar to [Ogden's Theorem](/ogdens-theorem-and-inherent-ambiguity).