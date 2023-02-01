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

# 2. use proxy to create new Lendgine

https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Factory.sol#L82

This code use create2 to create new Lendgine contract, this is a high-cost deploy.

We can change to use minimal proxy (unupgradable) to save deploy gas.