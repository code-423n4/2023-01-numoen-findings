# QA Report
## Finding Summary

[**Non-Critical**](#Non-Critical)
1. [**Power of Ten Literal Not In Scientific Notation**](#1-Power-of-Ten-Literal-Not-In-Scientific-Notation)
2. [**Lack Of Whitespace In Operation**](#2-Lack-Of-Whitespace-In-Operation)
3. [**Order of Functions Not Compliant With Solidity Docs**](#3-Order-of-Functions-Not-Compliant-With-Solidity-Docs)
4. [**Contract Layout Voids Solidity Docs**](#4-Contract-Layout-Voids-Solidity-Docs)
5. [**Long Lines**](#5-Long-Lines)
6. [**No License Indication**](#6-No-License-Indication)
7. [**Contracts Missing @title Tag**](#7-Contracts-Missing-title-Tag)
8. [**Inconsistent Named Returns**](#8-Inconsistent-Named-Returns)
9. [**Spelling Mistake**](#9-Spelling-Mistake)
10. [**Inconsistent Header Spacing**](#10-Inconsistent-Header-Spacing)

# Non-Critical

## 1. Power of Ten Literal Not In Scientific Notation

Power of ten literals > `1e2` are easier to read when expressed in scientific notation. Consider expressing large powers of ten in scientific notation.

*/src/periphery/UniswapV2/libraries/UniswapV2Library.sol*

```solidity
64:	uint256 denominator = (reserveIn * 1000) + amountInWithFee;
80:	uint256 numerator = reserveIn * amountOut * 1000;
```

## 2. Lack Of Whitespace In Operation

The [Solidity Style Guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#names-to-avoid) recommends for exponentiation to have no single whitespace before and non after.

*/src/core/ImmutableState.sol*

```solidity
35:	token0Scale = 10 ** (18 - _token0Exp);
36:	token1Scale = 10 ** (18 - _token1Exp);
```

*/src/libraries/SafeCast.sol*

```solidity
16:	require(y < 2 ** 255);
```

## 3. Order of Functions Not Compliant With Solidity Docs

The Solidity Style Guide suggests the following function order: constructor, receive function (if exists), fallback function (if exists), external, public, internal, private.

The following contracts are not compliant (examples are only to prove the functions are out of order NOT a full description): 

* [JumpRate.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/JumpRate.sol): external functions are positioned after public functions.
* [Payment.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/Payment.sol): external functions are positioned after public functions.
* [Pair.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Pair.sol): external functions are positioned after internal functions.

## 4. Contract Layout Voids Solidity Docs

The [Solidity Style Guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-layout) suggests the following contract layout order: Type declarations, State variables, Events, Modifiers, Functions.

The following contracts are not compliant (examples are only to prove the layout are out of order NOT a full description): 

* [Factory.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Factory.sol): Events are positioned before State.
* [Lendgine.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Lendgine.sol): State is positioned after Events.
* [LiquidityManager.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/LiquidityManager.sol): State is positioned after Events.
* [LendgineRouter.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/LendgineRouter.sol): State is positioned after Events.
* [SwapHelper.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/SwapHelper.sol): State is positioned after Functions.
* [Pair.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Pair.sol): State is positioned after Events.

## 5. Long Lines

Lines with greater length than 120 characters are used. The [Solidity Style Guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-lengthhttps://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-length) suggests that all lines should be 120 characters or less in width.

The following lines are longer than 120 characters, it is suggested to shorten these lines:

*/src/core/Lendgine.sol*
*  [194](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Lendgine.sol#L194). 

*/src/core/JumpRate.sol*
*  [13](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/JumpRate.sol#L13). 

*/src/core/Pair.sol*
*  [116](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Pair.sol#L116). 

## 6. No License Indication

[UniswapV2Library.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol) is missing a license indication. If no license is used `SPDX-License-Identifier: UNLICENSED` should be at the top of the contract.

## 7. Contracts Missing @title Tag

14 out of 15 of the contracts in scope are missing a `@title` tag. Given that 1 contract has a `@title` tag, consider adding one per the 14 remaining contracts.

[Factory.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Factory.sol), [Lendgine.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Lendgine.sol), [LiquidityManager.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/LiquidityManager.sol), [LendgineRouter.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/LendgineRouter.sol), [ImmutableState.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/ImmutableState.sol), [JumpRate.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/JumpRate.sol), [SwapHelper.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/SwapHelper.sol), [Pair.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Pair.sol), [PositionMath.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/libraries/PositionMath.sol), [Balance.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/libraries/Balance.sol), [SafeCast.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/libraries/SafeCast.sol), [LendgineAddress.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/libraries/LendgineAddress.sol), [Position.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/libraries/Position.sol), and [UniswapV2Library.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol) are missing a `@title` tag.

## 8. Inconsistent Named Returns

Some functions use named returns and others do not. It is best for code clearity to keep a consistent style.

1. The following contracts only have named returns (EX. `returns(uint256 foo)`): [Factory.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Factory.sol), [LiquidityManager.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/LiquidityManager.sol), [LendgineRouter.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/LendgineRouter.sol), [JumpRate.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/JumpRate.sol), [SwapHelper.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/SwapHelper.sol), [PositionMath.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/libraries/PositionMath.sol), [SafeCast.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/libraries/SafeCast.sol), [LendgineAddress.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/libraries/LendgineAddress.sol), and [UniswapV2Library.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol).
2. The following contracts only have non-named returns (EX. `returns(uint256)`): [Balance.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/libraries/Balance.sol), and [Position.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/libraries/Position.sol).
3. The following contracts have both: [Lendgine.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Lendgine.sol), and [Pair.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Pair.sol).

## 9. Spelling Mistake

Consider fixing all spelling mistakes.

*/src/periphery/LiquidityManager.sol*

* The word `liquidity` is misspelled as [`liqudity`](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L229).

## 10. Inconsistent Header Spacing

The events header in [LiquidityManager.sol](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L19) does not have a following newline where the event header in [Lendgine.sol](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L24) does. Consider sticking to a single style.



