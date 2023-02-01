
# Gas

## `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables

Using the addition operator instead of plus-equals saves gas. Same goes for other operands like `-=`.

- [Lendgine.sol#L91](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Lendgine.sol#L91)
    totalLiquidityBorrowed += liquidity;

- [Lendgine.sol#L257](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Lendgine.sol#L257)
    rewardPerPositionStored += FullMath.mulDiv(dilutionSpeculative, 1e18, totalPositionSize);

- [Lendgine.sol#L114](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Lendgine.sol#L114)
    totalLiquidityBorrowed -= liquidity;

- [Lendgine.sol#L176](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Lendgine.sol#L176)
    totalPositionSize -= size;


## Not using the named return variables when a function returns, wastes deployment gas


- [JumpRate.sol#L13](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/JumpRate.sol#L13)
  function getBorrowRate(uint256 borrowedLiquidity, uint256 totalLiquidity) public pure override returns (uint256 rate) {

- [JumpRate.sol#L32](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/JumpRate.sol#L32)
    returns (uint256 rate)

- [JumpRate.sol#L40](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/JumpRate.sol#L40)
  function utilizationRate(uint256 borrowedLiquidity, uint256 totalLiquidity) private pure returns (uint256 rate) {


## `internal` functions only called once can be inlined to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

`newTokensOwed`

- [Position.sol#L69](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/libraries/Position.sol#L69)
  function newTokensOwed(Position.Info memory position, uint256 rewardPerPosition) internal pure returns (uint256) {

`addDelta`

- [PositionMath.sol#L12](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/libraries/PositionMath.sol#L12)
  function addDelta(uint256 x, int256 y) internal pure returns (uint256 z) {








## `abi.encode()` is less gas efficient than `abi.encodePacked()`

Changing the abi.encode function to `abi.encodePacked` can save gas since the `abi.encode` function pads extra null `bytes` at the end of the call data, which is unnecessary. Also, in general, `abi.encodePacked` is more gas-efficient.

- [Factory.sol#L82](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Factory.sol#L82)
    lendgine = address(new Lendgine{ salt: keccak256(abi.encode(token0, token1, token0Exp, token1Exp, upperBound)) }());

- [LiquidityManager.sol#L160](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/LiquidityManager.sol#L160)
      abi.encode(

- [LendgineRouter.sol#L150](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/LendgineRouter.sol#L150)
      abi.encode(

- [LendgineRouter.sol#L268](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/LendgineRouter.sol#L268)
      abi.encode(

- [SwapHelper.sol#L108](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/SwapHelper.sol#L108)
        abi.encode(params.tokenIn)

- [LendgineAddress.sol#L28](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/libraries/LendgineAddress.sol#L28)
              keccak256(abi.encode(token0, token1, token0Exp, token1Exp, upperBound)),







## Duplicated `require()` check should be refactored

Duplicated `require()` / `revert()` checks should be refactored to a modifier or function to save gas. If it's been used for example `require(addr != address(0))` or `require(a > b)`, etc multiple times in the same contract. This can be refactored to a modifier like `notZeroAddress(address a)`, function `biggerThan(uint a, uint b)`, etc.

- [UniswapV2Library.sol#L61](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L61)
    require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");

- [UniswapV2Library.sol#L79](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L79)
    require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");



## Splitting `require()` statements that use `&&` saves gas

Instead of using the `&&` operator in a single require statement to check multiple conditions, consider using multiple require statements with 1 condition per require statement (saving 3 gas per `&`).


- [UniswapV2Library.sol#L61](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L61)
    require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");

- [UniswapV2Library.sol#L79](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L79)
    require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");


## Use Custom Errors

`NOTE`: None of these findings where found by [4naly3er output - Gas](LINKKKKKKK)

[Source](https://blog.soliditylang.org/2021/04/21/custom-errors/) Instead of using error strings, to reduce deployment and runtime cost, you should use Custom Errors. This would save both deployment and runtime cost.


- [SwapHelper.sol#L116](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/SwapHelper.sol#L116)
        require(amountOutReceived == params.amount);


- [SafeCast.sol#L9](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/libraries/SafeCast.sol#L9)
    require((z = uint120(y)) == y);

- [SafeCast.sol#L16](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/libraries/SafeCast.sol#L16)
    require(y < 2 ** 255);




## Use calldata instead of memory for function arguments that do not get mutated

`NOTE`: None of these findings where found by [4naly3er output - Gas](LINKKKKKKK)


- [SwapHelper.sol#L69](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/SwapHelper.sol#L69)
  function swap(SwapType swapType, SwapParams memory params, bytes memory data) internal returns (uint256 amount) {

- [Position.sol#L69](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/libraries/Position.sol#L69)
  function newTokensOwed(Position.Info memory position, uint256 rewardPerPosition) internal pure returns (uint256) {

