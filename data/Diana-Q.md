
## 01 Use scientific notation rather than exponentiation

Use scientific notation (e.g. 1e18) rather than exponentiation (e.g. `10**18`)

Scientific notation should be used for better code readability

_There are 3 instances of this issue_

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/ImmutableState.sol

```
File: src/core/ImmutableState.sol

35: token0Scale = 10 ** (18 - _token0Exp);
36: token1Scale = 10 ** (18 - _token1Exp);
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/libraries/SafeCast.sol

```
File: src/libraries/SafeCast.sol

16: require(y < 2 ** 255);
```

-----------

## 02 Adding a return statement when the function defines a named return variable, is redundant

_There are 3 instances of this issue_

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol

```
File: src/core/JumpRate.sol

13: function getBorrowRate(uint256 borrowedLiquidity, uint256 totalLiquidity) public pure override returns (uint256 rate) {
25: function getSupplyRate(
40: function utilizationRate(uint256 borrowedLiquidity, uint256 totalLiquidity) private pure returns (uint256 rate) {
```

-------

## 03 Variable names for constants should consist of all capital letters

Constants and immutable variables should be named in all capital letters

_There are 3 instances of this issue_

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol

```
File: src/core/JumpRate.sol

7: uint256 public constant override kink = 0.8 ether;
9: uint256 public constant override multiplier = 1.375 ether;
11: uint256 public constant override jumpMultiplier = 44.5 ether;
```

-------

## 04 Natspec is incomplete

_There are 39 instances of this issue_

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Factory.sol

```
File: src/core/Factory.sol

63-72: function createLendgine(
    address token0,
    address token1,
    uint8 token0Exp,
    uint8 token1Exp,
    uint256 upperBound
  )
    external
    override
    returns (address lendgine)
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol

```
File: src/core/JumpRate.sol

13: function getBorrowRate(uint256 borrowedLiquidity, uint256 totalLiquidity) public pure override returns (uint256 rate) {

25-32: function getSupplyRate(
    uint256 borrowedLiquidity,
    uint256 totalLiquidity
  )
    external
    pure
    override
    returns (uint256 rate)
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol

```
File: src/core/Lendgine.sol

71-79: function mint(
    address to,
    uint256 collateral,
    bytes calldata data
  )
    external
    override
    nonReentrant
    returns (uint256 shares)

123-131: function deposit(
    address to,
    uint256 liquidity,
    bytes calldata data
  )
    external
    override
    nonReentrant
    returns (uint256 size)

194: function collect(address to, uint256 collateralRequested) external override nonReentrant returns (uint256 collateral) {

213: function convertLiquidityToShare(uint256 liquidity) public view override returns (uint256) {

219: function convertShareToLiquidity(uint256 shares) public view override returns (uint256) {

224: function convertCollateralToLiquidity(uint256 collateral) public view override returns (uint256) {

229: function convertLiquidityToCollateral(uint256 liquidity) public view override returns (uint256) {
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Pair.sol

```
File: src/core/Pair.sol

53: function invariant(uint256 amount0, uint256 amount1, uint256 liquidity) public view override returns (bool) {

70: function mint(uint256 liquidity, bytes calldata data) internal {

93: function burn(address to, uint256 liquidity) internal returns (uint256 amount0, uint256 amount1) {

116: function swap(address to, uint256 amount0Out, uint256 amount1Out, bytes calldata data) external override nonReentrant {
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/libraries/Position.sol

```
File: src/core/libraries/Position.sol

69: function newTokensOwed(Position.Info memory position, uint256 rewardPerPosition) internal pure returns (uint256) {

73-80: function convertLiquidityToPosition(
    uint256 liquidity,
    uint256 totalLiquiditySupplied,
    uint256 totalPositionSize
  )
    internal
    pure
    returns (uint256)

86-93: function convertPositionToLiquidity( 
    uint256 position,
    uint256 totalLiquiditySupplied,
    uint256 totalPositionSize
  )
    internal
    pure
    returns (uint256)
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/libraries/Balance.sol

```
File: src/libraries/Balance.sol

12: function balance(address token) internal view returns (uint256) {
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/libraries/SafeCast.sol

```
File: src/libraries/SafeCast.sol

8: function toUint120(uint256 y) internal pure returns (uint120 z) {
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LendgineRouter.sol

```
File: src/periphery/LendgineRouter.sol

87-93: function mintCallback(
    uint256 collateralTotal,
    uint256 amount0,
    uint256 amount1,
    uint256,
    bytes calldata data
  )

190: function pairMintCallback(uint256 liquidity, bytes calldata data) external override {

257: function burn(BurnParams calldata params) external payable checkDeadline(params.deadline) returns (uint256 amount) {
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol

```
File: src/periphery/LiquidityManager.sol

104: function pairMintCallback(uint256, bytes calldata data) external {

135: function addLiquidity(AddLiquidityParams calldata params) external payable checkDeadline(params.deadline) {

201: function removeLiquidity(RemoveLiquidityParams calldata params) external payable checkDeadline(params.deadline) {

230: function collect(CollectParams calldata params) external payable returns (uint256 amount) {
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/Payment.sol

```
File: src/periphery/Payment.sol

25: function unwrapWETH(uint256 amountMinimum, address recipient) public payable {

35: function sweepToken(address token, uint256 amountMinimum, address recipient) public payable {
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/SwapHelper.sol

```
File: src/periphery/SwapHelper.sol

38: function uniswapV3SwapCallback(int256 amount0Delta, int256 amount1Delta, bytes calldata data) external override {
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/libraries/LendgineAddress.sol

```
File: src/periphery/libraries/LendgineAddress.sol

9-19: function computeAddress(
    address factory,
    address token0,
    address token1,
    uint256 token0Exp,
    uint256 token1Exp,
    uint256 upperBound
  )
    internal
    pure
    returns (address lendgine)
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol

```
File: src/periphery/UniswapV2/libraries/UniswapV2Library.sol

10: function sortTokens(address tokenA, address tokenB) internal pure returns (address token0, address token1) {

17: function pairFor(address factory, address tokenA, address tokenB) internal pure returns (address pair) {

36-43: function getReserves(
    address factory,
    address tokenA,
    address tokenB
  )
    internal
    view
    returns (uint256 reserveA, uint256 reserveB)

51-58: function getAmountOut( 
    uint256 amountIn,
    uint256 reserveIn,
    uint256 reserveOut
  )
    internal
    pure
    returns (uint256 amountOut)

69-76: function getAmountIn( 
    uint256 amountOut,
    uint256 reserveIn,
    uint256 reserveOut
  )
    internal
    pure
    returns (uint256 amountIn)
```

-----

## 05 Use a more recent version of solidity

It's a best practice to use the latest compiler version.

The specified minimum compiler version is quite old. Older compilers might be susceptible to some bugs. We recommend changing the solidity version pragma to the latest version to enforce the use of an up to date compiler.

List of known compiler bugs and their severity can be found here: [https://etherscan.io/solcbuginfo](https://etherscan.io/solcbuginfo)

_This issue exists in all the In-scope contracts_. _There are 15 instances of this issue_

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

--------

## 06 Non-library or interface files should use fixed compiler versions, not floating ones

In the contracts, floating pragmas should not be used. Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

_This issue exists in all the In-scope contracts_. _There are 15 instances of this issue_

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

-----------

