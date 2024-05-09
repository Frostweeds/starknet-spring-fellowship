# Challenge 6 - Execution Engine

In this final week we will implement the Cairo smart contract to execute an arbitrage solution produced by the optimiser we wrote last week. You are free to decide on how you encode the route and how you execute it, however, you can take inspiration from AVNU [execution contract](https://github.com/avnu-labs/avnu-contracts-v2).

> [!NOTE]
> AVNU support a lot of integrations and is designed to be modular which is not required in our case so don't be afraid to cut everything that is not needed.

> [!CAUTION]
> As mentioned, your are free to decide how to encode the route, however, what is important to understand is that if your transaction is valid, it will be executed. While this seems obvious, what needs to be understood is that, validity does not mean that it will do what you expect ! More specifically, in DeFi, it's very important to build safeguard to avoid executing a transaction that could drain all your funds. In our context, if you were to execute a transaction with invalid value, you could potentially do a swap resulting in a very high price impact for example, potentially loosing a lot of money.
>
> Consequently, we propose that you always pass the amount out that you expect to get from your arbitrage and to compare in the contract, the value obtained with what you expected. If the two value differ too much then you abort the transaction, preventing you from loosing money. This parameter is in general named slippage, and it protects you against market volatility, erroneous route or even malicious contract. We propose to use a very small threshold and ensure that you are only checking for negative deviation.

---

### Exercise 1

Implement and deploy your router contract in Cairo

---

### Exercise 2

Encode your $OptimalRouteGraph$ into a valid calldata and send it to your router contract

---

### Exercise 3

Finalize the main bot loop, implementing step 4-7. Here $tokens_list$ is a list of pair of token for which you are looking for arbitrage. You could try out all the token in the graph or only particular tokens, both approach are fine.

```
1 Every S seconds
2    let pool_updates = "Fetch pool updates"   
3    Insert pool_updates in graph  
4    for (A,B) in token_lists:
5        let arbitrage = find arbitrage opportunity
6        if arbitrage is None goto 4
7        else execute arbitrage
```

---

Congratulations ! You should now have a first version of an arbitrage bot for Starknet ! We hope that you had fun with this project and that you've learned a few things that will be useful for your journey on Starknet and we are really excited to see what you will build next ! 
