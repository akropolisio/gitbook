---
description: This section will provide an overview of key concepts utilized by Vortex.
---

# 📓 Basis Trading and Perpetual Contracts

## What is Basis Trading?

**Basis Trading** is an arbitrage strategy used in financial markets which takes advantage of the difference between the spot and future price of a commodity (the _basis_).

As an example of how basis trading works - and without going into too much detail - imagine a trader had the opportunity to purchase 1 ETH at $3500 and sell the equivalent of 1 ETH of futures contracts at $4000. The trader would then be able to lock in a profit from the basis, which in this case is $500.

Vortex works in a similar way, but instead of a centralized futures market, uses decentralized derivative exchanges that offer **Perpetual Contracts** to generate yield.

## What are Perpetual Contracts?

**Perpetual Contracts** are similar to traditional futures, but, as their name suggests, have no expiration or settlement date.

This indefinite-until-closed nature also means that Perpetuals trade much closer to the current spot price than futures - but, being derivatives, they do still diverge. This price divergence from spot generally reflects the sentiment of traders on the exchange.

It is crucial that this divergence is controlled and the price of Perpetuals are frequently brought back to closely match spot prices.

The mechanism to achieve this control and incentivize spot/Perpetual price stability is known as the **Funding Rate**.

## What is the Funding rate?

The **Funding Rate** is a fee periodically paid from the ‘more popular’ side of the market to the opposing ‘less popular’ side to incentivize contract purchases.

If the Perpetuals price is above the spot price, the Funding Rate will be positive and traders with open long contracts will pay the rate to traders with open short contracts.

Conversely, if the Perpetuals price is below the spot price, the Funding Rate will be negative and open shorts will pay open longs.
