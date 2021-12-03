# 👓 High-Level View

Vortex actively maintains three core positions:

1. **ETH Holdings -** The _long -_ ETH Holdings are sourced from a decentralized exchange and held idle in an address.
2. **ETH Short Contracts -** The _short -_ ETH Short Contracts are collateralized in USDC and collect the Funding Rate from the decentralized derivatives exchange.
3. **Position Buffer -** The _safety_ - The Position Buffer is sourced from user deposits and held idle to ensure the ETH Short Contracts are not liquidated.

{% hint style="info" %}
_For more information, see_ [_Position Buffer_](risk-management/position-buffer.md)__
{% endhint %}

{% hint style="success" %}
Vortex v1 utilizes [**MCDEX**](https://mcdex.io/homepage/) as the decentralized derivatives exchange and has been developed to accommodate their unique [**AMM Design**](https://docs.mcdex.io/protocol/amm-design).
{% endhint %}
