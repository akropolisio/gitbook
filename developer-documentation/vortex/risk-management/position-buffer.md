# Position Buffer

Vortex v1 will remain at 1x leverage, but, as USDC is used as collateral, positions are at risk of liquidation. To manage this risk, a percentage of funds are held idle in an address as a **Position Buffer**. This is done to increase the available collateral in the margin account such that the margin account is always overcollateralised, dramatically reducing any liquidation risk.&#x20;

This Buffer is a set size and will be maintained so long as active positions are open.

The Buffer can be changed at any time by the Vortex Manager; _remargin()_ must be called after the buffer change.

{% hint style="info" %}
_For more information, on how this is managed during strategy operation see_ [_remargin_](remargin.md)
{% endhint %}
