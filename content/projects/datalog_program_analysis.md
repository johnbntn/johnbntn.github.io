+++
title = 'Datalog Program Analysis'
date = 2025-03-19T14:18:11-04:00
draft = false
+++

Datalog is a logical programming language that is closely related to Prolog. You can think of it as Prolog that always terminates. Recently, there has been some research regarding Datalog's use in program analysis [[1,](https://www.usenix.org/conference/usenixsecurity20/presentation/flores-montoya) [2,](https://www.cse.psu.edu/~gxt29/papers/BPA_NDSS21.pdf) [3](https://yanniss.github.io/doop-datalog2.0.pdf)]

I used BAP (Binary Analysis Program) to generate Datalog facts about a binary for easier analysis and replicated the BAP tutorial using Datalog in significantly less code. [Here](https://github.com/johnbntn/dat) is the repository.

I believe that Datalog analysis will become even more popular as more research is done to make it more expressive and faster. [[4, ](https://plg.uwaterloo.ca/~olhotak/pubs/pldi16.pdf) [5, ](https://dl.acm.org/doi/10.1145/3689754) [6](https://popl24.sigplan.org/details/POPL-2024-popl-research-papers/88/Flan-An-Expressive-and-Efficient-Datalog-Compiler-for-Program-Analysis)]

