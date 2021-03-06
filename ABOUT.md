# About

VIZBTS is a Market Pegged Asset issued on the BitShares blockchain. It purpose is to represent a value of 1 VIZ token.

The main goal is to provide **collaterized** trustless asset. Asset is backed by a collateral and can be force settled to get corresponding backing asset amount.

# Target audience

The general audience are those who involved in [VIZ community](https://viz.world/).

* VIZ holders: can deposit VIZ into gateway and get **collaterized** VIZBTS  to trade later (Note: there is no such gateway yet)
* VIZ buyers: buy VIZBTS and withdraw it via secure gateway or excange for XCHNG.VIZ UIA and use their gateway to withdraw
* Traders: can trade XCHNG.VIZ/VIZBTS pair at a fixed spread with center price of 1 to get profits from allowing XCHNG.VIZ <---> VIZBTS exchange (XCHNG.VIZ is an UIA for VIZ, you can deposit/withdraw it via [XCHNG service](https://viz.world/media/@xchng/%D0%BF%D1%80%D0%B0%D0%B2%D0%B8%D0%BB%D0%B0-%D1%80%D0%B0%D0%B1%D0%BE%D1%82%D1%8B-%D0%B0%D0%B2%D1%82%D0%BE%D0%BC%D0%B0%D1%82%D0%B8%D1%87%D0%B5%D1%81%D0%BA%D0%BE%D0%B3%D0%BE-%D1%88%D0%BB%D1%8E%D0%B7%D0%B0-xchngviz/))
* Shorters: can borrow VIZBTS to open a short position and then buy back at lower price
* Market makers: trade VIZBTS/XXX pairs to get profits from spread and volatility without carrying gateway risks

# Price feeding

Price feeding currently performed by asset issuer. Because VIZ markets are not yet well-established and VIZ is not listed on exchanges except BitShares, we'll use a special technique to provide price feed. Across XCHNG.VIZ market *buy support* mode is used, and *center price* mode is used across VIZBTS markets.

## Buy support mode

Measure buy order book depth for at least 1000 VIZ on several markets to calculate where is strong buy support. Use averaged buy prices across markets


## Center price mode

Across several markets, measure orderbook for 20% DEPTH to obtain liquidity volume in this range. Then, calculate weighted average buy and sell price inside this DEPTH. From these prices, calculate center price.  Use weighted average center prices across several markets, where weight is proportional to liqudity volume of the market. This means that markets with high orderbook liquidity influence more than markets with low liquidity.

# Sudden Margin Call protection

Because VIZ markets are pretty immature, it's easy for an attacker to create high volatility to cause Margin Call and gain profits by selling previously bought VIZBTS into Margin Call order.

To protect borrowers from this kind of manipulation, we're limiting feed price elevation rate. Price feed computations are performed once in a hour. Each time, the algorithm is allowed to increase price "BTS per VIZ" no more than 0.1%. This means max daily increase is ~2.4%.

Meanwhile, max possible price increase is limited by `CR_least - MSSR` (where CR\_least is a collateral ratio of the least collaterized position).

Such price elevation limiting assumes that price elevation normally will not exceed `CR_least - MSSR` faster than increase of the price feed. If it is, the asset will fail into undercollateralized state until price returns, because the GS protection is active.

If `CR_least = 175%`, and `MSSR = 112%`, then diff is 63%, at increase rate 1% per 10 minutes, 63% increase will take 10.5 hours.

# Global Settlement (Black Swan) protection

The asset has a GS protection via pricefeed algorithm. It will limit feed price by not allowing it to cross the GS price.

# Force settlements

Force settlements are recommended as emergency mechanism when asset holder cannot exchange their VIZBTS to another asset for some reason, or for absolutely emergency case when VIZBTS asset issuer is lost (e.g. sudden death).

To discourage unconducive force settlements and protect borrowers, a **Force Settlement Offset** is set to 6%. This means, if feed price is 1, asset holder will settle at price `1 - 6% = 0.94`

# Asset options

* MCR: 200%
* MSSR: 112%
* Feed lifetime: 4 months. Such a big time buffer is needed to allow force settlements in emergency case
* Force Settlement Delay: 24 hours
* Force Settlement Offset: 6%
* Maximum Force Settlement Volume: 5%

# Asset flags

* Market fee: 0.05%
