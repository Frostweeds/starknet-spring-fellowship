# Challenge 4 - Route Generation

## Overview

Last week we laid out the first few bricks for our bot and we will continue building on top this week. Recall that our goal is to find arbitrage opportunities and arbitrage can arise in many contexts.

In the first week, we've seen simple arbitrage on a single pool and we've seen that finding such opportunity amount to finding unbalanced pools. However, finding such pools can be a bit tricky because we need an oracle to compare prices.

However, there is something interesting we can do ! Instead of considering just single swap, we will consider multi-swap that can go through multiple pools. This has many advantages, first it will allow us to find arbitrage opportunity for token that we don't necessarily own but that we could get if we exchanged some other token that we do have. More specifically, suppose there is an arbitrage $B \rightarrow C$ but we only have some $A$. If there is a pool $A \rightarrow B$ then we could do $A \rightarrow B \rightarrow C$. Moreover, we can also consider swap of the form $A \rightarrow .. \rightarrow A$ which allow us to remove the oracle constraint we had.

Actually, for any two tokens that have several pools available, we can estimate their price and use that value as the oracle. The accuracy and variance of the value will depend on the amount of pools available as well as the depth of their liquidity but in general this is feasible. This approach allow us to get rid of oracle for most scenarios.

Following what has just been said, we are going to separate the process into two stages, first generate the paths and then finding arbitrages using optimisation algorithms.

Recall that by definition our graph is a undirected graph, i.e edges can be taken in any direction. To generate all the paths from to token, we will start from the all the pools that have as token, the token $from$, and we will then simply follow edges until we find all the pools that are reachable and have as token the token $to$.

However, there are a few subtleties to take into account.

---

## Exercise 1

Is is possible to have a path $A_1 \rightarrow A_2 \rightarrow A_1$ that has an arbitrage opportunity ?

---

## Exercise 2

Is it possible to have a path with an arbitrage opportunity $A_1 \rightarrow ... \rightarrow A_n$ such that it contains a subpath $P$ as in exercise 2 and by removing $P$ there is no longer an arbitrage ?

---

## Exercice 3

Suppose that you have an arbitrage opportunity on a path $A_1 \rightarrow A_2 \rightarrow A_3$, is it possible to have a better arbitrage on a path $A_1 \rightarrow A_2 \rightarrow A_3 \rightarrow .. \rightarrow A_3$ ?

---

So using the observations made with the exercise above, let's write our path generation algorithm !

---

# Exercise 4

On the $PoolGraph$ type, write the method 

```
route(from, to) -> RouteGraph // Generate all the paths starting at from and ending at to
```

Where $RouteGraph$ is a recursive type defined as 

```
RouteGraph = [Route(uuid, DirectedPool, RouteGraph)]
DirectedPool = (From, To, Pool)
```

We propose to assign to each node of the route a unique index (uuid) that will be useful when we are going to perform the optimisation. We propose to simply assign consecutive numbers starting from 0 as the uuid.

Congrats, you should now be able to fetch the pools from the database, initialize a graph and generate valid $RouteGraph$ for different token (from, to) ! Now what remains it to use these subgrap which are now acyclic-directed graph (tree) to find arbitrages and this will be the subject of next's week challenge !




