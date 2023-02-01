| S No. | Issue | Instances | Gas Savings (from provided tests) |
|-----|-----|-----|-----|
| [G-01] | x += y costs more gas than x = x + y for state variables (same applies for x -= y)| 10 | 1130
| [G-02] | Internal functions only called once can be inlined to save gas | 6 | 240
| [G-03] | Not using the named return variables when a function returns, wastes deployment gas | 3 | 9
| [G-04] | Splitting require() statements that use && saves gas | 2 | 6
| [G-05] | Use a more recent version of solidity | 1 | -
| [G-06] | Duplicated require() or revert() checks should be refactored to a modifier or function | 2 | 10

## G-01 x += y costs more gas than x = x + y for state variables (same applies for x -= y)

Using the addition operator instead of plus-equals saves **[113 gas](https://gist.github.com/IllIllI000/cbbfb267425b898e5be734d4008d4fe8)**.

_There are 10 instances of this issue_

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol

```
File: src/core/Lendgine.sol

91: totalLiquidityBorrowed += liquidity;
114: totalLiquidityBorrowed -= liquidity;
176: totalPositionSize -= size;
257: rewardPerPositionStored += FullMath.mulDiv(dilutionSpeculative, 1e18, totalPositionSize);
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol

```
File: src/periphery/LiquidityManager.sol

178: position.tokensOwed += FullMath.mulDiv(position.size, rewardPerPositionPaid - position.rewardPerPositionPaid, 1e18);
180: position.size += size;
214: position.tokensOwed += FullMath.mulDiv(position.size, rewardPerPositionPaid - position.rewardPerPositionPaid, 1e18);
216: position.size -= params.size;
238: position.tokensOwed += FullMath.mulDiv(position.size, rewardPerPositionPaid - position.rewardPerPositionPaid, 1e18);
242: position.tokensOwed -= amount;
```

------

## G-02 Internal functions only called once can be inlined to save gas

Not inlining costs **20 to 40 gas** because of two extra `JUMP` instructions and additional stack operations needed for function calls.

_There are 6 instances of this issue_

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/libraries/Position.sol

```
File: src/core/libraries/Position.sol

69: function newTokensOwed(Position.Info memory position, uint256 rewardPerPosition) internal pure returns (uint256) {
73: function convertLiquidityToPosition(
86: function convertPositionToLiquidity(
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/libraries/PositionMath.sol

```
File: src/core/libraries/PositionMath.sol

12: function addDelta(uint256 x, int256 y) internal pure returns (uint256 z) {
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol

```
File: src/periphery/UniswapV2/libraries/UniswapV2Library.sol

51: function getAmountOut(
69: function getAmountIn(
```

-----

## G-03 Not using the named return variables when a function returns, wastes deployment gas

Each MLOAD and MSTORE costs **3 additional gas**

_There are 3 instances of this issue_

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol

```
File: src/core/JumpRate.sol

13: function getBorrowRate(uint256 borrowedLiquidity, uint256 totalLiquidity) public pure override returns (uint256 rate) {
25: function getSupplyRate(
40: function utilizationRate(uint256 borrowedLiquidity, uint256 totalLiquidity) private pure returns (uint256 rate) {
```

---

## G-04 Splitting require() statements that use && saves gas

See [this issue](https://github.com/code-423n4/2022-01-xdefi-findings/issues/128) which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper by **3 gas**

_There are 2 instances of this issue_

https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol

```
File: src/periphery/UniswapV2/libraries/UniswapV2Library.sol

61: require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");
79: require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");
```

------------

## G-05 Use a more recent version of solidity

Use a solidity version of at least 0.8.2 to get compiler automatic inlining  
Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads  
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than `revert()/require()` strings  
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

_There are 15 instances of this issue_

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/libraries/Position.sol

```
File: src/core/libraries/Position.sol

2: pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/libraries/PositionMath.sol

```
File: src/core/libraries/PositionMath.sol

2: pragma solidity >=0.5.0;
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Factory.sol

```
File: src/core/Factory.sol

2: pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/ImmutableState.sol

```
File: src/core/ImmutableState.sol

2: pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol

```
File: src/core/JumpRate.sol

2: pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol

```
File: src/core/Lendgine.sol

2: pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Pair.sol

```
File: src/core/Pair.sol

2: pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/libraries/Balance.sol

```
File: src/libraries/Balance.sol

2: pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/libraries/SafeCast.sol

```
File: src/libraries/SafeCast.sol

2: pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/libraries/LendgineAddress.sol

```
File: src/periphery/libraries/LendgineAddress.sol

2: pragma solidity >=0.5.0;
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol

```
File: src/periphery/UniswapV2/libraries/UniswapV2Library.sol

1: pragma solidity >=0.8.0;
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LendgineRouter.sol

```
File: src/periphery/LendgineRouter.sol

2: pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol

```
File: src/periphery/LiquidityManager.sol

2: pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/Payment.sol

```
File: src/periphery/Payment.sol

2: pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/SwapHelper.sol

```
File: src/periphery/SwapHelper.sol

2: pragma solidity ^0.8.4;
```

-----

## G-06 Duplicated require() or revert() checks should be refactored to a modifier or function

Saves deployment costs.

_There are 2 instances of this issue_

https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol

```
File: src/periphery/UniswapV2/libraries/UniswapV2Library.sol

61: require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");
79: require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");
```

------
