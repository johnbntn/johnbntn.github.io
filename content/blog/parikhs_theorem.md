+++
title = "Parikh's Theorem"
description = 'Proving a language context free'
date = 2025-05-25T20:23:31-04:00
draft = true
+++

Don't let the lobbyists at big PDA have you thinking there's only one way to prove a language is context free. Parikh's theorem is a helpful alternative when a pda seems too complex to draw. In this post we'll go over Parikh's Theorem, its proof, and how to use it. Strap in because it's gonna be a wild ride.

## Background 
Like any good TCS concept, Parikh's Theorem starts with a slightly intimidating amount of definitions. They are as follows:

Let $ \Sigma = \lbrace a_1, a_2, \dots, a_k\rbrace$  be a language. Then the *parikh vector* of a word $w \in\Sigma^* $ is the function $\psi: \Sigma^* \rightarrow \mathbb{N}^k $ where $\Psi(w) = (|w_{a_1}|,\dots, |w_{a_k}|) $

Seems a bit messy but all we're really saying is that, given some valid word, we can count the occurences of every symbol in that word. 

Now a subset of $\mathbb{N}^k$ is *linear* if you can represent it as

$$ \lbrace \mu_0 + t_1\mu_1 + \dots + t_m\mu_m \\: | \\: t_1 \dots t_m \in \mathbb{N} \rbrace $$ 

where $\mu_1 ... \mu_m $ are vectors. And a set is called *semi linear* if it is made up of finitely many linear subsets. 

The phrasing semi linear initially threw me off. A semi linear set is not somehow "partially linear", it is just a collection of linear subsets. In fact, just one linear subset is considered to be semi linear. Just another victim of the horrid naming choices made by computer scientists.

## The Theorem

Parikh's Theorem states that for any context free language $L$, then $\Psi(L)$ is semi-linear. Also, if $S$ is a semi-linear set, then there is a regular lanuage that has Parikh vectors $S$. 

More intuitively, the theorem claims that, if you ignore the ordering, a regular language is equivalent to a context free language.

First we'll prove that any semi-linear set $S$ is equal to the Parikh vectors of a regular set $R$. Since $S$ is the union of a finite number of linear sets and the union of regular sets is also a regular set, we only have to prove that a single linear set corresponds to the parikh vectors of a regular set. Let $S'$ be the linear set $\lbrace u_0 + t_1u_1 + \dots + t_mu_m | t_i \in \mathbb{N} \rbrace$. Then it corresponds to the regular set $R = \lbrace z_{0} \cdot (\bigcup_{i = 1}^m z_i) \rbrace$ where each $z_i$ has Parikh vector $u_i$. 

Next we'll show that any CFL has Parikh vectors that are a semi-linear set.

To do so, we'll first need to strengthen the [pumping lemma for CFLs](https://en.wikipedia.org/wiki/Pumping_lemma_for_context-free_languages).

For every context free grammar $G = (N, \Sigma, T, S)$, there is an integer $p$ such that, for every $k \geq 1$, if $w \in L(G)$ and $|w| \geq p^k$, then any derivation $S \Rightarrow ^ * w$ is equivalent to (uses the same derivation tree) as a derivation 

\\[
    S \Rightarrow ^* uAv \Rightarrow ^* ux_1Ay_1v \Rightarrow ^* \dots \Rightarrow ^* ux_1 \dots x_k A y_k \dots y_1v \Rightarrow ^* ux_1 \dots x_k z y_k \dots y_1v = w
\\]

for some nonterminal $A \in N$ and each $v_ix_i \neq \epsilon$ and $|v_1 \dots v_kwx_k...x_1| \leq p^k$

The proof of this is incredibly similar to the proof of the original pumping lemma. At a large enough $p$, a variable will always show up at least twice and if a word has length $\geq p^k$, a variable will show up at least $k$ times. For a better explanation on strengthening the pumping lemma, see my earlier post on [Ogden's Theorem](/ogdens-theorem-and-inherent-ambiguity). 

To prove the theorem, we'll construct a regular language, prove that it's Parikh vectors are equivalent to an arbitrary context free language, then close with proving its semi-linearity.

Consider a CFG $G = (N, \Sigma, T, S)$ and let $L = L(G)$ be its associating CFL. Let it have pumping length $p$. Let $U \subseteq N$ and $S \in U$ Let $L_U \subseteq L$ be the words in $L$ that use only the nonterminals belonging to $U$. We'll prove that $\Psi(L_U)$ is semilinear, proving $\Psi(L)$ is as well because context free languages are closed under union. 

First let $k = |U|$ and define

\\[
F = \lbrace w \in L_U \mid |w| < p^k \rbrace
\\]

\\[
G = \lbrace xy \mid 1 \leq |xy| \leq p^k \text{ and } A \Rightarrow ^* xAy \text{ for some } A \in U \rbrace
\\]

Note that both of these are finite, making $FG^*$ regular.

We will prove $\Psi (L_U) = \Psi (FG^*)$.

To prove that \\( \Psi (L_U) \subseteq \Psi (FG^*) \text{ let } w \in L_U \\). 

If $|w| \lt p^k, w \in F \subseteq FG^* $. If $|w| \geq p^k$, we'll have to use our strengthened pumping lemma. Because $w \in L_U$, by definition there is a derivation $S \Rightarrow^* w$ that uses all the non-terminals in $L_U$ and by our strengthened pumping lemma is equivalent to a derivation

\\[
S \Rightarrow ^* uAv \Rightarrow ^* ux_1Ay_1v \Rightarrow ^* \dots \Rightarrow ^* ux_1 \dots x_k A y_k \dots y_1v \Rightarrow ^* ux_1 \dots x_k z y_k \dots y_1v = w$
\\]

where $A \in U$, and each $x_iy_i \in G$. By a pigeonhole argument (think about how many derivations we need to do to get from $uAv$ to $ux_1 \dots x_k z y_k \dots y_1v$), we can remove a derivation $A \Rightarrow^* x_iAy_i$ and still ensure that the new string $w'$ has a derivation tree that includes all non-terminals and so $w' \in L_U$. Because $|w'| \lt |w|$, we can inductively assume that $|w'| \lt p^k$ and so $\Psi (w') \in \Psi (FG^*)$. 

Thus, $\Psi(w) = \Psi(w'x_iy_i) \in \Psi (FG^*)$. 

Next, we'll prove\\(\Psi (FG^*) \subseteq \Psi(L_U) \\).

Let $w \in FG^\ast$. If $w \in F$, then $w \in L_U$ If $w \notin F$ then think of $w$ as a string $w_0s$ where $w_0 \in F$ and $s \in G$. Then $s = xy$ where there is some transition $A \Rightarrow^\ast xAy$ for some $A \in U$. Since $|w_0| \lt w$ (else $w \in F$), then we can assume inductively that $\Psi (w_o) = \Psi(w'), w' \in F$. By our strengthened pumping lemma, there is a derivation 

$S \Rightarrow^* uAv \Rightarrow^* uzv = w'$

This is pumpable, so there is a derivation

$S \Rightarrow^* uAv \Rightarrow^* uxAyv \Rightarrow^* uxzyv = w''$

Thus, $\Psi(w'') = \Psi(w'xy) = \Psi(w_0s) = \Psi(w)$ and $w'' \in L_U$, so $\Psi (w) = \Psi(w'') \in \Psi(L_U)$. 

Thus, $\Psi (FG^*) \subseteq \Psi(L_U)$.

All that's left is to prove that $\Psi(FG^*)$ is semi-linear. This is left as an exercise, to start, note that both $F$ and $G$ are finite.

## But Why?

For a quick application of Parikh's Theorem, take a look at problem *2.43* from the [Sipser book:](https://www.goodreads.com/book/show/400716.Introduction_to_the_Theory_of_Computation)

**Question:** For strings $w \text{ and } t$, write $w \doteq t$ if the symbols of $w$ are a permutation of the
symbols of $t$. In other words, $w \doteq t$ if $t$ and $w$ have the same symbols in the same
quantities, but possibly in a different order.

For any string $w$, define $SCRAMBLE(w) = \lbrace t \mid t \doteq w \rbrace$. For any language $A$, let
$SCRAMBLE(A) = \lbrace t \mid t \in SCRAMBLE(w) \text{ for some } w \in A \rbrace$.

**a.** Show that if $\Sigma = \lbrace 0,1 \rbrace$, then the $SCRAMBLE$ of a regular language is context free.

**b.** What happens in part (a) if $\Sigma$ contains three or more symbols? Prove your
answer.

For right now, perhaps a more intuitive way to answer this question is to keep track of the number of seen $0s$ and $1s$ through your PDA, comparing that with a path through your NFA (see [this](https://cs.stackexchange.com/questions/138357/given-l-is-a-regular-language-prove-that-perml-is-context-free/138358#138358) answer and on CS Stack Exchange for more details).  Then, for part **b**, notice that a permutation of $(abc)^*$ is not context free. 

However, Parikh's theorem provides another (I think more elegant) way to solve this problem. 

Consider some context free (and therefore regular) language $L$ with $\Sigma = \lbrace 0, 1 \rbrace$. By Parikh's theorem, $\Psi(L) is a semi linear set. Now, instead of reasoning about a PDA simulating a NFA, we can prove that for any **linear set** $S$, the language $L(S) \text{ such that } w \in L(S) if \psi(w) \in S \text{, where } \Sigma = \lbrace 0, 1 \rbrace$. Remember that $SCRAMBLE(w)$ only counts the number of occurences, so it's perfectly modeled by the Parikh vector.

To prove this, we'll construct a PDA and prove its correctness. First, a reminder that a semi