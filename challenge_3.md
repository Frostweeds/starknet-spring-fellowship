# Challenge 3 - Starting our bot and modelling Uniswap V2

## Overview
- TODO

Last week we've managed to index the data we need to simulate Jediswap - Uniswap V2 implementation. This week, we will start coding our bot and implement our off-chain model to simulate swap.

> Note
>
>The bot can be written in any language you want, but ideally a language with a good MongoDB library and a good Starknet library.

Here is a proposition of the bot architecture but you are free to implement it entierely differently. Nonetheless, the exercises below are written based on the assumption that you are following the diagram. 

TODO Diagram

### Exercise 1

Implement the $Pool$ type that exposes at least the following methods

- swap(amount_in) -> float
- apply_swap(amount_in) -> float
- estimate_price() -> float

The first method take an amount in, apply the Uniswap V2 swap function and return the amount out. The second method does the same thing but mutate the pool to reflect the change in the reserves.

### Exercise 2

Implement the $PoolGraph$ type which will store our $G(V;E)$ of pools. The set $V$ of vertices will be the pools while the set $E$ of edges will contains a edge between any two pools that have a common token. For example, suppose you have two pools $P_{A,B},P_{B,C}$, where $A,B,C$ are tokens then there will be an edge between them because they share token $B$.

The $PoolGraph$ will be used to generate all the paths going from any token $A$ to any token $B$ so you can already use that information in your implementation, however path generation will be the subject of next week's challenge.

We propose the following methods

- add_pool(pool)
- routes(from, to) -> RouteGraph

### Exercise 3

The main loop of our bot will work roughly as follows

```
1 Every S seconds
2    let pool_updates = "Fetch pool updates"   
3    Insert pool_updates in graph  
4    for (A,B) in token_lists:
5        let arbitrage = find arbitrage opportunity
6        if arbitrage is None goto 4
7        else execute arbitrage
```

Implement the steps 1-3
