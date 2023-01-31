# Numoen report by Timenov

## Summary
G-01 Using `x += y` costs more gas than using `x = x + y`.
G-02 Some checks can be moved up.

### [G-01] Using `x += y` costs more gas than using `x = x + y`.
*There is 10 instances of this issue.*

```solidity
File: core/Lendgine.sol

91: totalLiquidityBorrowed += liquidity;
114: totalLiquidityBorrowed -= liquidity;
176: totalPositionSize -= size;
257: rewardPerPositionStored += FullMath.mulDiv(dilutionSpeculative, 1e18, totalPositionSize);
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol

```solidity
File: periphery/LiquidityManager.sol

178: position.tokensOwed += FullMath.mulDiv(position.size, rewardPerPositionPaid - position.rewardPerPositionPaid, 1e18);
180: position.size += size;
214: position.tokensOwed += FullMath.mulDiv(position.size, rewardPerPositionPaid - position.rewardPerPositionPaid, 1e18);
216: position.size -= params.size;
238: position.tokensOwed += FullMath.mulDiv(position.size, rewardPerPositionPaid - position.rewardPerPositionPaid, 1e18);
242: position.tokensOwed -= amount;
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol

### [G-02] Some checks can be moved up.
Some of the checks can be moved up in cases where we are sure that the check will pass.
*There is 1 instances of this issue.*

```solidity
File: periphery/LiquidityManager.sol

147:
    if (totalLiquidity == 0) {
      amount0 = params.amount0Min;
      amount1 = params.amount1Min;
    } else {
      amount0 = FullMath.mulDivRoundingUp(params.liquidity, r0, totalLiquidity);
      amount1 = FullMath.mulDivRoundingUp(params.liquidity, r1, totalLiquidity);
    }

    if (amount0 < params.amount0Min || amount1 < params.amount1Min) revert AmountError();
```

Can be written like this:

```solidity

147:
    if (totalLiquidity == 0) {
      amount0 = params.amount0Min;
      amount1 = params.amount1Min;
    } else {
      amount0 = FullMath.mulDivRoundingUp(params.liquidity, r0, totalLiquidity);
      amount1 = FullMath.mulDivRoundingUp(params.liquidity, r1, totalLiquidity);

      if (amount0 < params.amount0Min || amount1 < params.amount1Min) revert AmountError();
    }
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol