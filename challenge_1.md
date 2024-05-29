## Challenge 1 - Liquidity Models

The goal of decentralized finance is to give access to traditionnal financial instruments to everyone by removing gatekeepers and middleman, thus empowering everyone with the ability to lend, borrow and trade assets freely.

In today's world, blockchains and cryptocurrencies are the core technologies used to enable decentralized finance. Financial instruments are in general implemented as smart contracts allowing anyone to interact with them to exchange value. Every such exchange is thus settle on-chain, offering a transparent and immutable record of everything that has happened since the system has been launched. In the blockchain world we rarely use the term "financial instruments" but instead to talk about protocol which can be essentially understood as a collection of smart contracts offering specifics features.

For example, on Starknet, you can find the Nostra Protocol which allow you to lend, borrow and trade assets or the Ekubo Protocol which allow people to lend or trade assets. Both of these protocols could be loosly compared to banks, as they allow people to do essentially the same thing, however there is a big difference in how they operate !

Let's take the example of Ekubo for example. Suppose that you owned two types of cryptocurrency, some ETH and some Stark and you would like to invest that money somewhere. Using Ekubo, you can very easily place that money on an object called a "pool of liquidity". A pool of liquidity is essentially a smart contracts which related two assets and allow people to trade one for the other, provided that there is enough units of the traded asset available. Hence, in our example, you can find or create a pool for the pair of assets (ETH, Stark), put some of each on it and you'll start earning fees everytime someone uses your liquidity.

This is very different from the traditionnal banking system where currency exchanges are in general managed by banks or you can, for example, delve into Forex trading which is very complex. On the blockchain, these protocols abstract a lot of the complexity and provided you have a wallet and some tokens, anyone can start investing.

Ekubo is what we call an Automated Market Makers (AMM) which is essentially a Market Maker but where the management of the liquidity is done by an algorithm on-chain. It relies on a model called "Uniswap V3" which is not necessarily easy to understand as you can see by reading the whitepaper.
In anycase, protocols such as Ekubo are a the core of the DeFi world and they will be our object of interest in this small project, because they will allow use to find arbitrage opportunity.

Let's now get back to the context of our arbitrage bot. How do we find arbitrage opportunity ? Arbitrages happens when pool are said to be "unbalanced". To understand what it means we will start with one of the simplest (but also most widely used) AMM model which is that of Uniswap V2. These models defines, for a given amount in of an asset, how much amount out you'll end up with. However, we will see that in general, instead of giving the swap function, these models define an implicit formula (invariant) relating the amount of each tokens they manage.

To make it more concrete, let's see what it means for the Uniswap V2 model. This model is based on what we call a "constant-product formula" which is an invariant that takes the form 

$$reserve_A*reserve_B = K, K \in \mathbb{R}^+ $$

Where $A$ and $B$ are the assets and the reserves are the amount of each of these assets managed by the pool. The value $K$ is a constant implicitely defined at the initialisation of the pool from the the value set for the reserves. So, once the pool has been initialized, the invariant should always hold true ! So assuming that it's true, we can use the invariant to deduce the actual swaps functions of Uniswap V2, i.e the function $f_{A \rightarrow B}(x) = y$ and $f_{B \rightarrow A}(x) = y$ that gives for an amount of token $A$ (respectively $B$) the amount of token $B$ (respectively $A$) one would obtain.

---

### Exercise 1

Derive the two functions $f_{A \rightarrow B}(x) = y$ and $f_{B \rightarrow A}(x) = y$

<details>
  <summary>Solution</summary>

  Suppose we trade an amount $x$ of $A$. The new invariant takes the following form
  $$(reserve_{A} + x)*(reserve_{B} - y) = K$$

  Therefore, to know the amount $y$ out we rewrite it as a function of $x$, yielding
  
  $$y = \frac{reserve_B(reserve_A + x) - K}{reserve_A + x}$$
  
  Now, remembering that $K = reserve_A * reserve_B$ we can simplify the above into
  
  $$f_{A \rightarrow B}(x) = \frac{reserve_B}{reserve_A + x}x = y$$

  The derivation of $f_{B \rightarrow A}$ is essentially the same so we skip it.
</details>

---

Now, we conviniently omitted a very subtle detail which is that AMM, in general, take fees that they redistribute to the people providing the liquidity. While this is a fair mechanism to incentivise user to provide liquidity, it has consequences for our model as we show in the next exercise.

---

### Exercise 2

Suppose that we apply a fee $f$ of 30BPS (0.3%) on the amount in. 
- Modify the formula derived in exercise 1 to take these fees into account.
- Is $K$ still constant ? 

<details>
  <summary>Solution</summary>
  
  The new amount in $x$ becomes $x' = (1 - 0.003)*x = 0.997x$ so we just need to substitute it in the formula. Now let's see how this impact the invariant by writing the following

  $$(reserve_a + x)(reserve_b - f_{A \rightarrow B}(0.997x)) = K'$$

  After a little bit of algebraic manipulation we get

  $$K = 0.997K' \implies K' = \frac{K}{0.997}$$

  So we see that the constant actually increase after each swap proportionnaly to the fee !
  
</details>

---

While this model is very neat and easy, it gives rise to a subtle phenomenon which is what we call the price impact. In an ideal world, suppose that trading 1 unit of token A given you 1.5 unit of token B. Now, suppose you trade 100 unit of token A, how much would you expect to get of token B ? 150 units of B, right ?

Unfortunately, in Uniswap V2 this is not always true, do you see why ? The swap functions depends on the value of the reserves ! While it seems obvious, you cannot get 150 unit of B if there isn't enough of them, what a bit more weird is that such a trade would still be valid, however potentially yielding a horrendously low amount of B.

---

### Exercie 3

Compute the following limit $lim_{x}f_{A \rightarrow B}(x)$. If we consider the price $P(x) = \frac{f_{A \rightarrow B}(x)}{x}$, what can we say about it ?

<details>
  <summary>Solution</summary>

  We get $lim_{x \rightarrow \infty}f_{A \rightarrow B}(x) = reserve_A$ which make sense, however we see that the price become extremely bad ! If for fun we considered $lim_{x \rightarrow \infty}P(x)$ we see that we get a price of 0 !
</details>

---

As you can see, provided that the pool has been initialized with non-zero reserves (it's always the case), then any trade is valid but might potentially yield very poor prices. This is both a big advantage of AMM, because a pool can always be used without requiring external interventions, and a big disadvantage because picking the wrong pool can make you loose all your money so be careful !

Let's get back to our notion of price impact, the price impact is the ratio between the ideal price and the price you will get using that pool in term of percentage.

---

### Exercise 4

Go on app.avnu.fi and try disabling some sources (the little gear bottom-right corner) and try to swap different tokens with medium to large values, checking to induced price impact.

---

Now that we have a better understanding of these different concept, let's see how we can spot arbitrage opportunity ! We've mentionned a the beginning of this challenge that such opportunities arise when the pools are unbalanced.

Suppose we are at block B and we have a pool $Pool(reserve_A,reserve_B)$ balanced which means that $f_{A \rightarrow B}(1) = P$ where $P$ is the real price on the market. Now suppose that someone do a trade, using our pool, with an amount $x$. Let's see how this moves the price !

---

### Exercise 5

Suppose that we have a balanced pool $Pool(reserve_A,reserve_B)$ i.e f_{A \rightarrow B}(1) = P, the real market price. Now suppose someone comes in an trade an amount $x$. What is the new price after the swap, how does it compare to the real market price ?

<details>
  <summary>Solution</summary>

  The new swap function after the swap if

  $$f_{A \rightarrow B}(y) = \frac{(reserve_B - f_{A \rightarrow B}(x))}{(reserve_A + x) + y}y$$

  An therefore we get

  $$P' = f_{A \rightarrow B}(1) = \frac{(reserve_B - f_{A \rightarrow B}(x))}{(reserve_A + x) + 1} < P$$

  Now it should be clear that the impact really depends on how $x$ compare to $reserve_A$.
</details>

---

As you can see, our price is more or less impacted and the fact that this pool gives a new price P' does not mean that the rest of the market does, the pool is now unbalanced ! Observe that the price P' from A to B satisfy $P' < P$ which means that for B to A we have $\frac{1}{P'} > \frac{1}{P}$. Therefore, what we could do is, trade an amount $y$ in the other direction, taking profit of a better price to end up we more money then we started with !

Now that you understand what is arbitrage and how we can take profit of unbalanced pools to make money, our focus in the upcoming weeks will be to build a bot that automatically find and exploit these opportunities. 
