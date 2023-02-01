# Gas Report
## Finding Summary

1. [**uint256 Not Used**](#1-uint256-Not-Used)
3. [**&& In Require Statements**](#2--In-Require-Statements)
4. [**&& In If Statements**](#3--In-If-Statements)
5. [**x += y Used For State Variables**](#4-x--y-Used-For-State-Variables)
6. [**Error Checks Can Be Made Earlier**](#5-Error-Checks-Can-Be-Made-Earlier)
7. [**x -= y Used For State Variables**](#6-x---y-Used-For-State-Variables)

## 1. uint256 Not Used

The EVM deals with 32 bytes at a time. For `uint<size>`s less than 32 bytes the EVM performs more operations as a result of the needed size reduction from `uint256` to `uint<size>`. Consider using `uint256`.

*/src/periphery/SwapHelper.sol*

```solidity
62:	uint24 fee;
```

*/src/core/Pair.sol*

```solidity
40:	uint120 public override reserve0;
43:	uint120 public override reserve1;
```

## 2. && In Require Statements

Seperating require statements saves gas.

**Example**
```solidity
//From
require(a != HIGH && b != LOW);
//To
require(a != HIGH);
require(b != LOW);
```

*/src/periphery/UniswapV2/libraries/UniswapV2Library.sol*

```solidity
61:	require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");
79:	require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");
```

## 3. && In If Statements

Seperating if statements saves gas.

**Example**
```solidity
//From
if (a != HIGH && b != LOW) {
	//Do Something
}
//To
if (a != HIGH) {
	if (b != LOW) {
		//Do Something
	}
}
```

*/src/core/Lendgine.sol*

```solidity
89:	if (totalSupply > 0 && totalLiquidityBorrowed == 0) revert CompleteUtilizationError();
142:	if (totalLiquiditySupplied == 0 && totalPositionSize > 0) revert CompleteUtilizationError();
```

*/src/periphery/Payment.sol*

```solidity
53:	if (token == weth && address(this).balance >= value) {
```

*/src/core/Pair.sol*

```solidity
54:	if (liquidity == 0) return (amount0 == 0 && amount1 == 0);
100:	if (amount0 == 0 && amount1 == 0) revert InsufficientOutputError();
117:	if (amount0Out == 0 && amount1Out == 0) revert InsufficientOutputError();
```

## 4. x += y Used For State Variables

When dealing with state variables `x = x + y` is cheaper than `x += y`

*/src/core/Lendgine.sol*

```solidity
91:	totalLiquidityBorrowed += liquidity;
257:	rewardPerPositionStored += FullMath.mulDiv(dilutionSpeculative, 1e18, totalPositionSize);
```
**Suggested Change**
```solidity
91:	totalLiquidityBorrowed = totalLiquidityBorrowed + liquidity;
257:	rewardPerPositionStored = rewardPerPositionStored + FullMath.mulDiv(dilutionSpeculative, 1e18, totalPositionSize);
```

*/src/periphery/LiquidityManager.sol*

```solidity
178:	position.tokensOwed += FullMath.mulDiv(position.size, rewardPerPositionPaid - position.rewardPerPositionPaid, 1e18);
180:	position.size += size;
214:	position.tokensOwed += FullMath.mulDiv(position.size, rewardPerPositionPaid - position.rewardPerPositionPaid, 1e18);
238:	position.tokensOwed += FullMath.mulDiv(position.size, rewardPerPositionPaid - position.rewardPerPositionPaid, 1e18);
```
**Suggested Change**
```solidity
178:	position.tokensOwed = position.tokensOwed + FullMath.mulDiv(position.size, rewardPerPositionPaid - position.rewardPerPositionPaid, 1e18);
180:	position.size = position.size + size;
214:	position.tokensOwed = position.tokensOwed + FullMath.mulDiv(position.size, rewardPerPositionPaid - position.rewardPerPositionPaid, 1e18);
238:	position.tokensOwed = position.tokensOwed + FullMath.mulDiv(position.size, rewardPerPositionPaid - position.rewardPerPositionPaid, 1e18);
```

## 5. Error Checks Can Be Made Earlier 

Error checks should revert as early as possible to prevent unnecessary computation. 

*/src/core/Lendgine.sol*

* On [L140](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L140) and [L170](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L170) `liquidity == 0` is checked. `liquidity` is a parameter and therefore can be checked at the very beginning of `deposit` and `withdraw`.

## 6. x -= y Used For State Variables

When dealing with state variables `x = x - y` is cheaper than `x -= y`.

*/src/core/Lendgine.sol*

```solidity
114:	totalLiquidityBorrowed -= liquidity;
176:	totalPositionSize -= size;
```
**Suggested Change**
```solidity
114:	totalLiquidityBorrowed = totalLiquidityBorrowed - liquidity;
176:	totalPositionSize = totalPositionSize - size;
```

*/src/periphery/LiquidityManager.sol*

```solidity
216:	position.size -= params.size;
242:	position.tokensOwed -= amount;
```
**Suggested Change**
```solidity
216:	position.size = position.size - params.size;
242:	position.tokensOwed = position.tokensOwed - amount;
```