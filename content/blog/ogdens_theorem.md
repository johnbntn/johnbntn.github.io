+++
title = 'Ogdens Theorem and Inherent Ambiguity in CFLs'
date = 2025-05-25T20:23:22-04:00
draft = true
+++

The pumping lemma for context free languages is a useful tool to prove a language to be non context free. But sometimes proofs about CFLs require more powerful tools. William Ogden, during his PhD at Stanford, published a paper that describes a way to generalize a the pumping lemma, allowing you to, in a sense, "choose where to pump".

But why study it? Well, most notably, the theorem lends itself well to a proof about the inherent ambiguity of CFLs; less notably, I think it's cool.

*This post requires some background on context free languages, specifically the pumping lemma and the CNF of a context free language.*

## Ogden's Theorem
The theorem is as follows:

Let $L$ be an arbitrary CFL. There is some number $p$ such that for any word $w \in L$ where $w$ has at least $p$ *marked symbols*, $w$ can be split into five parts $uvxyz$ with the properties

1) $vxy \leq p$ marked symbols
2) $vy \geq 1$ marked symbol
3) You can "pump" i.e. $uv^ixy^iz \in L, \; \forall i \in \mathbb{N}$

You'll notice the theorem does not place any restrictions on choosing the number of marked symbols; similar to the proof of the original pumping lemma, we need to choose a number where we can guarantee a variable will be seen more than once in a words derivation tree. If a variable shows up more than once, we know we have reached a cycle and can pump. 

Without loss of generality, assume we are dealing with a CFL in Chomsky Normal Form. Any CFL can be converted to CNF, so this is fine. We're going to choose the magical number $p = 2^{k+1}$ where $k$ is the number of non-terminals (or variables) in our language.

To prove that this amount of non-terminals will always result in a loop, consider the derivation tree of a word $w \in L$ with $2^{k+1}$ marked symbols. 

Because of CNF, at every node, we're given the choice between two paths, each with a number of marked symbols. If we always pick the child node with the larger number of marked symbols, we'll never lose out on more than half of the current available marked symbols.

More formally, if parent node $n$ has $x$ marked symbols under it and we choose child node $m$, $m$ has no less than $x/2$ marked symbols under it because we chose the node with the larger amount of marked symbols.

Thus, the number of nodes we visit will be no less than $log_2 2^{k+1} = k+1$, ensuring we visit at least one variable twice. 

From here, the proof almost completely models the proof from the original pumping lemma:
1) $uxy$ has at most $p$ marked symbols
2) Say that variable $A$ is repeated. $A$ has rule $A \rightarrow BC$. Because $A$ is a node on our derivation tree, both $B$ and $C$ also have marked symbols. Thus, $vy$ has at least 1 marked symbol
3) Finally, since $A \Rightarrow^* vAy$ and $A \Rightarrow^* x$, $A \Rightarrow^* v^ixy^i$ so we can do $uv^ixy^iz$ $\forall i \in \mathbb{N}$ 

If this explanation only made you more confused, do not be alarmed; panic is a common reaction to seeing proofs about CFLs. I find that drawing a picture of the parse tree for a random grammar is a good way to mitigate confusion.


## Inherent Ambiguity

Now that we're all Ogden's Theorem experts, we can talk about inherent ambiguity. If you've ever taken an intro to TOC or maybe even a programming languages class, you've likely been asked an exam question about turning an ambiguous grammar (one that has multiple derivation trees for the same word), into a non ambiguous one. For example, the grammar
$$ 
S \rightarrow A \medspace | \medspace B \newline
A \rightarrow a \newline
B \rightarrow a 
$$
is ambigous because the word $a$ can be made 2 different ways. In this extremely contrived example it's easy to see that removing the ambiguity is as simple as removing variable $A$ or $B$.

But computer scientists have been able to prove that some languages can never be turned unambiguous. So if you're ever stuck on an ambiguouty problem on an exam, just say it's not possible and there will be a non-zero chance you're right.

To prove this, we'll take a look at the language $L = \lbrace 0^n1^n2^m \rbrace \cup \lbrace 0^m1^n2^n \rbrace $ and use Ogden's Lemma to show that a string belonging to the language can always be derived in more than 1 way, no matter the grammar rules.