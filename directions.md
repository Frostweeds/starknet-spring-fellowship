
# New AMM Models

Starknet is already home to several well-known AMM models. We've seen Uniswap V2 in the challenges, but there is also Curve (SithSwap, Nostra) or even Uniswap V3 implementation which provide greater capital efficiency but with an extra complexity for people depositing liquidity.

While these are all essentials models, there exists other very interesting models that have been formalised like Maverick that revolutionized capital efficienty on Ethereum. Why don't you try your hand and implement the next-gen of AMM models on Starknet ?

# Intent-Centric Architecture

One of AVNU's core objectives is to create the optimal transaction that satisfy a user's trading intent. Today, we are the sole actor on Starknet providing this service which is against the decentralized nature of the system. Indeed, it means that the user has to go through AVNU and trust it to provide the best execution. It also means that AVNU third party must ensure integration with AVNU to grab a share of the deal flow or they must advertise their own swap service and user have to juggle between different swap plateform, degrading the total user experience.

In an ideal world, anyone should be able to have access to the user intents and be able to propose a solution that would optimally solve it. What it means, is that we need an infrastructure that allow company, like AVNU, to compete for users intent. Each participants would propose an optimal solutions and they would then be compared and only the best one would be returned to the user. What design would you propose for this infrastructure ?

# Advanced Market Analysis Language

Decentralisation in the context of DeFi allows everyone to manage funds and trade which is amazing. However, it also means that there exists many protocols, many different way of managing your liquidity or trading which can become very cumbersome. Even similar implementation don't use the same naming conventions nor the same interface.

Here the idea is to abstract away this complexity and provide user with a language allowing them to interact with the market in an agnostic way. One approach, could be to have a language that allow you to define how to structurate your portfolio as to satisfy different criterions and upon execution the language would deploy your liquidity the best way possible amongst the different liquidity providers, the Terraform of DeFi ! Another approach is to have a language allowing somone to define rules that are executed against each new block and that can, for example, issue transaction to move your liquidity between pools when some invariant are broken (price not falling in specific range, ...)

# Liquid Restaking Tokens on Starknet

The goal is to bring liquid restaking tokens (LRT) adoption on Starknet. As a first step, you could try bridging existing tokens by leveraging L1 <-> L2 native bridge, or DeFi pooling systems.
It can be another source of real yield on top of very liquid asset.

# Yield market on Starknet

There is a growing demand for Yield Market Protocol like Pendle or Spectra V2. Yield market protocol tokenize yield, effectively separating any yield-bearing token into two parts, the principal and the yield which can then be bought or sold separately. You can compare this to zero-coupon bond and detached coupon in traditionnal finance.

Such protocol increases liquidity sources while also enabling users to bet on yield volatily and execute advanced yield strategies.

