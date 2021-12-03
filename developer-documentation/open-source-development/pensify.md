# Pensify

Pensify is a secure, non-custodial, no-loss and no-risk Pension Fund built on Ethereum blockchain. &#x20;

* By using Robo-Advisor for Yield (RAY) from Staked.US, Fund constantly generates interest from different DeFi protocols&#x20;
* Compound, Aave, dYdX, Fulcrum (latest is turned off atm), MCD, DSR.&#x20;
* Members can also use Flash Loans to earn additional income via a browser bot for automatic arbitrage between Uniswap and Balancer pools.&#x20;
* The fund is built using AkropolisOS framework, which allows automated liquidity provision enabled by the bonding curve, treasury management & automated yield rebalancing.

### Github repo:

{% embed url="https://github.com/AlexanderMazaletskiy/pensify" %}

{% embed url="https://youtu.be/Sw8ki5fnWt0" %}

### How it's made

We used AkropolisOS framework to build a basic architecture for Pensify. It is based on [OpenZeppelin](http://openzeppelin.io) and allows automated liquidity provision enabled by the bonding curve, treasury management & automated yield rebalancing. To enable mobile support, we used [Portis Wallet](https://www.portis.io). It provides secure storage and access to Pensify from any device. We use [Uniswap](https://uniswap.exchange) and [Balancer](https://balancer.finance) protocols as a part of arbitrage strategies for fund members. They can earn additional income by utilizing Flash Loans and performing arbitrage between Uniswap and Balancer. We use [Compound](https://compound.finance) and [Aave](https://aave.com) as an interest source through rebalancer - [Robo Yield Advisor](https://staked.us/v/robo-advisor-yield/) from Staked.Us. It allows us to accumulate interest on all Pensify funds.
