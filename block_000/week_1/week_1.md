
### Codebase: 

[Strategy Rebalancer](https://github.com/tonkers-kuma/strategy-rebalancer/), commit [936b42941b4a8a4a86db7fd6cd58fc60c3120b02](https://github.com/tonkers-kuma/strategy-rebalancer/commit/936b42941b4a8a4a86db7fd6cd58fc60c3120b02)
  
### Findings:


[Group 1](/group1.pdf), [Group2](/group2.pdf), [Group 3](/group3.pdf)


- Potential for impermanent loss
    - Impermanent loss scales exponentially in a two-sided automated market maker
    - Because volatility begets volatility, the strategy could end up in a situation
    - As a result, if the pool is continuously rebalanced to a fixed value, IL will be realized without the benefit of gains from swaps, or in worst case, repeatedly realizing IL
    - Apparent reason, the strategy could use a volatility index to determine if a pool should be
    - repopulated the pool after pulling out assets in adjust position.
    - (We are still investigating this potential issue)
- Collect Trading Fees Condition Failure 1
    - Joint Provider - prepareReturn LL111-118 can liquidate some of the pool (Rebalancer L311 or L327)
    - Joint Provider - prepareReturn L128 then tries to collect trading fees
    - Rebalancer - collectTradingFees L158 checks that pool balance of A > debt of A (and same for B)
    - The liquidation will change the pool balance, but the debt will not have changed yet. There may be fees that could have been collected if collectTradingFees were called before liquidating some of the pool
- Collect Trading Fees Condition Failure 2
    - Rebalancer - adjustPosition LL256-258 include weight limits
    - Rebalancer - collectTradingFees L158 checks that pool balance of A > debt of A (and same for B)
    - There may be fees available but the condition may evaluate to false if the weight limits have capped what the pool balance can be (e.g., debt A is 95% of total debt, but weight of A is capped at 90%).
- Rebalancer - shouldTend L204 B.getPriceFeed is divided by 10^A.getPriceFeedDecimals
    - Should be B decimals not A decimals
    - Consider adjusting by decimals rather than dividing by decimals to avoid destroying precision
- Rebalancer - adjustPosition LL250-251 is not adjusted for price feed decimals