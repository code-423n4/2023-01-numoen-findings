

|Sno.|Issue|Instances|Gas Savings|
|---|---|---|---|
|[G-01]|X += Y COSTS MORE GAS THAN X = X + Y FOR STATE VARIABLES|10|1130
|[G-02]|SPLITTING REQUIRE() STATEMENTS THAT USE && SAVES GAS|2|6
|[G-03]|DUPLICATED REQUIRE() or REVERT() CHECKS SHOULD BE REFACTORED TO A MODIFIER OR FUNCTION|2|20
|[G-04]|USING BOTH NAMED RETURNS AND A RETURN STATEMENT ISNT NECESSARY|3|9
|[G-05]|INTERNAL FUNCTIONS ONLY CALLED ONCE CAN BE INLINED TO SAVE GAS|6|240



### G-01 X += Y COSTS MORE GAS THAN X = X + Y FOR STATE VARIABLES

*Number of Instances Identified: 10*

Using the addition operator instead of plus-equals saves **[113 gas]([https://gist.github.com/IllIllI000/cbbfb267425b898e5be734d4008d4fe8)**

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol

```
91: totalLiquidityBorrowed += liquidity;
114: totalLiquidityBorrowed -= liquidity;
176: totalPositionSize -= size;
257: rewardPerPositionStored += FullMath.mulDiv(dilutionSpeculative, 1e18, totalPositionSize);
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol

```
178: position.tokensOwed += FullMath.mulDiv(position.size, rewardPerPositionPaid - position.rewardPerPositionPaid, 1e18);
180: position.size += size;
214: position.tokensOwed += FullMath.mulDiv(position.size, rewardPerPositionPaid - position.rewardPerPositionPaid, 1e18);
216: position.size -= params.size;
238: position.tokensOwed += FullMath.mulDiv(position.size, rewardPerPositionPaid - position.rewardPerPositionPaid, 1e18);
242: position.tokensOwed -= amount;
```


### G-02 SPLITTING REQUIRE() STATEMENTS THAT USE && SAVES GAS

*Number of Instances Identified: 2*

See [this issue](https://github.com/code-423n4/2022-01-xdefi-findings/issues/128) which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper by **3 gas**

https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol

```
61: require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");
79: require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");
```


### G-03 DUPLICATED REQUIRE() or REVERT() CHECKS SHOULD BE REFACTORED TO A MODIFIER OR FUNCTION

*Number of Instances Identified: 2*

Saves deployment costs

```
61: require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");
79: require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");
```


### G-04 USING BOTH NAMED RETURNS AND A RETURN STATEMENT ISNT NECESSARY

*Number of Instances Identified: 3*

Removing unused named returns variables can reduce gas usage (MSTOREs/MLOADs) and improve code clarity. To save gas and improve code quality: consider using only one of those.
Each MLOAD and MSTORE costs `3 additional gas`

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol

```
13-23: function getBorrowRate(uint256 borrowedLiquidity, uint256 totalLiquidity) public pure override returns (uint256 rate) { 
    uint256 util = utilizationRate(borrowedLiquidity, totalLiquidity);

    if (util <= kink) {
      return (util * multiplier) / 1e18;
    } else {
      uint256 normalRate = (kink * multiplier) / 1e18;
      uint256 excessUtil = util - kink;
      return ((excessUtil * jumpMultiplier) / 1e18) + normalRate;
    }
  }

25-38: unction getSupplyRate(
    uint256 borrowedLiquidity,
    uint256 totalLiquidity
  )
    external
    pure
    override
    returns (uint256 rate)
  {
    uint256 util = utilizationRate(borrowedLiquidity, totalLiquidity);
    uint256 borrowRate = getBorrowRate(borrowedLiquidity, totalLiquidity);

    return (borrowRate * util) / 1e18;
  }

40-43: function utilizationRate(uint256 borrowedLiquidity, uint256 totalLiquidity) private pure returns (uint256 rate) { 
    if (totalLiquidity == 0) return 0;
    return (borrowedLiquidity * 1e18) / totalLiquidity;
  }
```


### G-05 INTERNAL FUNCTIONS ONLY CALLED ONCE CAN BE INLINED TO SAVE GAS

*Number of Instances Identified: 6*

Not inlining costs **20 to 40 gas** because of two extra `JUMP` instructions and additional stack operations needed for function calls.

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/libraries/Position.sol

```
69: function newTokensOwed(Position.Info memory position, uint256 rewardPerPosition) internal

73-78: function convertLiquidityToPosition(
    uint256 liquidity,
    uint256 totalLiquiditySupplied,
    uint256 totalPositionSize
  )
    internal

86-91: function convertPositionToLiquidity(
    uint256 position,
    uint256 totalLiquiditySupplied,
    uint256 totalPositionSize
  )
    internal
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/libraries/PositionMath.sol

```
12: function addDelta(uint256 x, int256 y) internal
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol

```
51-56: function getAmountOut(
    uint256 amountIn,
    uint256 reserveIn,
    uint256 reserveOut
  )
    internal

69-74: function getAmountIn(
    uint256 amountOut,
    uint256 reserveIn,
    uint256 reserveOut
  )
    internal
```