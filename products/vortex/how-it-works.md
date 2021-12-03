# 💻 How it works

## High-level example

Here’s a high-level example of how Vortex works in favorable conditions:

{% hint style="info" %}
_Assuming 1 ETH = 3500 USDC_
{% endhint %}

1. User deposits 7000 USDC into Vortex, receiving a proportionate share of the pool as Vault Tokens.
2. Vortex’s underlying strategy will then:&#x20;
   1. Send 3500 USDC to a decentralized exchange to buy 1 ‘physical’ ETH;&#x20;
   2. Send 3500 USDC to a decentralized derivatives exchange and use it as collateral to short 1 ETH worth of _Perpetual Contracts_.&#x20;
3. Vortex will automatically collect the _Funding Rate_ and periodically compound and rebalance into both positions, increasing the value of the Vault Tokens.

![](<../../.gitbook/assets/image (5).png>)

{% hint style="success" %}
Vortex utilizes a **Basis Trading** strategy.
{% endhint %}

## How Vortex applies Basis Trading

The crypto markets have historically been weighted towards longs as the majority of participants speculate that prices will go up.

This trend has continued on decentralized derivatives exchanges that offer Perpetual Contracts, which means that Funding Rates have historically, on average, been positive. As a result, from a Funding Rate perspective, it has been profitable to open short positions - but then you may lose a lot more than your Funding Rate returns if prices suddenly moon.

**Vortex** fixes this by removing the directional price risk from the short position while maintaining the Funding Rate advantage, enabling users to generate market-neutral yields.



To learn more about Basis Trading and Perpetual contracts, please visit:

{% content-ref url="../../additional-resources/basis-trading-and-perpetual-contracts.md" %}
[basis-trading-and-perpetual-contracts.md](../../additional-resources/basis-trading-and-perpetual-contracts.md)
{% endcontent-ref %}
