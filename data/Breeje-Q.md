## QA Report

| |Issue|Instances|
|-|:-|:-:|
| [L-1](#L-1) | USE OF FLOATING PRAGMA IN FILES OUTSIDE ONES IN AUTOMATED FINDINGS | 12 |
| [L-2](#L-2) | DIVISION BEFORE MULTIPLICATION ERROR | 3 |
| [NC-1](#NC-1) | AVOID VARIABLE NAMES THAT CAN SHADE | 4 |
| [NC-2](#NC-2) | NATSPEC COMMENTS MISSING | 9 |
| [NC-3](#NC-3) | SPDX LICENSE IDENTIFIER NOT PROVIDED | 1 |

### [L-1] USE OF FLOATING PRAGMA IN FILES OUTSIDE ONES IN AUTOMATED FINDINGS

Impact: [swcregistry](https://swcregistry.io/docs/SWC-103)

*Instances (12):*

All File in Scope outside the ones already found in Automated Findings uses `pragma solidity ^0.8.4;` Floating pragma Solidity Version.

### [L-2] DIVISION BEFORE MULTIPLICATION ERROR

Division before multiplication can lead to Huge Precision Error that can lead to the protocol functioning incorrectly.

*Instances (3):*
```solidity
File: Lendgine.sol

252:      uint256 dilutionLPRequested = (FullMath.mulDiv(borrowRate, _totalLiquidityBorrowed, 1e18) * timeElapsed) / 365 days;

```
[Link to Code](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L252)

```solidity
File: Pair.sol

56:      uint256 scale0 = FullMath.mulDiv(amount0, 1e18, liquidity) * token0Scale;
57:      uint256 scale1 = FullMath.mulDiv(amount1, 1e18, liquidity) * token1Scale;

```
[Link to Code](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Pair.sol#L56-L57)

### [NC-1] AVOID VARIABLE NAMES THAT CAN SHADE

*Instances (4):*
```solidity
File: src/core/Factory.sol

80:    Parameters({token0: token0, token1: token1, token0Exp: token0Exp, token1Exp: token1Exp, upperBound: upperBound});

```
[Link to Code](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Factory.sol#L80)

```solidity
File: src/periphery/LiquidityManager.sol

167:      amount0: amount0,
168:      amount1: amount1,

```
[Link to Code](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L167-L168)

```solidity
File: src/periphery/Payment.sol

55:      IWETH9(weth).deposit{value: value}(); // wrap only what is needed to pay

```
[Link to Code](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/Payment.sol#L55)

### [NC-2] NATSPEC COMMENTS MISSING

Natspec Comments are missing the Following Functions:

*Instances (9):*
* [Position.sol: `convertLiquidityToPosition`](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/libraries/Position.sol#L73-L84)
* [Position.sol: `convertPositionToLiquidity`](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/libraries/Position.sol#L86-L97)
* [JumpRate.sol: `getBorrowRate`](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol#L13-L23)
* [JumpRate.sol: `getSupplyRate`](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol#L25-L38)
* [JumpRate.sol: `utilizationRate`](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol#L40-L44)
* [Payment.sol: `unwrapWETH`](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/Payment.sol#L25-L33)
* [Payment.sol: `sweepToken`](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/Payment.sol#L35-L42)
* [Payment.sol: `refundETH`](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/Payment.sol#L44-L46)
* [ILendgine.sol: `burn`: @return missing](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/interfaces/ILendgine.sol#L38)

### [NC-3] SPDX LICENSE IDENTIFIER NOT PROVIDED

SPDX license identifier not provided in source file. Before publishing, consider adding a comment containing "SPDX-License-Identifier: <SPDX-License>" to each source file. Please see https://spdx.org for more information.

*Instance (1):*
* [`UniswapV2Library.sol`](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L1)