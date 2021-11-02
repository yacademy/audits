
### Codebase: 

[WBTC Levered Strategy](https://github.com/GalloDaSballo/wbtc-levered-strat), commit [c3feef72b7c342f690c64cb501026adc489f7c0d](https://github.com/GalloDaSballo/wbtc-levered-strat/commit/c3feef72b7c342f690c64cb501026adc489f7c0d)
  
### Findings:

- [Kat's report](https://github.com/yacademy/audits/blob/main/block_000/week_0/kat.pdf)

- When a user withdraws from a yearn v2 vault, these withdrawals can, if they exceed the reserve of the vault, start withdrawing from individual strategies. The main logic for this is situated in the vault, and uses the strategies liquidatePosition function to divest assets from active strategy positions and return to the vault. Generally speaking any losses accrued through these types of withdrawal are shifted on to the user, by having the yearn strategy satisfying the the following invariant: `_liquidatedAmount + _loss <= _amountNeeded`

- An attacker can frontrun any loss in the strategy by withdrawing less assets than the strategy has available after it has divest its aave position. While individual users potentially can frontrun losses in this strategy, the circumstances for doing so are depending on one of several factors, i.e. what position the strategy has in the withdrawal queue, how much assets the strategy has compared to other strategies in the vault. A larger concern would be the potential to use this as a stepping block for a more severe attack, as the user can move out their assets from the vault without suffering any loss that has occurred in the leveraged WBTC strategy.