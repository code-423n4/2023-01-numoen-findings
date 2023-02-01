## Gas Optimizations
Issue
## [G-01] MULTIPLE ADDRESS/ID MAPPINGS CAN BE COMBINED INTO A SINGLE MAPPING OF AN ADDRESS/ID TO A STRUCT, WHERE APPROPRIATE
## [G-02] Multiple accesses of a mapping/array should use a local variable cache
## [G-03] Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead
## [G-04] <X> += <Y> COSTS MORE GAS THAN <X> = <X> + <Y> FOR STATE VARIABLES
## [G-05] STORAGE POINTER TO A STRUCTURE IS CHEAPER THAN COPYING EACH VALUE OF THE STRUCTURE INTO MEMORY, SAME FOR ARRAY AND MAPPING
## [G-06] SETTING THE CONSTRUCTOR TO PAYABLE
## [G-07] NOT USING THE NAMED RETURN VARIABLES WHEN A FUNCTION RETURNS, WASTES DEPLOYMENT GAS
## [G-08] EXPRESSIONS FOR CONSTANT VALUES SUCH AS A CALL TO KECCAK256(), SHOULD USE IMMUTABLE RATHER THAN CONSTANT
Total: 51 contexts over 08  issues

## [G-01] MULTIPLE ADDRESS/ID MAPPINGS CAN BE COMBINED INTO A SINGLE MAPPING OF AN ADDRESS/ID TO A STRUCT, WHERE APPROPRIATE
Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key’s keccak256 hash (Gkeccak256 - 30 gas) and that calculation’s associated stack operations.
Contexts: 02
File: src/core/Factory.sol
  mapping(address => mapping(address => mapping(uint256 => mapping(uint256 => mapping(uint256 => address)))))
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Factory.sol#L39

File: src/periphery/LiquidityManager.sol
   mapping(address => mapping(address => Position)) public positions;
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/LiquidityManager.sol#L69

## [G-02] Multiple accesses of a mapping/array should use a local variable cache
The instances below point to the second+ access of a value inside a mapping/array, within a function. Caching a mapping’s value in a local storage or calldata variable when the value is accessed multiple times, saves ~42 gas per access due to not having to recalculate the key’s keccak256 hash (Gkeccak256 - 30 gas) and that calculation’s associated stack operations. Caching an array’s struct avoids recalculating the array offsets into memory/calldata
Contexts: 01
File: src/core/Factory.sol
     if (getLendgine[token0][token1][token0Exp][token1Exp][upperBound] != address(0)) revert DeployedError();
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Factory.sol#L76

## [G-03] Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead
When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.
https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html
Contexts: 17
File: src/core/Factory.sol
     uint128 token0Exp;
     uint128 token0Exp;
     uint8 token0Exp,
     uint8 token1Exp,
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Factory.sol#L50
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Factory.sol#L51
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Factory.sol#L66
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Factory.sol#L67

File: src/core/ImmutableState.sol
    uint128 _token0Exp;
    uint128 _token1Exp;
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/ImmutableState.sol#L30
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/ImmutableState.sol#L31

File: src/periphery/SwapHelper.sol
    uint24 fee;
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/SwapHelper.sol#L62

File: src/core/Pair.sol
  uint120 public override reserve0;
  uint120 public override reserve1;
    uint120 _reserve0 = reserve0; // SLOAD
    uint120 _reserve1 = reserve1; // SLOAD
    uint120 _reserve0 = reserve0; // SLOAD
    uint120 _reserve1 = reserve1; // SLOAD
    uint120 _reserve0 = reserve0; // SLOAD
    uint120 _reserve1 = reserve1; // SLOAD
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Pair.sol#L120
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Pair.sol#L40
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Pair.sol#L43
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Pair.sol#L71
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Pair.sol#L72
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Pair.sol#L94
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Pair.sol#L95
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Pair.sol#L119
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Pair.sol#L120

File: src/periphery/libraries/LendgineAddress.sol
      uint160(
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/libraries/LendgineAddress.sol#L22

File: src/periphery/UniswapV2/libraries/UniswapV2Library.sol
      uint160( // extra cast for newer solidity
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L20

## [G-04] <X> += <Y> COSTS MORE GAS THAN <X> = <X> + <Y> FOR STATE VARIABLES
Contexts: 10
File: src/core/Lendgine.sol
    totalLiquidityBorrowed += liquidity;
     totalLiquidityBorrowed -= liquidity;
     totalPositionSize -= size;
     rewardPerPositionStored += FullMath.mulDiv(dilutionSpeculative, 1e18, totalPositionSize);
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Lendgine.sol#L91
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Lendgine.sol#L114
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Lendgine.sol#L176
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Lendgine.sol#L257

File: src/periphery/LiquidityManager.sol 
    position.tokensOwed += FullMath.mulDiv(position.size, rewardPerPositionPaid - position.rewardPerPositionPaid, 1e18);
    position.size += size;
    position.tokensOwed += FullMath.mulDiv(position.size, rewardPerPositionPaid - position.rewardPerPositionPaid, 1e18);
    position.size -= params.size;
    position.tokensOwed += FullMath.mulDiv(position.size, rewardPerPositionPaid - position.rewardPerPositionPaid, 1e18);
    position.tokensOwed -= amount;
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/LiquidityManager.sol#L178
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/LiquidityManager.sol#L180
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/LiquidityManager.sol#L214
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/LiquidityManager.sol#L216
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/LiquidityManager.sol#L238
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/LiquidityManager.sol#L242

## [G-05] STORAGE POINTER TO A STRUCTURE IS CHEAPER THAN COPYING EACH VALUE OF THE STRUCTURE INTO MEMORY, SAME FOR ARRAY AND MAPPING
It may not be obvious, but every time you copy a storage struct/array/mapping to a memory variable, you are literally copying each member by reading it from storage, which is expensive. And when you use the storage keyword, you are just storing a pointer to the storage, which is much cheaper.
Contexts: 04
File: src/core/Lendgine.sol
    Position.Info memory positionInfo = positions[msg.sender]; // SLOAD
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Lendgine.sol#L167

File: src/periphery/LiquidityManager.sol 
    Position memory position = positions[params.recipient][lendgine]; // SLOAD
    Position memory position = positions[msg.sender][lendgine]; // SLOAD
    Position memory position = positions[msg.sender][params.lendgine]; // SLOAD
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/LiquidityManager.sol#L175
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/LiquidityManager.sol#L211
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/LiquidityManager.sol#L235

## [G-06] SETTING THE CONSTRUCTOR TO PAYABLE
Saves ~13 gas per instance
Contexts: 04
File: src/periphery/LiquidityManager.sol 
   constructor(address _factory, address _weth) Payment(_weth) {
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/LiquidityManager.sol#L75

File: src/core/ImmutableState.sol
  constructor() {
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/ImmutableState.sol#L27-L37

File: src/periphery/Payment.sol
  constructor(address _weth) {
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/Payment.sol#L17-L19

File: src/periphery/SwapHelper.sol
  constructor(address _uniswapV2Factory, address _uniswapV3Factory) {
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/SwapHelper.sol#L29-L32

## [G-07] NOT USING THE NAMED RETURN VARIABLES WHEN A FUNCTION RETURNS, WASTES DEPLOYMENT GAS
It is not necessary to have both a named return and a return statement.
Contexts: 12
File: src/core/JumpRate.sol
  function getBorrowRate(uint256 borrowedLiquidity, uint256 totalLiquidity) public pure override returns (uint256 rate) {
    returns (uint256 rate)
  function utilizationRate(uint256 borrowedLiquidity, uint256 totalLiquidity) private pure returns (uint256 rate) {
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/JumpRate.sol#L13
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/JumpRate.sol#L32
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/JumpRate.sol#L40

File: src/core/libraries/PositionMath.sol
  function addDelta(uint256 x, int256 y) internal pure returns (uint256 z) {
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/libraries/PositionMath.sol#L12

File: src/libraries/SafeCast.sol
  function toUint120(uint256 y) internal pure returns (uint120 z) {
  function toInt256(uint256 y) internal pure returns (int256 z) {
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/libraries/SafeCast.sol#L8
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/libraries/SafeCast.sol#L15

File: src/periphery/libraries/LendgineAddress.sol
    returns (address lendgine)
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/libraries/LendgineAddress.sol#L19

File: src/periphery/UniswapV2/libraries/UniswapV2Library.sol
  function sortTokens(address tokenA, address tokenB) internal pure returns (address token0, address token1) {
  function pairFor(address factory, address tokenA, address tokenB) internal pure returns (address pair) {
    returns (uint256 amountOut)
    returns (uint256 amountIn)
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L43
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L10
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L17
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L43
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L58
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L76

## [G-08] EXPRESSIONS FOR CONSTANT VALUES SUCH AS A CALL TO KECCAK256(), SHOULD USE IMMUTABLE RATHER THAN CONSTANT
Contexts: 01
File: src/periphery/libraries/LendgineAddress.sol
  uint256 internal constant INIT_CODE_HASH =
    71_695_300_681_742_793_458_567_320_549_603_773_755_938_496_017_772_337_363_704_152_556_600_186_974;
              keccak256(abi.encode(token0, token1, token0Exp, token1Exp, upperBound)),
              bytes32(INIT_CODE_HASH)
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/libraries/LendgineAddress.sol#L6-L7
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/libraries/LendgineAddress.sol#L28-L29