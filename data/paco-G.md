```
File: src/core/Lendgine.sol
142:     if (totalLiquiditySupplied == 0 && totalPositionSize > 0) revert CompleteUtilizationError();
```

can be changed to use stack variable for gas optimization

```
142:     if (totalLiquiditySupplied == 0 && _totalPositionSize > 0) revert CompleteUtilizationError();
```

This saves 100 gas, the gas snapshot diff is below:

```
❯ diff .gas-snapshot.old .gas-snapshot.new
1,11c1,11
< DepositTest:testAccrueOnDeposit() (gas: 501390)
< DepositTest:testAccrueOnDepositEmpty() (gas: 266681)
< DepositTest:testAccrueOnPositionDeposit() (gas: 526356)
< DepositTest:testBasicDeposit() (gas: 272276)
< DepositTest:testDepositAfterFullAccrue() (gas: 415719)
< DepositTest:testEmitLendgine() (gas: 268356)
< DepositTest:testEmitPair() (gas: 267836)
< DepositTest:testNonStandardDecimals() (gas: 3054155)
< DepositTest:testOverDeposit() (gas: 272302)
< DepositTest:testProportionPositionSize() (gas: 505264)
< DepositTest:testUnderPayment() (gas: 246454)
---
> DepositTest:testAccrueOnDeposit() (gas: 501290)
> DepositTest:testAccrueOnDepositEmpty() (gas: 266601)
> DepositTest:testAccrueOnPositionDeposit() (gas: 526276)
> DepositTest:testBasicDeposit() (gas: 272196)
> DepositTest:testDepositAfterFullAccrue() (gas: 415559)
> DepositTest:testEmitLendgine() (gas: 268276)
> DepositTest:testEmitPair() (gas: 267756)
> DepositTest:testNonStandardDecimals() (gas: 3053641)
> DepositTest:testOverDeposit() (gas: 272222)
> DepositTest:testProportionPositionSize() (gas: 505164)
> DepositTest:testUnderPayment() (gas: 246374)
```