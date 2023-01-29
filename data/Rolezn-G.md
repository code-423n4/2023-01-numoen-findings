## Summary<a name="Summary">

### Gas Optimizations
| |Issue|Contexts|Estimated Gas Saved|
|-|:-|:-|:-:|
| [GAS&#x2011;1](#GAS&#x2011;1) | `abi.encode()` is less efficient than `abi.encodepacked()` | 6 | 600 |
| [GAS&#x2011;2](#GAS&#x2011;2) | Setting the `constructor` to `payable` | 5 | 65 |
| [GAS&#x2011;3](#GAS&#x2011;3) | Duplicated `require()`/`revert()` Checks Should Be Refactored To A Modifier Or Function | 2 | 56 |
| [GAS&#x2011;4](#GAS&#x2011;4) | Use `assembly` to write address storage values | 15 | - |
| [GAS&#x2011;5](#GAS&#x2011;5) | Use hardcoded address instead `address(this)` | 16 | - |
| [GAS&#x2011;6](#GAS&#x2011;6) | Optimize names to save gas | 9 | 198 |
| [GAS&#x2011;7](#GAS&#x2011;7) | `<x> += <y>` Costs More Gas Than `<x> = <x> + <y>` For State Variables | 10 | - |
| [GAS&#x2011;8](#GAS&#x2011;8) | Public Functions To External | 8 | - |
| [GAS&#x2011;9](#GAS&#x2011;9) | Splitting `require()` Statements That Use `&&` Saves Gas | 2 | 18 |
| [GAS&#x2011;10](#GAS&#x2011;10) | Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead | 25 | - |
| [GAS&#x2011;11](#GAS&#x2011;11) | Using `unchecked` blocks to save gas | 3 | 408 |
| [GAS&#x2011;12](#GAS&#x2011;12) | Use solidity version 0.8.17 to gain some gas boost | 15 | 1320 |
| [GAS&#x2011;13](#GAS&#x2011;13) | Using `storage` instead of `memory` saves gas | 4 | 1400 |

Total: 159 contexts over 22 issues

## Gas Optimizations

### <a href="#Summary">[GAS&#x2011;1]</a><a name="GAS&#x2011;1"> `abi.encode()` is less efficient than `abi.encodepacked()`

See for more information: https://github.com/ConnorBlockchain/Solidity-Encode-Gas-Comparison 

#### <ins>Proof Of Concept</ins>


```solidity
82: lendgine = address(new Lendgine{ salt: keccak256(abi.encode(token0, token1, token0Exp, token1Exp, upperBound)) }());
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Factory.sol#L82

```solidity
150: abi.encode(
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/LendgineRouter.sol#L150

```solidity
268: abi.encode(
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/LendgineRouter.sol#L268

```solidity
160: abi.encode(
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/LiquidityManager.sol#L160

```solidity
108: abi.encode(params.tokenIn)
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/SwapHelper.sol#L108

```solidity
28: keccak256(abi.encode(token0, token1, token0Exp, token1Exp, upperBound)),
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/libraries/LendgineAddress.sol#L28







### <a href="#Summary">[GAS&#x2011;2]</a><a name="GAS&#x2011;2"> Setting the `constructor` to `payable`

Saves ~13 gas per instance

#### <ins>Proof Of Concept</ins>

```solidity
27: constructor()
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/ImmutableState.sol#L27

```solidity
49: constructor(
    address _factory,
    address _uniswapV2Factory,
    address _uniswapV3Factory,
    address _weth
  )
    SwapHelper(_uniswapV2Factory, _uniswapV3Factory)
    Payment(_weth)
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/LendgineRouter.sol#L49

```solidity
75: constructor(address _factory, address _weth) Payment(_weth)
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/LiquidityManager.sol#L75

```solidity
17: constructor(address _weth)
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/Payment.sol#L17

```solidity
29: constructor(address _uniswapV2Factory, address _uniswapV3Factory)
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/SwapHelper.sol#L29





#### <a href="#Summary">[GAS&#x2011;3]</a><a name="GAS&#x2011;3"> Duplicated `require()`/`revert()` Checks Should Be Refactored To A Modifier Or Function

Saves deployment costs

#### <ins>Proof Of Concept</ins>

```solidity
61: require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");
79: require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");

```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L61

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L79








### <a href="#Summary">[GAS&#x2011;4]</a><a name="GAS&#x2011;4"> Use `assembly` to write address storage values

#### <ins>Proof Of Concept</ins>

```solidity
135: _totalPositionSize = totalPositionSize; // SLOAD
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Lendgine.sol#L135

```solidity
163: _totalPositionSize = totalPositionSize; // SLOAD
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Lendgine.sol#L163

```solidity
164: _totalLiquidity = totalLiquidity; // SLOAD
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Lendgine.sol#L164

```solidity
214: _totalLiquidityBorrowed = totalLiquidityBorrowed; // SLOAD
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Lendgine.sol#L214

```solidity
247: _totalLiquidityBorrowed = totalLiquidityBorrowed; // SLOAD
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Lendgine.sol#L247

```solidity
267: _rewardPerPositionStored = rewardPerPositionStored; // SLOAD
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Lendgine.sol#L267

```solidity
73: _totalLiquidity = totalLiquidity; // SLOAD
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Pair.sol#L73

```solidity
96: _totalLiquidity = totalLiquidity; // SLOAD
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Pair.sol#L96

```solidity
47: _positionInfo = positionInfo;
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/libraries/Position.sol#L47





### <a href="#Summary">[GAS&#x2011;5]</a><a name="GAS&#x2011;5"> Use hardcode address instead `address(this)`

Instead of using `address(this)`, it is more gas-efficient to pre-calculate and use the hardcoded `address`. Foundry's script.sol and solmate's `LibRlp.sol` contracts can help achieve this.

References: 
https://book.getfoundry.sh/reference/forge-std/compute-create-address 

https://twitter.com/transmissions11/status/1518507047943245824

#### <ins>Proof Of Concept</ins>

```solidity
108: uint256 shares = balanceOf[address(this)];
115: _burn(address(this), shares);

```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Lendgine.sol#L108

https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Lendgine.sol#L115



```solidity
14: token.staticcall(abi.encodeWithSelector(bytes4(keccak256(bytes("balanceOf(address)"))), address(this)));

```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/libraries/Balance.sol#L14

```solidity
148: address(this),

```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/LendgineRouter.sol#L148

```solidity
235: if (decoded.recipient != address(this)) {

```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/LendgineRouter.sol#L235

```solidity
262: address recipient = params.recipient == address(0) ? address(this) : params.recipient;
267: address(this),

```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/LendgineRouter.sol#L262

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/LendgineRouter.sol#L267



```solidity
158: address(this),
177: (, uint256 rewardPerPositionPaid,) = ILendgine(lendgine).positions(address(this));

```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/LiquidityManager.sol#L158

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/LiquidityManager.sol#L177



```solidity
206: address recipient = params.recipient == address(0) ? address(this) : params.recipient;
213: (, uint256 rewardPerPositionPaid,) = ILendgine(lendgine).positions(address(this));

```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/LiquidityManager.sol#L206

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/LiquidityManager.sol#L213



```solidity
233: address recipient = params.recipient == address(0) ? address(this) : params.recipient;
237: (, uint256 rewardPerPositionPaid,) = ILendgine(params.lendgine).positions(address(this));

```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/LiquidityManager.sol#L233

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/LiquidityManager.sol#L237



```solidity
45: if (address(this).balance > 0) SafeTransferLib.safeTransferETH(msg.sender, address(this).balance);

```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/Payment.sol#L45

```solidity
53: if (token == weth && address(this).balance >= value) {
57: } else if (payer == address(this)) {

```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/Payment.sol#L53

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/Payment.sol#L57





#### <ins>Recommended Mitigation Steps</ins>

Use hardcoded `address`












### <a href="#Summary">[GAS&#x2011;6]</a><a name="GAS&#x2011;6"> Optimize names to save gas

Contracts most called functions could simply save gas by function ordering via Method ID. Calling a function at runtime will be cheaper if the function is positioned earlier in the order (has a relatively lower Method ID) because 22 gas are added to the cost of a function for every position that came before it. The caller can save on gas if you prioritize most called functions. 

See more <a href="https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92">here</a>

#### <ins>Proof Of Concept</ins>

All in-scope contracts

#### <ins>Recommended Mitigation Steps</ins>
Find a lower method ID name for the most called functions for example Call() vs. Call1() is cheaper by 22 gas
For example, the function IDs in the Gauge.sol contract will be the most used; A lower method ID may be given.



### <a href="#Summary">[GAS&#x2011;7]</a><a name="GAS&#x2011;7"> `<x> += <y>` Costs More Gas Than `<x> = <x> + <y>` For State Variables

#### <ins>Proof Of Concept</ins>


```solidity
91: totalLiquidityBorrowed += liquidity;

```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Lendgine.sol#L91

```solidity
114: totalLiquidityBorrowed -= liquidity;

```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Lendgine.sol#L114

```solidity
176: totalPositionSize -= size;

```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Lendgine.sol#L176

```solidity
257: rewardPerPositionStored += FullMath.mulDiv(dilutionSpeculative, 1e18, totalPositionSize);

```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Lendgine.sol#L257

```solidity
178: position.tokensOwed += FullMath.mulDiv(position.size, rewardPerPositionPaid - position.rewardPerPositionPaid, 1e18);
180: position.size += size;

```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/LiquidityManager.sol#L178

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/LiquidityManager.sol#L180



```solidity
214: position.tokensOwed += FullMath.mulDiv(position.size, rewardPerPositionPaid - position.rewardPerPositionPaid, 1e18);
216: position.size -= params.size;

```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/LiquidityManager.sol#L214

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/LiquidityManager.sol#L216



```solidity
238: position.tokensOwed += FullMath.mulDiv(position.size, rewardPerPositionPaid - position.rewardPerPositionPaid, 1e18);
242: position.tokensOwed -= amount;

```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/LiquidityManager.sol#L238

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/LiquidityManager.sol#L242







### <a href="#Summary">[GAS&#x2011;8]</a><a name="GAS&#x2011;8"> Public Functions To External

The following functions could be set external to save gas and improve code quality.
External call cost is less expensive than of public functions.

#### <ins>Proof Of Concept</ins>


```solidity
function getBorrowRate(uint256 borrowedLiquidity, uint256 totalLiquidity) public pure override returns (uint256 rate) {
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/JumpRate.sol#L13

```solidity
function convertLiquidityToShare(uint256 liquidity) public view override returns (uint256) {
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Lendgine.sol#L213

```solidity
function convertShareToLiquidity(uint256 shares) public view override returns (uint256) {
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Lendgine.sol#L219

```solidity
function convertCollateralToLiquidity(uint256 collateral) public view override returns (uint256) {
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Lendgine.sol#L224

```solidity
function convertLiquidityToCollateral(uint256 liquidity) public view override returns (uint256) {
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Lendgine.sol#L229

```solidity
function invariant(uint256 amount0, uint256 amount1, uint256 liquidity) public view override returns (bool) {
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Pair.sol#L53

```solidity
function unwrapWETH(uint256 amountMinimum, address recipient) public payable {
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/Payment.sol#L25

```solidity
function sweepToken(address token, uint256 amountMinimum, address recipient) public payable {
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/Payment.sol#L35





### <a href="#Summary">[GAS&#x2011;9]</a><a name="GAS&#x2011;9"> Splitting `require()` statements that use `&&` saves gas

Instead of using operator `&&` on a single `require`. Using a two `require` can save more gas.

i.e.
for `require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");` use:

```
	require(reserveIn > 0);
	require(reserveOut > 0);
```

#### <ins>Proof Of Concept</ins>

```solidity
61: require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L61

```solidity
79: require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L79








### <a href="#Summary">[GAS&#x2011;10]</a><a name="GAS&#x2011;10"> Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract's gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html
Each operation involving a `uint8` costs an extra 22-28 gas (depending on whether the other operand is also a variable of type `uint8`) as compared to ones involving `uint256`, due to the compiler having to clear the higher bits of the memory word before operating on the `uint8`, as well as the associated stack operations of doing so. Use a larger size then downcast where needed

#### <ins>Proof Of Concept</ins>


```solidity
50: uint128 token0Exp;
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Factory.sol#L50

```solidity
51: uint128 token1Exp;
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Factory.sol#L51

```solidity
30: uint128 _token0Exp;
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/ImmutableState.sol#L30

```solidity
31: uint128 _token1Exp;
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/ImmutableState.sol#L31

```solidity
40: uint120 public override reserve0;
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Pair.sol#L40

```solidity
43: uint120 public override reserve1;
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Pair.sol#L43

```solidity
119: uint120 _reserve0 = reserve0;
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Pair.sol#L119

```solidity
120: uint120 _reserve1 = reserve1;
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Pair.sol#L120

```solidity
62: uint24 fee;
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/SwapHelper.sol#L62






### <a href="#Summary">[GAS&#x2011;11]</a><a name="GAS&#x2011;11"> Using `unchecked` blocks to save gas

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isnâ€™t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an `unchecked` block

#### <ins>Proof Of Concept</ins>

```solidity
14: require((z = x - uint256(-y)) < x, "LS");

```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/libraries/PositionMath.sol#L14

```solidity
107: zeroForOne ? TickMath.MIN_SQRT_RATIO + 1 : TickMath.MAX_SQRT_RATIO - 1,

```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/SwapHelper.sol#L107

```solidity
81: uint256 denominator = (reserveOut - amountOut) * 997;

```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L81






### <a href="#Summary">[GAS&#x2011;12]</a><a name="GAS&#x2011;12"> Use solidity version 0.8.17 to gain some gas boost

Upgrade to the latest solidity version 0.8.17 to get additional gas savings.

#### <ins>Proof Of Concept</ins>


```solidity
pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Factory.sol#L2

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/ImmutableState.sol#L2

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/JumpRate.sol#L2

```solidity
pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Lendgine.sol#L2

```solidity
pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Pair.sol#L2

```solidity
pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/libraries/Position.sol#L2

```solidity
pragma solidity >=0.5.0;
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/libraries/PositionMath.sol#L2

```solidity
pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/libraries/Balance.sol#L2

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/libraries/SafeCast.sol#L2

```solidity
pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/LendgineRouter.sol#L2

```solidity
pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/LiquidityManager.sol#L2

```solidity
pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/Payment.sol#L2

```solidity
pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/SwapHelper.sol#L2

```solidity
pragma solidity >=0.5.0;
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/libraries/LendgineAddress.sol#L2

```solidity
pragma solidity >=0.8.0;
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L1





### <a href="#Summary">[GAS&#x2011;13]</a><a name="GAS&#x2011;13"> Using `storage` instead of `memory` saves gas

When fetching data from a `storage` location, assigning the data to a `memory` variable causes all fields of the struct/array to be read from `storage`, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new `memory` variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declearing the variable with the memory keyword, declaring the variable with the `storage` keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a `memory` variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another `memory` array/struct

#### <ins>Proof Of Concept</ins>


```solidity
167: Position.Info memory positionInfo = positions[msg.sender];

```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Lendgine.sol#L167

```solidity
175: Position memory position = positions[params.recipient][lendgine];

```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/LiquidityManager.sol#L175

```solidity
211: Position memory position = positions[msg.sender][lendgine];

```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/LiquidityManager.sol#L211

```solidity
235: Position memory position = positions[msg.sender][params.lendgine];

```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/LiquidityManager.sol#L235





