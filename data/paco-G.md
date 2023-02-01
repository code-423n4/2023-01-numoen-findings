## GAS-1
[GAS-1] use local variable `_totalPositionSize` to save gas

[link to code](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L142)
```
File: src/core/Lendgine.sol
142:     if (totalLiquiditySupplied == 0 && totalPositionSize > 0) revert CompleteUtilizationError();
```
Saves 100 gas.

Gas snapshot diff:
```
❯ diff .gas-snapshot.old .gas-snapshot.new
1,11c1,11
< DepositTest:testAccrueOnDeposit() (gas: 501390)
---
> DepositTest:testAccrueOnDeposit() (gas: 501290)
```

## GAS-2

[GAS-2] removing unnecessary local var and merge if statement

[link to code](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/libraries/Position.sol#L54-L62)

The change diff: 

```
diff --git a/src/core/libraries/Position.sol b/src/core/libraries/Position.sol
index 440b716..06f5c5c 100644
--- a/src/core/libraries/Position.sol
+++ b/src/core/libraries/Position.sol
@@ -51,15 +51,12 @@ library Position {
       tokensOwed = newTokensOwed(_positionInfo, rewardPerPosition);
     }

-    uint256 sizeNext;
     if (sizeDelta == 0) {
       if (_positionInfo.size == 0) revert NoPositionError();
-      sizeNext = _positionInfo.size;
     } else {
-      sizeNext = PositionMath.addDelta(_positionInfo.size, sizeDelta);
+      positionInfo.size = PositionMath.addDelta(_positionInfo.size, sizeDelta);
     }

-    if (sizeDelta != 0) positionInfo.size = sizeNext;
     positionInfo.rewardPerPositionPaid = rewardPerPosition;
     if (tokensOwed > 0) positionInfo.tokensOwed = _positionInfo.tokensOwed + tokensOwed;
   }
```

Saves 66 gas by removing an intermediate variable `sizeNext` and merging two if statements together. Also, the readability is better after changing.

Gas snapshot diff:
```
❯ diff .gas-snapshot.old .gas-snapshot.new
1,11c1,11
< DepositTest:testAccrueOnDeposit() (gas: 501390)
---
> DepositTest:testAccrueOnDeposit() (gas: 501324)
```