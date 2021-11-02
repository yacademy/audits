### Codebase: 

[Rari Capital Vaults](https://github.com/Rari-Capital/vaults), commit [ce4383a6bb5380069e84a746d50c68b7b5d9482a](https://github.com/Rari-Capital/vaults/commit/ce4383a6bb5380069e84a746d50c68b7b5d9482a)
  

### Findings:

- Context Issues
    - Use of msg.sender throughout - vault should better control context in general msgSender()
    - No way to account for tokens sent to the strategy by means not pre-determined by vault logic. Whether through air drops or mistakes, capturing value that enters the vault ecosystem unpredictably is quality UX
    - In lockedProfit, profitUnlockDelay can be changed before all existing profits are unlocked, either put a cap on profitUnlockDelay or require block.timestamp>=lastHarvest+profitUnlockDelay before being able to update
- Withdraw/Redeem
    - Withdraw/redeem do not call harvest or otherwise check whether there were losses or gains before allowing people to withdraw/redeem. At best, users have to remember to call harvest before they withdraw/redeem. At worst, this allows users to skip out on any losses that may have occurred before they withdraw/redeem.
- Harvest / trust system / auth
    - Withdraw/redeem do not call harvest or otherwise check whether there were losses or gains before allowing people to withdraw/redeem. At best, users have to remember to call harvest before they withdraw/redeem. At worst, this allows users to skip out on any losses that may have occurred before they withdraw/redeem.
- deposit/withdraw to/from strategy
    - Should maybe not allow invalid deposits/withdraws - validate inputs
    - There should be a way to validate the totalStrategyHoldings before updating state - a function that recalcs every time by calling dependencies and whatnot and determining how many tokens are TRULY in the system
    - WTF is mint???? There is approval but no transfer
    - There isn’t any pushing or popping of strategies from the queue, so a lot of dependent processes are disjointed
    - Same issues for withdraw
- Conclusion
    - It seems like the designer favored modularity and encapsulation more than security. A lot of the accounting and state changes need to be handled manually by a trusted centralized party, which removes a lot of the decentralized, anti-fragile elements of the original Yearn V1 system. We believe tradeoffs should be better analyzed with security and reliability in mind as opposed to granularity and modularity. It’s definitely respectable how organized the system is, but there are a lot of benefits to monolithic architectures that are lost along the way.