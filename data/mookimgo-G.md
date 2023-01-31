# 1. add unchecked to PositionMath's addDelta

https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/libraries/PositionMath.sol#L12

```
  function addDelta(uint256 x, int256 y) internal pure returns (uint256 z) {
    if (y < 0) {
      require((z = x - uint256(-y)) < x, "LS");
    } else {
      require((z = x + uint256(y)) >= x, "LA");
    }
  }
```

This function has already checked overflow and underflow, adding unchecked to save gas.