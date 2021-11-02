
### Codebase: 

[Rari Capital Vaults]([Maker Dai Delegate](https://github.com/therealmonoloco/maker-dai-delegate/), commit [aed11a7670df18c6c8a2653021297da4d74cad95](https://github.com/therealmonoloco/maker-dai-delegate/commit/aed11a7670df18c6c8a2653021297da4d74cad95)
  

### Findings:

- [Group 1 report](https://github.com/yacademy/audits/blob/main/block_000/week_3/Dhruv.Nibbler.Redbeard)
  
- [Group 2 report](https://github.com/yacademy/audits/blob/main/block_000/week_3/Bebis.Goober.BMApprentices.pdf)

- Protecting against VAR shocks
    - Tolerating a 210% c-ratio would mean a 39% drawdown would liquidate an ETH-A pool, 29% an ETH-B pool, and 17% an ETH-C pool. Under extreme conditions, this would potentially liquidate the underlying collateral in under a day if measures aren’t taken to protect the funds, which would result in significant losses. Not quite disaster resistant.
    - would consider tightening the tolerance to 10% or less as opposed to lowering the collateral ratio - even lowering the ratio to 220% with a 5% tolerance would be safer and allow more leverage. More frequent adjustments and tighter controls would facilitate more TVL with less risk.
- Lock Up Due to Oracle Failure
    - `_getWantTokenPrice L763 - includes `require(minPrice>0)`, so calls will revert if any oracle fails.
    - `liquidatePosition` and `liquidateAllPositions` call `_getWantTokenPrice`, so neither function will execute if an oracle fails.  But, there may be an urgent need to liquidate when an oracle fails.
    - Potential Solutions: Rewrite `liquidateAllPositions` so that it does not call `liquidatePosition/_getWantTokenPrice`
    - Or, use the remaining valid oracle if one oracle fails.
- Strategy Does Not Immediately Detect Loss
    - `prepareReturn` L304 - `_profit` is calculated as `estimatedTotalAssets() - totalDebt`
    - `liquidatePosition` L407 - loss is calculated as `debtOutstanding + profit - looseWant` (after freeing collateral)
    - If the strategy can free sufficient `want` to cover the `debtOutstanding` (plus profit), the strategy will not report a loss.
    - For example, the strategy may not report a loss despite experiencing a loss in the yVault or despite being partially liquidated.
- Current Ratio and Amount to Mint Calculated Differently
    - `adjustPosition` L342 - When computing `currentRatio` calls `getPessimisticRatioOfCdpWithExternalPrice`, which at L214 takes the minimum of `getWantTokenPrice` and `getSpotPrice`("ilk")
    - `_mintMoreInvestmentToken` L556 - uses `getWantTokenPrice`.  If `getWantTokenPrice` is higher than `getSpotPrice`(ilk), the strategy will mint more investment tokens than what is actually indicated by the currentRatio.  This could result in more frequent rebalancing.