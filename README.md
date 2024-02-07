# DeFi-Fundamental-Analysis

 In this project for my UT Austin MSITM Financial Technology class I explore a few DeFi protocols and the applicability of fundamental analysis from TradFi in the DeFi lanscape.

 I will be testing and analyzing these platforms on the Base Ethereum L2 chain as it has cheaper gas fees and faster transaction times than the Ethereum L1 chain.

## Uniswap - Calculate Price Impact

Uniswap is a decentralized exchange (DEX) that allows users to swap various ERC-20 tokens. It is a popular platform for trading and providing liquidity to the DeFi ecosystem. 

One of the key metrics for Uniswap is the price impact of a trade. While Uniswap already calculates this an displays it in the trading window for us, it is useful to understand why this directly impacts the price of your trade and the final amount that you receive. It can be calculated as follows, using the WETH/USDbC pool as an example:

![Pool Stats](docs/poolstats.png)

We can see that the WETH/USDbC pool currently has a rough valuation (TVL) of $1,520,000, where the reserves are ~203.59 WETH and ~1,030,000 USDC. 

Let's first say that we wanted to swap 200,000 USDbC for WETH. We first need to get the initial price of WETH: