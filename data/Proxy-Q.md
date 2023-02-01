# Non critical
## 1.  Use _varName for function arguments to distinguish them from structure variables in struct Parameters.
In function `createLendgine` [src/core/Factory.sol#L63](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Factory.sol#L63) use _token0, _token1, _token0Exp, _token1Exp, _upperBound for function arguments to easily distinguish between function parameters and struct variables.

## 2. Events Mint, Burn, Swap do not contain address indexed sender
In [src/core/Pair.sol#L21-L25](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Pair.sol#L21-L25) events `Mint`, `Burn`, `Swap` should contain `address indexed sender`