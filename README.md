<img src="docs/header.png" alt="">

# DeFi-Fundamental-Analysis

The purpose of this project for my UT Austin MSITM Financial Technology class is to explore the applicability of fundamental analysis in decentralized finance (DeFi). I have done this by running a Discounted Cash Flows (DCF) model in Python, a staple valuation method in traditional finance, on the decentralized lending protocol [Aave](https://aave.com/).

Before getting started with the Discounted Cash Flow (DCF) model, I will briefly introduce two of the most utilized DeFi protocols, Uniswap and Aave, to display their functionality. I will then run the DCF model on Aave to estimate its intrinsic value (actual worth of an asset, determined by its fundamentals, such as cash flows, assets, and earnings, rather than its current market price).

### Table of Contents

1. [Uniswap - Price Impact, Swapping, & Liquidity](#1-uniswap---price-impact-swapping--liquidity)
   - [Price Impact Swapping 200,000 USDbC for WETH](#price-impact-swapping-200000-usdbc-for-weth)
   - [Price Impact of Swapping 2 USDbC for WETH](#price-impact-of-swapping-2-usdbc-for-weth)
   - [Swap Demonstration](#swap-demonstration)
   - [Liquidity Provision](#liquidity-provision)
2. [Aave - Supplying & Borrowing](#2-aave---supplying--borrowing)
   - [Interest Rate Model Summary](#interest-rate-model-summary)
   - [Supply ETH & Borrow USDC](#supply-eth--borrow-usdc)
   - [Repay USDC & Withdraw ETH](#repay-usdc--withdraw-eth)
3. [Aave - DeFi Fundamental Analysis (DCF)](#aave---defi-fundamental-analysis-dcf)
   - [Macroeconomic & Industry Analysis](#macroeconomic--industry-analysis)
   - [Python DCF Model](#python-dcf-model)
   - [Results Interpretation](#results-interpretation)


## 1. Uniswap - Price Impact, Swapping, & Liquidity

> 🔎 _See my analysis notebook here for reference: [**Uniswap Analysis**](uniswap.ipynb)_

Uniswap is a decentralized exchange (DEX) that allows users to swap various ERC-20 tokens. It is a popular platform for trading and providing liquidity to the DeFi ecosystem. 

I will be exploring this platform on the Base Ethereum L2 chain as it has cheaper gas fees and faster transaction times than the Ethereum L1 chain.

One of the key considerations when swapping tokens is the price impact of your trade. The price impact of a trade is essentially the impact of your trade on the market of the underlying asset pair. When you trade one asset for another, you're increasing the supply of one asset and decreasing the supply of the other. This can cause the price of the asset you're buying to increase and the price of the asset you're selling to decrease. It can also cause the inverse, which would present a profit on your trade, also called an "arbitrage." The weight of this impact is determined by the size of your trade relative to the size of the liquidity pool.

Conveniently, Uniswap and many other platforms already show this one the UI as it's a critical consideration in a swap trade, but it's still beneficial to understand why/how it works. It can be calculated as follows, using the WETH/USDbC pool as an example:

![Pool Stats](docs/poolstats.png)

We can see that the WETH/USDbC pool currently has a rough valuation (TVL) of $1,520,000, where the reserves are ~203.59 WETH and ~1,030,000 USDC. 

Let's first say that we wanted to swap 200,000 USDbC for WETH. 

We would first calculate the reserve ratio of USDbC/WETH before the swap, which can be calculated as `USDbC_Reserves / WETH_Reserves = reserve_ratio`: 

```
1,030,000 / 203.59 = 5059.19
```

Given this ratio, we can then calculate the constant product, which is the product of the reserves of the pool, and should remain constant after the swap like so `USDbC_reserve_before * WETH_reserve_before = constant_product`:

```
1,030,000 * 203.59 = 209,697,700.0
```

### Price Impact Swapping 200,000 USDbC for WETH

To calculate the price impact of a 200,000 USDbC swap for WETH, given our previous reserve ratio and constant product, we will now need to calculate the reserves after our swap.

We can easily calculate how much USDbC will result in the reserves after the swap, where we add 200,000 to the pool as we are swapping for WETH by using `USDbC_reserve_before + 200000 = USDbC_reserve_after`:

```
1,030,000 + 200,000 = 1,230,000
```

We can then calculate the amount of WETH we will receive from the pool as `constant_product / USDbC_reserve_after = WETH_reserve_after`: 

```
209,697,700 / 1,230,000 = 170.5
```

Lastly, we need to use this value to calculate the reserve ratio after the swap, which can be calculated as `USDbC_Reserves_After / WETH_Reserves_After = reserve_ratio_after`:

```
1,230,000 / 170.5 = 7214.67
```

So at this point we have calculated the following:

- **USDbC Reserves Before**: 1,030,000
- **WETH Reserves Before**: 203.59
- **Reserve Ratio Before**: 5059.19
- **Constant Product**: 209,697,700

<br>

- **USDbC Reserves After**: 1,230,000
- **WETH Reserves After**: 170.5
- **Reserve Ratio After**: 7214.67

Using this information, we can calculate the price impact of our trade using:

![alt text](docs/price_impact.png)

```
((7214.67 - 5059.19) / 5059.19) * 100 = 42.6%
```

This value of **42.6%** is the **price impact** of our trade, which is a signifigant impact. This suggesdts that the price of WETH will increase by 42.6% after our trade, so we would essentially receive only `1 - 42.6% = 57.4%` of the value that we deposited. This is a good example of why it's important to consider the price impact of your trade before making a swap.

### Price Impact of Swapping 2 USDbC for WETH

Now that we've given an example of a significant trade, let's consider a smaller trade that will have a much lower price impact and effect on the liquidity pool.

We already have the following data from our previous example:

- **USDbC Reserves Before**: 1,030,000
- **WETH Reserves Before**: 203.59
- **Reserve Ratio Before**: 5059.19
- **Constant Product**: 209,697,700


Now we need to find the reserves and ratio after the swap.

We can calculate the USDbC reserves after the swap as `USDbC_Reserves_Before + 2 = USDbC_Reserves_After`:

```
1,030,000 + 2 = 1,030,002
```

We can then calculate the WETH reserves after the swap as `constant_product / USDbC_Reserves_After = WETH_Reserves_After`:

```
209,697,700 / 1,030,002 = 203.5896
```

Since the value of this transaction is so small, the difference in the WETH reserves from our swap is negligible. 

We can then calculate the reserve ratio after the swap as `USDbC_Reserves_After / WETH_Reserves_After = reserve_ratio_after`:

```
1,030,002 / 203.5896 = 5059.21
```

Using this information, we can calculate the price impact of our trade using:

```
((5059.21 - 5059.19) / 5059.19) * 100 = 0.0004%
```

As we can see, the **price impact** of this trade is **0.0004%**, which is essentially negligible. This is a good example of how the price impact of a trade can be significantly reduced by reducing the size of the trade relative to the size of the liquidity pool.

> ⚠️ _It's important to note that the reserves displayed on the Uniswap stats UI are rounded to 2 decimal places, so the values we calculated may not be precise enough as 2 decimal places in ETH isn't low enough to reflect such small transactions._

### Swap Demonstration

Now that we've explored the concept of price impact, let's demonstrate a swap on Uniswap. We will swap ETH for 2 USDC, and then check the reported price impact, fees, and actual amount of USDC received.

With the current pool selected, for a swap of ETH for 2 USDC, the price impact is report as just 1.065% since our transaction is so small. In addition to this price impact, we will be paying a protocol fee of <$0.01 and a network (gas) cost of $0.47.

![Swap Price Impact](docs/swap_price_impact.png)

In confirming the swap we can see that we are exchanging about 0.000829 ETH.

![Swap Confirmation](docs/swap_confirm.png)

After the transaction was confirmed, we can see that I've received exactly 2 USDC into the wallet, which is what I expected. I paid a total of $0.52 in gas fees, and spent ~$2.01 in ETH to obtain the USDC. These values change always so there are discrepancies in the exact values between the different UIs.

![Swap Receipt](docs/txn.png)

![Wallet USDC](docs/wallet_usdc.png)

### Liquidity Provision

In addition to swapping, Uniswap also allows users to provide liquidity to the platform. This is done by depositing an asset into one of the pools, and then receiving a share of the pool's fees in return.

To explore this I will be depositing the $2 of USDC that I just swapped for into the ETH/USDC pool.

I will be entering into the 0.05% fee pool, which means that I will receive a _share_ of the 0.05% fee on every swap that occurs in the pool. The low price and high prices of my positions are set to +/- 10% of the ETH price upon opening the position. If the USDC price of ETH falls outside of this range, my position will become inactive.

![Add Liquidity](docs/add_liquidity.png)

In depositing 2 USDC, I will have to deposit an equivalent amount of ETH to the pool. This is calculated as the current price of USDC in ETH, multiplied by the amount of USDC I'm depositing, which is 2. This results in a deposit of <0.001 ETH.

![lp deposit](docs/lp_deposit.png)

Once I have added the liquidity ([_txn reference_](https://basescan.org/tx/0x51cff6f83b2b9c614a47212827a9c8ec6c58460d6bda2f8ea5336bb9d9eba260)), we can see from the blockscan that I have paid a transaction fee of $0.45, and I have received a claim to the deposit in the form of a Uniswap NFT. 

After waiting for about 11.5 hours, the results can be summarized as follows:

#### Initial Investment:

- **Initial Position Value:** $3.8297
- **Initial ETH Deposited:** 0.000756201762617597
- **Initial USDC Deposited:** 2.0

#### After Withdrawal:

![lp withdrawal](docs/lp_withdrawal.png)

**Withdrawal Txn**: https://basescan.org/tx/0x02b60f1258eecbf84e7902b6e238456ed937b82b9a67445d68deb0af80cb66c7

- **Fees Yielded:** $0.00301
- **Amount of ETH Received:** 0.000731917631418648 (-3.2%) ($1.776 USD)
- **Amount of USDC Received:** $2.06187 (+3.1%)
- **Position PnL (Excl. Gas):** $3.83787 - $3.8297 = $0.00817

The fact that I received a different amount of ETH and USDC from my withdrawal is due to the fact that the price of ETH and USDC may have shifted during my investment, and my claim to each asset was shifted.

In conclusion, over the course of this trade I earned a total of $0.00301 in fees, and I gained $0.00817 overall. This is due to multiple factors, including the fact that the price of ETH and USDC may have shifted during my investment, and the fact that I was able to claim a share of the fees from the pool.

## 2. Aave - Supplying & Borrowing

Next I will be exploring Aave, a decentralized lending platform that allows users to supply assets to the platform and earn interest, or borrow assets from the platform and pay interest. Compared to traditional finance, this is essentially a decentralized version of a bank.

I will be also exploring this platform on the Base Ethereum L2 chain for the cheaper gas fees and faster transaction times.

Before proceeding, it is beneficial to understand how the Aave protocol works, specifically the interest rate model for borrowing and lending that determines the rewards that users receive for supplying assets, and the costs that users pay for borrowing assets.

### Interest Rate Model Summary

Aave's interest rate model is the key mechanism that determines the interest rates that users pay for borrowing assets, and the interest rates that incentivises users to supply assets. The interest rate model is based on the **utilization rate** of the assets in the protocol, which is the ratio of the borrowed assets to the supplied assets.

To summarize the mathematical model:

* When capital is available (i.e. supply is high; the debt pool has low utilization): low interest rates to encourage borrowing.

* When capital is scarce (i.e. supply is low; the debt pool is highly utilized): high interest rates to encourage repayments of debt and additional supplying

Using the ETH lending pool as an example, we can see their interest rate model is configured as follows, where the current utilization rate is 60% but the target utilization rate is 80%, so rates are relatively low for what they could be:

![im](docs/im.png)

To see an example of the interest rate model in action, we can continue to use the ETH lending pool as an example to study its supply rate spike in October 2023. We can see that the rate shot up to 6.99% at its peak, moving far above the average rate of 1.42%.

![rate_spike](docs/rate_spike.png)

The supply APR rising to such high levels would mean that a significant amount of ETH was being borrowed, and the protocol was incentivising users to supply more ETH to the pool. 

We can confirm from the borrows APR chart that the borrow rate was also spiking at this time, which would have been a significant incentive for users to repay their loans and decrease the utilization rate of the pool.

![borrows_eth](docs/borrows_eth.png)

To read more about the model in-depth and understand the math behind it, see their documentation: https://docs.aave.com/risk/liquidity-risk/borrow-interest-rate

### Supply ETH & Borrow USDC

To test how the Aave platform works I will deposit some ETH, and then borrow USDC on that ETH stored as collateral.

In depositing $0.53 of ETH, I will be yielding 1.38% APY on my investment. It is important to note that this is a variable rate, and it may change over time as the supply of ETH (WETH) changes. 

![Supply ETH](docs/eth_supply.png)

Once I have approved the supply transaction, we can see that I have received Aave V3 WETH tokens in return for my deposit. These tokens serve as a claim to my deposit, and can be used to withdraw my deposit at any time.

**Deposit Txn**: https://basescan.org/tx/0x141d8577cb44114857d399dd1ad2664e4d8e2113463c612d0cfe50a9cc7d0dd3

![WETH received](docs/WETH_received.png)

Now that I've supplied collateral in the form of ETH (WETH) I can take a partial loan on this collateral. I will be borrowing 50% of my collateral, which is 0.265 USDC. This loan will be charged at a 4.82% APY, which is a variable rate that may change over time, and means that we will accrue interest at a rate of 3.04% per year (as of the current rate) on our loan.

![Borrow USDC](docs/borrow_usdc2.png)

When we go to confirm the borrow transaction, we can see that there is a **health factor** metric. The health factor is a measure of the safety of your loan, and it is calculated as the ratio of your collateral to your loan. If your health factor falls below 1, your loan will be liquidated. The health factor changes based on the value of your collateral and the value of your loan, and it is important to monitor this value to ensure that your loan is safe.

![Borrow Txn](docs/borrow_txn.png)

**Borrow Txn**: https://basescan.org/tx/0xb495d6b4a0633c8de37587d3bf0847544c2d4c0b5db69f54d8adf66995530aa6

Once I have approved the borrow transaction, we can see that I have received 0.265 USDC in return for my loan, as well as a debt token that represents the debt that I owe to the Aave protocol. 

![Debt Token](docs/debt_token.png)

### Repay USDC & Withdraw ETH

After waiting about 6.5 hours, I have accrued a total of $0.00001 in interest on my loan. I initially borrowed exactly 0.265 USDC, and I now have an outstanding balance of 0.26501 USDC.

![Repay USDC](docs/borrows.png)

To repay this loan, I can use my WETH collateral. This will allow me to pay off the loan in full, and the debt token that I was issued will be burned.

I can see from the preview that repaying this loan will restore by health factor and nullify my borrow balance. My WETH staked as collateral will be chopped in half since I borrowed on 50% of it, but I have kept the USDC loan that I took since the WETH was used to repay instead of the USDC.

![Repay Preview](docs/repay_preview.png)

**Repay Txn**: https://basescan.org/tx/0xbd9c3ca108947c643a641d8b00a4d573b688c78569cf1fbc369451f47f57f2fe

Since I have repaid the loan and my health factor was restored, I can now withdraw all of my WETH remaining as collateral. 

**ETH Withdrawal Txn**: https://basescan.org/tx/0xcb5a90985e27e607d1da4fbb5c6bbf138620ce42d61127591432ddfdd209a9de

To summarize the results of this trade:

- **Initial Deposit (WETH):** 0.000216825584857269 ($0.53)
- **Initial Borrows (USDC):** $0.265
- **Interest Accrued (USDC):** $0.00001
- **Final Loan Payoff:** $0.26501
- **WETH Remaining (After Loan Payoff)**: 0.000108019088468248 (-0.00011)
- **USDC Remaning (After Loan Payoff)**: $0.26501 (+$0.26501)

I lost 0.000108806496389 ETH (because of the loan payoff with collateral) but I kept the USDC that I borrowed. So in the end I have about the same amount that I started with in total value (Excl. Gas Fees).

## Aave - DeFi Fundamental Analysis (DCF)

In this final section I will be conducting a fundamental analysis of the Aave protocol, which we previously explored. This will involve analyzing the macroeconomic and industry factors that affect the Aave protocol, and then conducting a discounted cash flow (DCF) analysis to determine the intrinsic value of the Aave protocol. 

A DCF is a staple valuation methodology in traditional finance that measures the intrinsic value of a company based on the sum of the present value of its future cash flows, and it measures the value of an asset based on the amount of cash that it can produce from today until the end of its useful life or until perpetuity, assuming a constant growth rate in those cash flows beyond the forecast period.

### Macroeconomic & Industry Analysis

Aave stands as the leading DeFi lending protocol by TVL, known for introducing innovative features such as flash loans. Its competitive edge is maintained through continuous development, community engagement, and integration with various blockchains to enhance liquidity and user experience. The DeFi sector has shown rapid growth, driven by the broader adoption of cryptocurrency and blockchain technology. 

![lend rankings](docs/lend_rankings.png)

Trends indicate an increasing interest in decentralized financial services, offering opportunities for protocols like Aave to expand their user base and product offerings. The Aave ecosystem benefits from a robust framework supporting diverse assets, a strong security focus, and initiatives to engage with its community through governance mechanisms, positioning it well within the competitive landscape of DeFi.

### Python DCF Model

Please see the DCF model that I ran by viewing the Python notebook here:

[**🕵️ Aave DCF Analysis**](aave.ipynb)

### Results Interpretation

To preface, the current market cap of Aave is $1.259 Billion, with a current token price of $85.50 and a circulating supply of 14,730,095 AAVE. I will compare these values to the intrinsic values calculated from my DCF analysis.

When running DCF analysis, there are a few key assumptions that must be made. There isn't necessarily a "right" way to make these assumptions, but they should be made based on the best available information and the most reasonable expectations for the future:

1. **Risk Free Rate**: This is the rate of return that an investor can expect to receive on an investment with zero risk. In the US, this is typically the yield on a 10-year treasury bond, however, the DeFi market doesn't have a direct equivalent to this. In order to solve this problem, I used a 2% risk free rate generated by a DeFi native model called the [minimum risk rate](https://docs.credmark.com/smart-money-in-defi/investment-concepts/minimum-risk-rate-of-defi#minimum-risk-rate-of-defi-1.0). 

2. **Market Risk Premium**: This is the additional return that an investor can expect to receive for taking on the risk of investing in the stock market. Traditionally this may be calculated as `SPY average return - risk free rate`, however this is not applicable to the DeFi market. I used the average returns from the BITW index fund less the risk free rate to get the market risk premium. This returned a value of 50.6%, which is high but could theoretically still be representative of the risk in crypto markets.

3. **Continued Growth Rate**: This is the rate at which the cash flows of the company are expected to grow into perpetuity. This is a difficult value to estimate, and is typically just assigned a similar value as GDP growth. For this value, I noticed that it had a minimal effect on the final value, and I used an industry standard of 4%, then added 1% to account for the significant growth in the DeFi market and the strong historical growth of Aave.

I ended up projecting two different intrinsic values. The calculation of the first value involved using the beta of the SPY vs. the Aave token to calculate the cost of equity (discount rate), and the second value was based on using the beta of the [BITW Index Fund](https://bitwiseinvestments.com/crypto-funds/bitw) vs. the Aave token

When using the SPY to find the beta (2.02), I found that the intrinsic value of Aave was only `58,171,084`, with a token value of just `$3.95`. This is significantly less than the current market cap and token price. 

However, when using BITW to find the beta (0.30), I found that the intrinsic value of Aave was roughly `$851,340,894`, implying a token value of `$57.80`. This is significantly more than the previous value, and much more representative of the current market cap and token price. 

In summary, The huge discrepancy (caused by shifting the beta) in these intrinsic values indicates that SPY may not be the best measure of risk and return in the crypto market, and that the BITW index may be a better representation. Either way, both of these values are significantly less than the current market cap and token price, which suggests that the Aave token may be overvalued according to my model.

In further considerations, it would likely be best to use historical Bitcoin prices to measure beta, tracing back much farther as an indicator of risk and return in the crypto market, as we only had access to index data from December of 2020. Bitcoin has historically been an indicator of the overall movement of the crypto market, and being the dominant coin historical data shows that many other coins follow its price movements to some extent.
