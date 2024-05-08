## Challenge 1 - Liquidity Models

## Overview
TODO

The goal of decentralized finance is to give access to traditionnal financial instruments to everyone by removing gatekeepers and middleman, thus empowering everyone with the ability to lend, borrow and trade assets freely.

In today's world, blockchains and cryptocurrencies are the core technologies used to enable decentralized finance. Financial instruments are in general implemented as smart contracts allowing anyone to interact with them to exchange value. Every such exchange is thus settle on-chain, offering a transparent and immutable record of everything that has happened since the system has been launched. In the blockchain world we rarely use the term "financial instruments" but instead to talk about protocol which can be essentially understood as a collection of smart contracts offering specifics features.

For example, on Starknet, you can find the Nostra Protocol which allow you to lend, borrow and trade assets or the Ekubo Protocol which allow people to lend or trade assets. Both of these protocols could be loosly compared to banks, as they allow people to do essentially the same thing, however there is a big difference in how they operate !

Let's take the example of Ekubo for example. Suppose that you owned two types of cryptocurrency, some ETH and some Stark and you would like to invest that money somewhere. Using Ekubo, you can very easily place that money on an object called a "pool of liquidity". A pool of liquidity is essentially a smart contracts which related two assets and allow people to trade one for the other, provided that there is enough units of the traded asset available. Hence, in our example, you can find or create a pool for the pair of assets (ETH, Stark), put some of each on it and you'll start earning fees everytime someone uses your liquidity.

This is very different from the traditionnal banking system where currency exchanges are in general managed by banks or you can, for example, delve into Forex trading which is very complex. On the blockchain, these protocols abstract a lot of the complexity and provided you have a wallet and some tokens, anyone can start investing.

Ekubo is what we call an Automated Market Makers (AMM) which is essentially a Market Maker but where the management of the liquidity is done by an algorithm on-chain. It relies on a model called "Uniswap V3" which is not necessarily easy to understand as you can see by reading the whitepaper.
In anycase, protocols such as Ekubo are a the core of the DeFi world and they will be our object of interest in this small project, because they will allow use to find arbitrage opportunity.

Let's now get back to the context of our arbitrage bot. How do we find arbitrage opportunity ? Arbitrages happens when pool are said to be "not-equilibrated". To understand what it means we will start with one of the simplest (but also most widely used) AMM model which is that of Uniswap V2. These models defines, for a given amount in of an asset, how much amount out you'll end up with. However, we will see that in general, instead of giving the swap function, these models define an implicit formula (invariant) relating the amount of each tokens they manage.

To make it more concrete, let's see what it means for the Uniswap V2 model. This model is based on what we call a "constant-product formula" which is an invariant that takes the form 

$reserve_a*reserve_b = k$.

Where $a$ and $a$ are the assets and the reserves are the amount of each managed by the pool. The value $k$ is a constant which is defined upon creation of the pool and that is in general a function of the price.

### Exercise 1

Suppose that you want to initialize a pool with 5 ETH and 10000 Stark, what would be the value of $k$ you would pick ?

Once the pool has been initialized, the invariant should always hold true ! So assuming that it's true, we can use the invariant to deduce the actual swaps functions of Uniswap V2, i.e the function $f_{a \rightarrow b}(x) = y$ and $f_{b \rightarrow a}(x) = y$ that gives for an amount of token $a$ (respectively $b$) the amount of token $b$ (respectively $a$) one would obtain.

### Exercise 2

Derive the two functions $f_{a \rightarrow b}(x) = y$ and $f_{b \rightarrow a}(x) = y$

It should be clear that trading assets will affect the reserves of the pool. More specifically, suppose we are doing a trade $A \rightarrow B$, then effectively what we are doing is that we are increasing the reserve of $A$ by an amount $x$ and reducing the reserve of $B$ by an amount of $y$. However, you can convince yourself that $k$ will still be the same !

While this model is very neat and easy, it gives rise to a subtle phenomenon which is what we call the price impact. In an ideal world, suppose that trading 1 unit of token A given you 1.5 unit of token B. Now, suppose you trade 100 unit of token A, how much would you expect to get of token B ? 150 units of B, right ?

Unfortunately, in Uniswap V2 this is not always true, do you see why ? The swap functions depends on the value of the reserves ! While it seems obvious, you cannot get 150 unit of B if there isn't enough of them, what a bit more weird is that such a trade would still be valid, however potentially yielding a horrendously low amount of B.

### Exercie 3

Compute the following quantity $lim_{reserve_b \rightarrow \infty}f_{a \rightarrow b}(x)$ and $lim_{reserve_b \rightarrow \infty}f_{b \rightarrow a}(x)$

As you can see, provided that the pool has been initialized with non-zero reserves (which must be impossible), then any trade is valid but might potentially yield very poor prices. This is both a big advantage of AMM, because a pool can always be used without requiring external interventions, and a big disadvantage because picking the wrong pool can make you loose all your money so be careful ! Fortunately, we will see that we can put safe guards.

Getting back to our notion of price impact, the price impact is the ratio between the ideal price and the price you will get using that pool in term of percentage.

### Exercise 4

Go on app.avnu.fi and try disabling some sources and swap different tokens with medium too large values, checking to induced price impact.

Now that we have a better understanding of these different concept, let's see how we can spot arbitrage opportunity ! We've mentionned a the beginning of this challenge that such opportunities arise when the pools are unbalanced.

Suppose we are at block B and we have a pool $Pool(reserve_a,reserve_b)$ balanced which means that $f{A \rightarrow B}(1) = P$ where $P$ is the real price on the market. Now suppose that someone do a trade, using our pool, with an amount $x$. Let's see how this moves the price !

### Exercise 5

Compute $f'_{A \rightarrow B}(1) where $f'$ is the new swap function after the trade, i.e $f$ but as a function of $reserve_a' = reserve_a + x$ and $reserve_b' = reserve_b - y$.

As you can see, our price is more or less impacted, depending on how big is $x$ compared to the $reserve_a$. So now, the fact that this pool gives a new price P' does not mean that the rest of the market does, the pool is now unbalanced ! Furthermore, observe that the price P' from A to B satisfy $P' < P$ so for B to A we have $\frac{1}{P'} > \frac{1}{P}$

Therefore, what we could do is, trade an amount $y$ in the other direction, taking profit of a better price to end up we more money then we started with !

Now that you understand what is arbitrage and how we can take profit of unbalanced pools to make money, our focus in the upcoming weeks will be to build a bot that automatically find and exploit these opportunities. 

TODO Present bot architecture
