# GAS OPTIMIZATION REPORT

---

### Summary of optimizations


| Number       | Issue details                                                                                                 | Instances |
| -------------- | --------------------------------------------------------------------------------------------------------------- | :---------: |
| [G-1](#G2)   | Use calldata instead of memory.                                                                               |     1     |
| [G-2](#G4)   | Multiple address mappings can be combined into a single mapping of an address to a struct, where appropriate. |     2     |
| [G-3](#G6)   | Use`++i` instead of `i++`                                                                                     |     1     |
| [G-4](#G7)   | Usage of`uint`s/`int`s smaller than 32 bytes (256 bits) incurs overhead.                                      |    17    |
| [G-5](#G8)   | `abi.encode()` is less efficient than `abi.encodePacked()`                                                    |     5     |
| [G-6](#G9)   | Internal functions only called once can be inlined to save gas.                                               |     5     |
| [G-7](#G10) | `>=` costs less gas than `>`.                                                                                 |    30    |
| [G-8](#G12) | `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables                                        |    10    |

*Total: 8 issues.*

---

### <a id=G2>[G-1]</a> Use calldata instead of memory.

##### Description

Use calldata instead of memory for function parameters saves gas if the function argument is only read.

##### Recommendation

Use calldata instead of memory.

##### *Instances (1):*

File: [2023-01-numoen/src/periphery/SwapHelper.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/SwapHelper.sol#L69 )

```solidity
69: function swap(SwapType swapType, SwapParams memory params, bytes memory data) internal returns (uint256 amount) {
```

### <a id=G4>[G-2]</a> Multiple address mappings can be combined into a single mapping of an address to a struct, where appropriate.

##### Description

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

##### Recommendation

Where appropriate, you can combine multiple address mappings into a single mapping of an address to a struct.

##### *Instances (2):*

File: [2023-01-numoen/src/core/Factory.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Factory.sol#L39 )

```solidity
39: mapping(address => mapping(address => mapping(uint256 => mapping(uint256 => mapping(uint256 => address)))))
```

File: [2023-01-numoen/src/periphery/LiquidityManager.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/LiquidityManager.sol#L69 )

```solidity
69: mapping(address => mapping(address => Position)) public positions;
```

### <a id=G6>[G-3]</a> Use `++i` instead of `i++`

##### Description

[Source](https://betterprogramming.pub/solidity-gas-optimizations-and-tricks-2bcee0f9f1f2): `++i`costs less gas compared to `i++` or `i += 1` for unsigned integer, as pre-increment is cheaper (about 5 gas per iteration). This statement is true even with the optimizer enabled.

Example:
`i++` increments `i` and returns the initial value of `i`. Which means:
`uint i = 1; i++; // == 1 but i == 2`
But `++i` returns the actual incremented value:
`uint i = 1;`
`++i; // == 2 and i == 2 too`, so no need for a temporary variable
In the first case, the compiler has to create a temporary variable (when used) for returning 1 instead of 2
Also check [this](https://ethereum.stackexchange.com/questions/133161/why-does-i-cost-less-gas-than-i) out.

##### Recommendation

Change from `i++` to `++i`.

##### *Instances (1):*

File: [2023-01-numoen/src/libraries/FullMath.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/libraries/FullMath.sol#L19 )

```solidity
126: result++;
```

### <a id=G7>[G-4]</a> Usage of `uint`s/`int`s smaller than 32 bytes (256 bits) incurs overhead.

##### Description

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

##### Recommendation

Use `uint256` instead if appropriate.

##### *Instances (17):*

File: [2023-01-numoen/src/core/Factory.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Factory.sol#L50 )

```solidity
50: uint128 token0Exp;
51: uint128 token1Exp;
66: uint8 token0Exp,
67: uint8 token1Exp,
```

File: [2023-01-numoen/src/core/ImmutableState.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/ImmutableState.sol#L30 )

```solidity
30: uint128 _token0Exp;
31: uint128 _token1Exp;
```

File: [2023-01-numoen/src/core/Pair.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Pair.sol#L40 )

```solidity
40: uint120 public override reserve0;
43: uint120 public override reserve1;
71: uint120 _reserve0 = reserve0; // SLOAD
72: uint120 _reserve1 = reserve1; // SLOAD
94: uint120 _reserve0 = reserve0; // SLOAD
95: uint120 _reserve1 = reserve1; // SLOAD
119: uint120 _reserve0 = reserve0; // SLOAD
120: uint120 _reserve1 = reserve1; // SLOAD
```

File: [2023-01-numoen/src/libraries/SafeCast.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/libraries/SafeCast.sol#L8 )

```solidity
8: function toUint120(uint256 y) internal pure returns (uint120 z) {
9: require((z = uint120(y)) == y);
```

File: [2023-01-numoen/src/periphery/SwapHelper.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/SwapHelper.sol#L62 )

```solidity
62: uint24 fee;
```

### <a id=G8>[G-5]</a> `abi.encode()` is less efficient than `abi.encodePacked()`

##### Description

`abi.encode` will apply ABI encoding rules. Therefore all elementary types are padded to 32 bytes and dynamic arrays include their length. Therefore it is possible to also decode this data again (with `abi.decode`) when the type are known.

`abi.encodePacked` will only use the only use the minimal required memory to encode the data. E.g. an address will only use 20 bytes and for dynamic arrays only the elements will be stored without length. For more info see the Solidity docs for packed mode.

For the input of the `keccak` method it is important that you can ensure that the resulting bytes of the encoding are unique. So if you always encode the same types and arrays always have the same length then there is no problem. But if you switch the parameters that you encode or encode multiple dynamic arrays you might have conflicts.
For example:
`abi.encodePacked(address(0x0000000000000000000000000000000000000001), uint(0))` encodes to the same as `abi.encodePacked(uint(0x0000000000000000000000000000000000000001000000000000000000000000), address(0))`
and `abi.encodePacked(uint[](1,2), uint[](3))` encodes to the same as `abi.encodePacked(uint[](1), uint[](2,3))`
Therefore these examples will also generate the same hashes even so they are different inputs.
On the other hand you require less memory and therefore in most cases abi.encodePacked uses less gas than abi.encode.

##### Recommendation

Use `abi.encodePacked()` where possible to save gas

##### *Instances (5):*

File: [2023-01-numoen/src/core/Factory.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Factory.sol#L82 )

```solidity
82: lendgine = address(new Lendgine{ salt: keccak256(abi.encode(token0, token1, token0Exp, token1Exp, upperBound)) }());
```

File: [2023-01-numoen/src/periphery/LendgineRouter.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/LendgineRouter.sol#L150 )

```solidity
150: abi.encode(
268: abi.encode(
```

File: [2023-01-numoen/src/periphery/LiquidityManager.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/LiquidityManager.sol#L160 )

```solidity
160: abi.encode(
```

File: [2023-01-numoen/src/periphery/SwapHelper.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/SwapHelper.sol#L108 )

```solidity
108: abi.encode(params.tokenIn)
```

### <a id=G9>[G-6]</a> Internal functions only called once can be inlined to save gas.

##### Description

Not inlining costs 20 to 40 gas because of two extra `JUMP` instructions and additional stack operations needed for function calls.
Check this out: [link](https://betterprogramming.pub/solidity-gas-optimizations-and-tricks-2bcee0f9f1f2)
Note: To sum up, when there is an internal function that is only called once, there is no point for that function to exist.

##### Recommendation

Remove the function and make it inline if it is a simple function.

##### *Instances (5):*

File: [2023-01-numoen/src/core/Pair.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Pair.sol#L93 )

```solidity
93: function burn(address to, uint256 liquidity) internal returns (uint256 amount0, uint256 amount1) {
```

File: [2023-01-numoen/src/core/libraries/Position.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/libraries/Position.sol#L69 )

```solidity
69: function newTokensOwed(Position.Info memory position, uint256 rewardPerPosition) internal pure returns (uint256) {
```

File: [2023-01-numoen/src/core/libraries/PositionMath.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/libraries/PositionMath.sol#L12 )

```solidity
12: function addDelta(uint256 x, int256 y) internal pure returns (uint256 z) {
```

File: [2023-01-numoen/src/libraries/SafeCast.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/libraries/SafeCast.sol#L8 )

```solidity
8: function toUint120(uint256 y) internal pure returns (uint120 z) {
15: function toInt256(uint256 y) internal pure returns (int256 z) {
```

### <a id=G10>[G-7]</a> `>=` costs less gas than `>`.

##### Description

The compiler uses opcodes `GT` and `ISZERO` for solidity code that uses `>`, but only requires `LT` for `>=`, which [saves 3 gas](https://gist.github.com/IllIllI000/3dc79d25acccfa16dee4e83ffdc6ffde)

##### Recommendation

Use `>=` instead if appropriate.

##### *Instances (30):*

File: [2023-01-numoen/src/core/Factory.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Factory.sol#L77 )

```solidity
77: if (token0Exp > 18 || token0Exp < 6 || token1Exp > 18 || token1Exp < 6) revert ScaleError();
```

File: [2023-01-numoen/src/core/Lendgine.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Lendgine.sol#L87 )

```solidity
87: if (liquidity > totalLiquidity) revert CompleteUtilizationError();
89: if (totalSupply > 0 && totalLiquidityBorrowed == 0) revert CompleteUtilizationError();
142: if (totalLiquiditySupplied == 0 && totalPositionSize > 0) revert CompleteUtilizationError();
172: if (size > positionInfo.size) revert InsufficientPositionError();
173: if (liquidity > _totalLiquidity) revert CompleteUtilizationError();
198: collateral = collateralRequested > tokensOwed ? tokensOwed : collateralRequested;
200: if (collateral > 0) {
253: uint256 dilutionLP = dilutionLPRequested > _totalLiquidityBorrowed ? _totalLiquidityBorrowed : dilutionLPRequested;
```

File: [2023-01-numoen/src/core/Pair.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Pair.sol#L59 )

```solidity
59: if (scale1 > 2 * upperBound) revert InvariantError();
102: if (amount0 > 0) SafeTransferLib.safeTransfer(token0, to, amount0);
103: if (amount1 > 0) SafeTransferLib.safeTransfer(token1, to, amount1);
122: if (amount0Out > 0) SafeTransferLib.safeTransfer(token0, to, amount0Out);
123: if (amount1Out > 0) SafeTransferLib.safeTransfer(token1, to, amount1Out);
```

File: [2023-01-numoen/src/core/libraries/Position.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/libraries/Position.sol#L50 )

```solidity
50: if (_positionInfo.size > 0) {
64: if (tokensOwed > 0) positionInfo.tokensOwed = _positionInfo.tokensOwed + tokensOwed;
```

File: [2023-01-numoen/src/libraries/FullMath.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/libraries/FullMath.sol#L37 )

```solidity
37: require(denominator > 0);
46: require(denominator > prod1);
125: if (mulmod(a, b, denominator) > 0) {
```

File: [2023-01-numoen/src/periphery/LendgineRouter.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/LendgineRouter.sol#L121 )

```solidity
121: if (collateralIn > decoded.collateralMax) revert AmountError();
```

File: [2023-01-numoen/src/periphery/LiquidityManager.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/LiquidityManager.sol#L112 )

```solidity
112: if (decoded.amount0 > 0) pay(decoded.token0, decoded.payer, msg.sender, decoded.amount0);
113: if (decoded.amount1 > 0) pay(decoded.token1, decoded.payer, msg.sender, decoded.amount1);
241: amount = params.amountRequested > position.tokensOwed ? position.tokensOwed : params.amountRequested;
```

File: [2023-01-numoen/src/periphery/Payment.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/Payment.sol#L29 )

```solidity
29: if (balanceWETH > 0) {
39: if (balanceToken > 0) {
45: if (address(this).balance > 0) SafeTransferLib.safeTransferETH(msg.sender, address(this).balance);
```

File: [2023-01-numoen/src/periphery/SwapHelper.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/SwapHelper.sol#L42 )

```solidity
42: SafeTransferLib.safeTransfer(tokenIn, msg.sender, amount0Delta > 0 ? uint256(amount0Delta) : uint256(amount1Delta));
76: amount = params.amount > 0
81: params.amount > 0 ? (uint256(params.amount), amount) : (amount, uint256(-params.amount));
111: if (params.amount > 0) {
```

### <a id=G12>[G-8]</a> `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables

##### Description

Using the addition operator instead of plus-equals saves **[113 gas](https://gist.github.com/IllIllI000/cbbfb267425b898e5be734d4008d4fe8)**

##### Recommendation

Use `<x> = <x> + <y>` instead.

##### *Instances (10):*

File: [2023-01-numoen/src/core/Lendgine.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Lendgine.sol#L91 )

```solidity
91: totalLiquidityBorrowed += liquidity;
114: totalLiquidityBorrowed -= liquidity;
176: totalPositionSize -= size;
257: rewardPerPositionStored += FullMath.mulDiv(dilutionSpeculative, 1e18, totalPositionSize);
```

File: [2023-01-numoen/src/periphery/LiquidityManager.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/LiquidityManager.sol#L178 )

```solidity
178: position.tokensOwed += FullMath.mulDiv(position.size, rewardPerPositionPaid - position.rewardPerPositionPaid, 1e18);
180: position.size += size;
214: position.tokensOwed += FullMath.mulDiv(position.size, rewardPerPositionPaid - position.rewardPerPositionPaid, 1e18);
216: position.size -= params.size;
238: position.tokensOwed += FullMath.mulDiv(position.size, rewardPerPositionPaid - position.rewardPerPositionPaid, 1e18);
242: position.tokensOwed -= amount;
```