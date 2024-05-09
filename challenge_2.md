# Challenge 2 - Indexing

Almost all blockchain products are composed of off-chain components that interact with on-chain contracts. On Starknet, these interactions can be either direct, for example a backend calling a specified contract or they could be indirect, where the backend build a transaction that is signed and can then be executed directly by the user.  

In general, off-chain components need to have a view of the blockchain in order to have the data required to build valid transactions. While in some cases, the required data can be fetched when needed, this approach rapidely give rise to issue of scaling, throughput, latency, ... Consequently, most product relies on blockchain indexers that fetch each new block, extract the relevant data, potentially doing some processing before finally storing it a database. For example, at AVNU, we have indexers that collect data about each liquidity sources available on Starknet (extracting reserves, fees, oracle prices, ...).

> [!NOTE]
> In this project, since we will only consider Uniswap V2 model you could technically do it without indexing. However, it would mean that you would have to fetch the information about all the pool declared everytime you startup the bot or you would need to store it once and only fetch the diff between the stored state and the current block which can be quite cumbersome. Indexing is really necessary with other implementation like Ekubo or implemention for which there isn't an easy way to find all the initialized pools. However, feel free to go with this approach if you prefer !

Using indexers allow you  to separate the data collection phase from your business logic which is a good design principle but that's not the only advantage. Typically, indexers are optimised to minimize the time required to get the data from the blockchain to your database, which is really important when you want to offer good user experience. Indeed, you want to provide your service based on the latest data available, otherwise this might result in invalid transactions which will degrade the experience of your user. Additionnaly, it might in some cases yield valid transactions that are no longer optimal, potentially resulting in a poor outcome for the user.

> [!TIP] 
> Block time can be quite long, on Starknet they typically range between 5-10min. Meanwhile, a lot can happen and transactions are not executed on the last committed state but on the pending state which is the state at the last block mutated by all the valid transactions that happened since then. Practically, it means that in most application you need to process the pending block otherwise you'll have a view that does not reflect everything that happened since the last committed block and in DeFi this has a huge impact.

In order to minimise the time between a new block and the storage of data in your database, an approach is to try to extract everything from the data of the block. In general, the data your service requires will be found in the [transactions](https://docs.starknet.io/documentation/architecture_and_concepts/Network_Architecture/transactions/), the [events](https://docs.starknet.io/documentation/architecture_and_concepts/Smart_Contracts/starknet-events/) or the [state update](https://docs.starknet.io/documentation/architecture_and_concepts/Smart_Contracts/contract-storage/) of the block. As an example, [ERC20]([0x49d36570d4e46f48e99674bd3fcc84644ddd6b96f7c741b1562b82f9e004dc7](https://voyager.online/contract/0x049d36570d4e46f48e99674bd3fcc84644ddd6b96f7c741b1562b82f9e004dc7)) emit event for each transfers, containing the a pair of values (from, to, value) easily extractable.

Unfortunately, this is not always as easy because some contracts might not emit event or store data in a different format than the one you need. For example, Pragma Oracle store the price of tokens for every sources, but the actual price is computed when calling the proper method, using different type of aggregation. Therefore, using only the block you would have access to the price for each source (either through events or state update) and you would have to implement the logic of the contract off-chain to recreate the value which might be very cumbersome and error-prone, not mentionning the fact that sometimes you don't have access to the source code.

As a result, most indexers tend to use a combination of block data extraction along with heavily parallelized RPC calls to the methods for which the cost of re-implementing the logic off-chain is too great.

Indexers can be either build from scratch or using off-the-shelves solutions like [Apibara](https://www.apibara.com/). In general, the best approach is to start with off-the-shelve solutions which allow you to rapidely spin-up your products and to move to a custom-made implementation when you want more control, resilience, better performances, ... However, it important to realise that a custom solution might come with non-negligible hosting and maintenance cost so ensure that you've clearly identified the reasons to migrate to such solution and that the benefits largely outweigh these costs.

---

### Exercise 1

The first exchange our bot is going to support is Jediswap which is a Uniswap V2 model as discussed in the previous challenge. In Jediswap, each pool is represented by a contract created from a [factory contract](https://voyager.online/contract/0x00dad44c139a476c7a17fc8141e6db680e9abc9f56fe249a105094c44382c2fd).


Jediswap is a very nice implemention to interact with because all the data that will be interesting to us is exposed through "events" so let's get familiar with them !

#### Exercise 1.1

Using events on the factory, find the first ever pool created on Jediswap. What are the tokens supported by this pool ?

#### Exercise 1.2

Recall the swap function for Uniswap V2, what parametrises it ? Can you find these values inside the events emitted by the pools ?

#### Exercice 1.3(Optionnal)

Using pools [contract storage](https://voyager.online/contract/0x07e2a13b40fc1119ec55e0bcf9428eedaa581ab3c924561ad4e955f95da63138#readStorage) can you also find the same value you found in the preceding exercise ?

---

Now that you understand a bit better how Jediswap works, let's write an indexer to automatically get the reserves at each block ! Once we have this values, we will store them in a database so that we will be able to use them to simulate swaps and find arbitrage opportunities.

---

#### Exercise 2

Create an ApiBara account and write an indexer that process Jediswap events to index new pool and pool updated, gathering the following data for each such pool
```
- pool_address
- fee
- reserve_a
- reserve_b
```


---

#### Exercise 3

Create a MongoDB free account. Configure and connecte your MangoDB instance to Apibara to store the pool data.

---

If everything went well, you should now have a database which is automatically populated at each new block with the last reserves for each pool that has been updated. In the next challenge we will construct our off-chain model for these pools that will allow us to efficiently simulate swaps at each block to find arbitrages. In the meantime, and if you're interested, you can try to write an indexer for other exchange on Starknet that use the same model, for example Nostra.
