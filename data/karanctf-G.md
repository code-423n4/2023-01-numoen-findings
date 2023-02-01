
gas

## [G-1]
In contract [JumpRate.sol](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol#L40)  in below code snipit on funciton `utilizationRate()` when `totalLiquidity == 0` it returns 0 and this function is called on first line of `getBorrowRate()` which sets the returned value of `utilizationRate()` to util then in next line it will return `0` as value of `util` will be `0`.

So instead of checking for `if (totalLiquidity == 0) return 0;` on private function `utilizationRate` use this on first line of `getBorrowRate()` funtion


```diff
function getBorrowRate(uint256 borrowedLiquidity, uint256 totalLiquidity) public pure override returns (uint256 rate) {
++	if (totalLiquidity == 0) return 0;
    uint256 util = utilizationRate(borrowedLiquidity, totalLiquidity);

    if (util <= kink) {
      return (util * multiplier) / 1e18;
    } else {
      uint256 normalRate = (kink * multiplier) / 1e18;
      uint256 excessUtil = util - kink;
      return ((excessUtil * jumpMultiplier) / 1e18) + normalRate;
    }
  }

  function getSupplyRate(
    uint256 borrowedLiquidity,
    uint256 totalLiquidity
  )
    external
    pure
    override
    returns (uint256 rate)
  {
++  if (totalLiquidity == 0) return 0;
    uint256 util = utilizationRate(borrowedLiquidity, totalLiquidity);
    uint256 borrowRate = getBorrowRate(borrowedLiquidity, totalLiquidity);

    return (borrowRate * util) / 1e18;
  }

  function utilizationRate(uint256 borrowedLiquidity, uint256 totalLiquidity) private pure returns (uint256 rate) {
--  if (totalLiquidity == 0) return 0;
    return (borrowedLiquidity * 1e18) / totalLiquidity;
  }
}
```
## [G-2]
In contract SafeCast.sol use bitshift insted of exponent saves 22 gas.

```diff
  function toInt256(uint256 y) internal pure returns (int256 z) {
--  require(y < 2 ** 255);// costs 21401 gas
++  require(y < 1 << 255);// costs 21379 gas
    z = int256(y);
  }
```

