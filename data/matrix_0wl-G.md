# Summary

## Gas Optimizations

|        | Issue                                                                                                                                                                       | Instances |
| ------ | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :-------: |
| GAS-1  | `<x> += <y>`/`<x> -= <y>` costs more gas than `<x> = <x> + <y>`/`<x> = <x> - <y>` for state variables                                                                       |    10     |
| GAS-2  | Setting the constructor to payable                                                                                                                                          |     5     |
| GAS-3  | Internal functions only called once can be inlined to save gas                                                                                                              |     2     |
| GAS-4  | Optimize names to save gas                                                                                                                                                  |     9     |
| GAS-5  | Proper data types                                                                                                                                                           |    17     |
| GAS-6  | Reorder structure layout                                                                                                                                                    |     4     |
| GAS-7  | Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()`, `revert` or `if` statement OR Using unchecked blocks to save gas |     5     |
| GAS-8  | Using storage instead of memory for structs/arrays saves gas                                                                                                                |    10     |
| GAS-9  | Splitting require() statements that use && saves gas                                                                                                                        |     2     |
| GAS-10 | Usage of `uint`/`int` smaller than 32 bytes (256 bits) incurs overhead                                                                                                      |     3     |
| GAS-11 | Upgrade to at least 0.8.4                                                                                                                                                   |     2     |
| GAS-12 | Public functions not called by the contract should be declared external instead                                                                                             |     2     |
| GAS-13 | Not using the named return variables when a function returns, wastes deployment gas                                                                                         |    11     |

### [GAS-1] `<x> += <y>`/`<x> -= <y>` costs more gas than `<x> = <x> + <y>`/`<x> = <x> - <y>` for state variables

Using compound assignment operators for state variables (like `State += X` or S`tate -= X` …) it’s more expensive than using operator assignment (like `State = State + X` or `State = State - X` …).

##### **Proof Of Concept**

```solidity
File: src/core/Lendgine.sol

91:     totalLiquidityBorrowed += liquidity;

114:     totalLiquidityBorrowed -= liquidity;

176:     totalPositionSize -= size;

257:     rewardPerPositionStored += FullMath.mulDiv(dilutionSpeculative, 1e18, totalPositionSize);

```

```solidity
File: src/periphery/LiquidityManager.sol

178:     position.tokensOwed += FullMath.mulDiv(position.size, rewardPerPositionPaid - position.rewardPerPositionPaid, 1e18);

180:     position.size += size;

214:     position.tokensOwed += FullMath.mulDiv(position.size, rewardPerPositionPaid - position.rewardPerPositionPaid, 1e18);

216:     position.size -= params.size;

238:     position.tokensOwed += FullMath.mulDiv(position.size, rewardPerPositionPaid - position.rewardPerPositionPaid, 1e18);

242:     position.tokensOwed -= amount;

```

### [GAS-2] Setting the constructor to payable

Saves ~13 gas per instance

##### **Proof Of Concept**

```solidity
File: src/core/ImmutableState.sol

27:   constructor() {

```

```solidity
File: src/periphery/LendgineRouter.sol

49:   constructor(

```

```solidity
File: src/periphery/LiquidityManager.sol

75:   constructor(address _factory, address _weth) Payment(_weth) {

```

```solidity
File: src/periphery/Payment.sol

17:   constructor(address _weth) {

```

```solidity
File: src/periphery/SwapHelper.sol

29:   constructor(address _uniswapV2Factory, address _uniswapV3Factory) {

```

### [GAS-3] Internal functions only called once can be inlined to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

##### **Proof Of Concept**

```solidity
File: src/core/libraries/Position.sol

69:   function newTokensOwed(Position.Info memory position, uint256 rewardPerPosition) internal pure returns (uint256) {

```

```solidity
File: src/periphery/UniswapV2/libraries/UniswapV2Library.sol

17:   function pairFor(address factory, address tokenA, address tokenB) internal pure returns (address pair) {

```

### [GAS-4] Optimize names to save gas

`public`/`external` function names and `public` member variable names can be optimized to save gas. See [this](https://gist.github.com/IllIllI000/a5d8b486a8259f9f77891a919febd1a9) link for an example of how it works. In this report are the interfaces/abstract contracts that can be optimized so that the most frequently-called functions use the least amount of gas possible during method lookup. Method IDs that have two leading zero bytes can save 128 gas each during deployment, and renaming functions to have lower method IDs will save 22 gas per call, [per sorted position shifted](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92).

_There are 9 instances of this issue._

##### **Proof Of Concept**

```solidity
File: src/core/Factory.sol

8: contract Factory is IFactory {

```

```solidity
File: src/core/ImmutableState.sol

8: abstract contract ImmutableState is IImmutableState {

```

```solidity
File: src/core/JumpRate.sol

6: abstract contract JumpRate is IJumpRate {

```

```solidity
File: src/core/Lendgine.sol

17: contract Lendgine is ERC20, JumpRate, Pair, ILendgine {

```

```solidity
File: src/core/Pair.sol

16: abstract contract Pair is ImmutableState, ReentrancyGuard, IPair {

```

```solidity
File: src/periphery/LendgineRouter.sol

20: contract LendgineRouter is Multicall, Payment, SelfPermit, SwapHelper, IMintCallback, IPairMintCallback {

```

```solidity
File: src/periphery/LiquidityManager.sol

16: contract LiquidityManager is Multicall, Payment, SelfPermit, IPairMintCallback {

```

```solidity
File: src/periphery/Payment.sol

12: abstract contract Payment {

```

```solidity
File: src/periphery/SwapHelper.sol

15: abstract contract SwapHelper is IUniswapV3SwapCallback {

```

### [GAS-5] Proper data types

In Solidity, some data types are more expensive than others. It’s important to be aware of the most efficient type that can be used. Here are a few rules about data types.

Type `uint` should be used in place of type `string` whenever possible.

Type `uint256` takes less gas to store than `uint8`

Type `bytes` should be used over `byte[]`

If the length of `bytes` can be limited, use the lowest amount possible from `bytes1` to `bytes32`.

Type `bytes32` is cheaper to use than type `string` and `bytes`.

If data can fit into 32 bytes, then you should use bytes32 datatype rather than bytes or strings as it is cheaper in solidity.

##### **Proof Of Concept**

```solidity
File: src/core/Factory.sol

50:     uint128 token0Exp;

51:     uint128 token1Exp;

66:     uint8 token0Exp,

67:     uint8 token1Exp,

```

```solidity
File: src/core/ImmutableState.sol

30:     uint128 _token0Exp;

31:     uint128 _token1Exp;

```

```solidity
File: src/core/Pair.sol

40:   uint120 public override reserve0;

43:   uint120 public override reserve1;

71:     uint120 _reserve0 = reserve0; // SLOAD

72:     uint120 _reserve1 = reserve1; // SLOAD

94:     uint120 _reserve0 = reserve0; // SLOAD

95:     uint120 _reserve1 = reserve1; // SLOAD

119:     uint120 _reserve0 = reserve0; // SLOAD

120:     uint120 _reserve1 = reserve1; // SLOAD

```

```solidity
File: src/periphery/SwapHelper.sol

62:     uint24 fee;

```

```solidity
File: src/periphery/UniswapV2/libraries/UniswapV2Library.sol

20:       uint160( // extra cast for newer solidity

```

```solidity
File: src/periphery/libraries/LendgineAddress.sol

22:       uint160(

```

### [GAS-6] Reorder structure layout

Structures could be optimized moving the position of certain values in order to save a lot slots.

For example Enums are represented by integers; the possibility listed first by 0, the next by 1, and so forth. An enum type just acts like uintN, where N is the smallest legal value large enough to accomodate all the possibilities.

[Source](https://ethdebug.github.io/solidity-data-representation)

[Source](https://docs.soliditylang.org/en/v0.8.17/internals/layout_in_storage.html#storage-inplace-encoding)

##### **Proof Of Concept**

```solidity
File: src/periphery/LendgineRouter.sol

74:   struct MintCallbackData {
        address token0;
        address token1;
        uint256 token0Exp;
        uint256 token1Exp;
        uint256 upperBound;
        uint256 collateralMax;
    +   bytes swapExtraData;
        SwapType swapType;
    -   bytes swapExtraData;
        address payer;
  }

126:   struct MintParams {
        address token0;
        address token1;
        uint256 token0Exp;
        uint256 token1Exp;
        uint256 upperBound;
        uint256 amountIn;
        uint256 amountBorrow;
        uint256 sharesMin;
+       bytes swapExtraData;
        SwapType swapType;
-       bytes swapExtraData;
        address recipient;
        uint256 deadline;
  }

175:   struct PairMintCallbackData {
        address token0;
        address token1;
        uint256 token0Exp;
        uint256 token1Exp;
        uint256 upperBound;
        uint256 collateralMin;
        uint256 amount0Min;
        uint256 amount1Min;
+       bytes swapExtraData;
        SwapType swapType;
-       bytes swapExtraData;
        address recipient;
  }

240:   struct BurnParams {
        address token0;
        address token1;
        uint256 token0Exp;
        uint256 token1Exp;
        uint256 upperBound;
        uint256 shares;
        uint256 collateralMin;
        uint256 amount0Min;
        uint256 amount1Min;
+       bytes swapExtraData;
        SwapType swapType;
-       bytes swapExtraData;
        address recipient;
        uint256 deadline;
  }

```

### [GAS-7] Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()`, `revert` or `if` statement OR Using unchecked blocks to save gas

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block

[Resource](https://github.com/ethereum/solidity/issues/10695)

`require(a <= b); x = b - a => require(a <= b); unchecked { x = b - a }`

`if(a <= b); x = b - a => if(a <= b); unchecked { x = b - a }`

This will stop the check for overflow and underflow so it will save gas

##### **Proof Of Concept**

```solidity
File: src/core/JumpRate.sol

20:     uint256 excessUtil = util - kink;

```

```solidity
File: src/periphery/UniswapV2/libraries/UniswapV2Library.sol

60:     require(amountIn > 0, "UniswapV2Library: INSUFFICIENT_INPUT_AMOUNT");

61:     require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");

78:     require(amountOut > 0, "UniswapV2Library: INSUFFICIENT_OUTPUT_AMOUNT");

79:     require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");

```

## [GAS-8] Using storage instead of memory for structs/arrays saves gas

When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional `MLOAD` rather than a cheap stack read. Instead of declearing the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read.

The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct

##### **Proof Of Concept**

```solidity
File: src/core/Lendgine.sol

167:     Position.Info memory positionInfo = positions[msg.sender]; // SLOAD

```

```solidity
File: src/core/libraries/Position.sol

47:     Position.Info memory _positionInfo = positionInfo;

69:   function newTokensOwed(Position.Info memory position, uint256 rewardPerPosition) internal pure returns (uint256) {

```

```solidity
File: src/periphery/LendgineRouter.sol

97:     MintCallbackData memory decoded = abi.decode(data, (MintCallbackData));

191:     PairMintCallbackData memory decoded = abi.decode(data, (PairMintCallbackData));

```

```solidity
File: src/periphery/LiquidityManager.sol

105:     PairMintCallbackData memory decoded = abi.decode(data, (PairMintCallbackData));

175:     Position memory position = positions[params.recipient][lendgine]; // SLOAD

211:     Position memory position = positions[msg.sender][lendgine]; // SLOAD

235:     Position memory position = positions[msg.sender][params.lendgine]; // SLOAD

```

```solidity
File: src/periphery/SwapHelper.sol

90:       UniV3Data memory uniV3Data = abi.decode(data, (UniV3Data));

```

### [GAS-9] Splitting require() statements that use && saves gas

Instead of using operator `&&` on a single `require`. Using a two `require` can save more gas.

##### **Proof Of Concept**

```solidity
File: src/periphery/UniswapV2/libraries/UniswapV2Library.sol

61:     require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");

79:     require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");

```

### [GAS-10] Usage of `uint`/`int` smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.
Each operation involving a `uint8` costs an extra 22-28 gas (depending on whether the other operand is also a variable of type `uint8`) as compared to ones involving uint256, due to the compiler having to clear the higher bits of the memory word before operating on the uint8, as well as the associated stack operations of doing so. Use a larger size then downcast where needed.

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html

##### **Proof Of Concept**

```solidity
File: src/core/Factory.sol

66:     uint8 token0Exp,

67:     uint8 token1Exp,

```

```solidity
File: src/periphery/SwapHelper.sol

62:     uint24 fee;

```

### [GAS-11] Upgrade to at least 0.8.4

Use pragma solidity bigger than 0.8.0 - Using newer compiler versions and the optimizer gives gas optimizations and additional safety checks for free!
Safemath by default from 0.8.0 (can be more gas efficient than some library-based safemath.)

[Low-level inliner](https://blog.soliditylang.org/2021/03/02/saving-gas-with-simple-inliner/) from 0.8.2, leads to cheaper runtime gas. Especially relevant when the contract has small functions. For example, OpenZeppelin libraries typically have a lot of small helper functions and if they are not inlined, they cost an additional 20 to 40 gas because of 2 extra jump instructions and additional stack operations needed for function calls.

[Optimizer improvements in packed structs](https://blog.soliditylang.org/2021/03/23/solidity-0.8.3-release-announcement/#optimizer-improvements): Before 0.8.3, storing packed structs, in some cases, used additional storage read operation. After [EIP-2929](https://eips.ethereum.org/EIPS/eip-2929), if the slot was already cold, this means unnecessary stack operations and extra deploy time costs. However, if the slot was already warm, this means an additional cost of 100 gas alongside the same unnecessary stack operations and extra deploy time costs.)

[Custom errors](https://blog.soliditylang.org/2021/04/21/custom-errors) 24 from 0.8.4, leads to cheaper deploy time cost and run time cost. Note: the run time cost is only relevant when the revert condition is met. In short, replace revert strings with custom errors.

[Source](https://forum.openzeppelin.com/t/a-collection-of-gas-optimisation-tricks/19966/6)

##### **Proof Of Concept**

```solidity
File: src/core/libraries/PositionMath.sol

2: pragma solidity >=0.5.0;

```

```solidity
File: src/periphery/libraries/LendgineAddress.sol

2: pragma solidity >=0.5.0;

```

### Recommended Mitigation Steps:

Upgrade to at least 0.8.4

### [GAS-12] Public functions not called by the contract should be declared external instead

Contracts [are allowed](https://docs.soliditylang.org/en/latest/contracts.html#function-overriding) to override their parents’ functions and change the visibility from external to public and can save gas by doing so.

##### **Proof Of Concept**

```solidity
File: src/periphery/Payment.sol

25:   function unwrapWETH(uint256 amountMinimum, address recipient) public payable {

35:   function sweepToken(address token, uint256 amountMinimum, address recipient) public payable {

```

### [GAS-13] Not using the named return variables when a function returns, wastes deployment gas

Do not use return at the end of the function:

##### **Proof Of Concept**

```solidity
File: src/core/Factory.sol

63:       function createLendgine(

```

```solidity
File: src/core/Lendgine.sol

71:      function mint(

105:   function burn(address to, bytes calldata data) external override nonReentrant returns (uint256 collateral) {

125:       function deposit(

152:       function withdraw(

194:   function collect(address to, uint256 collateralRequested) external override nonReentrant returns (uint256 collateral) {

```

```solidity
File: src/core/Pair.sol

93:   function burn(address to, uint256 liquidity) internal returns (uint256 amount0, uint256 amount1) {

```

```solidity
File: src/periphery/LendgineRouter.sol

142:   function mint(MintParams calldata params) external payable checkDeadline(params.deadline) returns (uint256 shares) {

257:   function burn(BurnParams calldata params) external payable checkDeadline(params.deadline) returns (uint256 amount) {

```

```solidity
File: src/periphery/LiquidityManager.sol

230:   function collect(CollectParams calldata params) external payable returns (uint256 amount) {

```

```solidity
File: src/periphery/SwapHelper.sol

69:   function swap(SwapType swapType, SwapParams memory params, bytes memory data) internal returns (uint256 amount) {

```
