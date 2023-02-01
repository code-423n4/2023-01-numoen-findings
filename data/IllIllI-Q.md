## Summary

### Low Risk Issues
| |Issue|Instances|
|-|:-|:-:|
| [L&#x2011;01] | Loss of precision | 1 | 
| [L&#x2011;02] | `abi.encodePacked()` should not be used with dynamic types when passing the result to a hash function such as `keccak256()` | 1 | 

Total: 2 instances over 2 issues


### Non-critical Issues
| |Issue|Instances|
|-|:-|:-:|
| [N&#x2011;01] | `2**<n> - 1` should be re-written as `type(uint<n>).max` | 1 | 
| [N&#x2011;02] | `constant`s should be defined rather than using magic numbers | 27 | 
| [N&#x2011;03] | Use a more recent version of solidity | 1 | 
| [N&#x2011;04] | Use a more recent version of solidity | 2 | 
| [N&#x2011;05] | Inconsistent method of specifying a floating pragma | 1 | 
| [N&#x2011;06] | Non-library/interface files should use fixed compiler versions, not floating ones | 4 | 
| [N&#x2011;07] | Using `>`/`>=` without specifying an upper bound is unsafe | 3 | 
| [N&#x2011;08] | Typos | 1 | 
| [N&#x2011;09] | File does not contain an SPDX Identifier | 1 | 
| [N&#x2011;10] | Misplaced SPDX identifier | 1 | 
| [N&#x2011;11] | File is missing NatSpec | 3 | 
| [N&#x2011;12] | NatSpec is incomplete | 3 | 
| [N&#x2011;13] | Not using the named return variables anywhere in the function is confusing | 2 | 
| [N&#x2011;14] | Duplicated `require()`/`revert()` checks should be refactored to a modifier or function | 1 | 
| [N&#x2011;15] | Contracts should have full test coverage | 1 | 
| [N&#x2011;16] | Large or complicated code bases should implement fuzzing tests | 1 | 
| [N&#x2011;17] | Function ordering does not follow the Solidity style guide | 3 | 
| [N&#x2011;18] | Contract does not follow the Solidity style guide's suggested layout ordering | 8 | 

Total: 64 instances over 18 issues





## Low Risk Issues

### [L&#x2011;01]  Loss of precision
Division by large numbers may result in the result being zero, due to solidity not supporting fractions. Consider requiring a minimum amount for the numerator to ensure that it is always larger than the denominator

*There is 1 instance of this issue:*

```solidity
File: src/core/JumpRate.sol

42:       return (borrowedLiquidity * 1e18) / totalLiquidity;

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/core/JumpRate.sol#L42

### [L&#x2011;02]  `abi.encodePacked()` should not be used with dynamic types when passing the result to a hash function such as `keccak256()`
Use `abi.encode()` instead which will pad items to 32 bytes, which will [prevent hash collisions](https://docs.soliditylang.org/en/v0.8.13/abi-spec.html#non-standard-packed-mode) (e.g. `abi.encodePacked(0x123,0x456)` => `0x123456` => `abi.encodePacked(0x1,0x23456)`, but `abi.encode(0x123,0x456)` => `0x0...1230...456`). "Unless there is a compelling reason, `abi.encode` should be preferred". If there is only one argument to `abi.encodePacked()` it can often be cast to `bytes()` or `bytes32()` [instead](https://ethereum.stackexchange.com/questions/30912/how-to-compare-strings-in-solidity#answer-82739).

If all arguments are strings and or bytes, `bytes.concat()` should be used instead

*There is 1 instance of this issue:*

```solidity
File: src/periphery/libraries/LendgineAddress.sol

24              keccak256(
25                abi.encodePacked(
26                  hex"ff",
27                  factory,
28                  keccak256(abi.encode(token0, token1, token0Exp, token1Exp, upperBound)),
29                  bytes32(INIT_CODE_HASH)
30                )
31:             )

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/periphery/libraries/LendgineAddress.sol#L24-L31

## Non-critical Issues

### [N&#x2011;01]  `2**<n> - 1` should be re-written as `type(uint<n>).max`
Earlier versions of solidity can use `uint<n>(-1)` instead. Expressions not including the `- 1` can often be re-written to accomodate the change (e.g. by using a `>` rather than a `>=`, which will also save some gas)

*There is 1 instance of this issue:*

```solidity
File: src/libraries/SafeCast.sol

16:       require(y < 2 ** 255);

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/libraries/SafeCast.sol#L16

### [N&#x2011;02]  `constant`s should be defined rather than using magic numbers
Even [assembly](https://github.com/code-423n4/2022-05-opensea-seaport/blob/9d7ce4d08bf3c3010304a0476a785c70c0e90ae7/contracts/lib/TokenTransferrer.sol#L35-L39) can benefit from using readable constants instead of hex/numeric literals

*There are 27 instances of this issue:*

```solidity
File: src/core/Factory.sol

/// @audit 18
/// @audit 6
/// @audit 18
/// @audit 6
77:       if (token0Exp > 18 || token0Exp < 6 || token1Exp > 18 || token1Exp < 6) revert ScaleError();

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/core/Factory.sol#L77

```solidity
File: src/core/JumpRate.sol

/// @audit 1e18
17:         return (util * multiplier) / 1e18;

/// @audit 1e18
19:         uint256 normalRate = (kink * multiplier) / 1e18;

/// @audit 1e18
21:         return ((excessUtil * jumpMultiplier) / 1e18) + normalRate;

/// @audit 1e18
37:       return (borrowRate * util) / 1e18;

/// @audit 1e18
42:       return (borrowedLiquidity * 1e18) / totalLiquidity;

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/core/JumpRate.sol#L17

```solidity
File: src/core/Lendgine.sol

/// @audit 1e18
225:      return FullMath.mulDiv(collateral * token1Scale, 1e18, 2 * upperBound);

/// @audit 1e18
230:      return FullMath.mulDiv(liquidity, 2 * upperBound, 1e18) / token1Scale;

/// @audit 1e18
/// @audit 365
252:      uint256 dilutionLPRequested = (FullMath.mulDiv(borrowRate, _totalLiquidityBorrowed, 1e18) * timeElapsed) / 365 days;

/// @audit 1e18
257:      rewardPerPositionStored += FullMath.mulDiv(dilutionSpeculative, 1e18, totalPositionSize);

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/core/Lendgine.sol#L225

```solidity
File: src/core/Pair.sol

/// @audit 1e18
56:       uint256 scale0 = FullMath.mulDiv(amount0, 1e18, liquidity) * token0Scale;

/// @audit 1e18
57:       uint256 scale1 = FullMath.mulDiv(amount1, 1e18, liquidity) * token1Scale;

/// @audit 1e18
61:       uint256 a = scale0 * 1e18;

/// @audit 4
63:       uint256 c = (scale1 * scale1) / 4;

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/core/Pair.sol#L56

```solidity
File: src/libraries/Balance.sol

/// @audit 32
15:       if (!success || data.length < 32) revert BalanceReturnError();

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/libraries/Balance.sol#L15

```solidity
File: src/libraries/SafeCast.sol

/// @audit 255
16:       require(y < 2 ** 255);

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/libraries/SafeCast.sol#L16

```solidity
File: src/periphery/LiquidityManager.sol

/// @audit 1e18
178:      position.tokensOwed += FullMath.mulDiv(position.size, rewardPerPositionPaid - position.rewardPerPositionPaid, 1e18);

/// @audit 1e18
214:      position.tokensOwed += FullMath.mulDiv(position.size, rewardPerPositionPaid - position.rewardPerPositionPaid, 1e18);

/// @audit 1e18
238:      position.tokensOwed += FullMath.mulDiv(position.size, rewardPerPositionPaid - position.rewardPerPositionPaid, 1e18);

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/periphery/LiquidityManager.sol#L178

```solidity
File: src/periphery/UniswapV2/libraries/UniswapV2Library.sol

/// @audit 997
62:       uint256 amountInWithFee = amountIn * 997;

/// @audit 1000
64:       uint256 denominator = (reserveIn * 1000) + amountInWithFee;

/// @audit 1000
80:       uint256 numerator = reserveIn * amountOut * 1000;

/// @audit 997
81:       uint256 denominator = (reserveOut - amountOut) * 997;

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L62

### [N&#x2011;03]  Use a more recent version of solidity
Use a solidity version of at least 0.8.13 to get the ability to use `using for` with a list of free functions

*There is 1 instance of this issue:*

```solidity
File: src/core/Lendgine.sol

2:    pragma solidity ^0.8.4;

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/core/Lendgine.sol#L2

### [N&#x2011;04]  Use a more recent version of solidity
Use a solidity version of at least 0.8.4 to get `bytes.concat()` instead of `abi.encodePacked(<bytes>,<bytes>)`
Use a solidity version of at least 0.8.12 to get `string.concat()` instead of `abi.encodePacked(<str>,<str>)`

*There are 2 instances of this issue:*

```solidity
File: src/periphery/libraries/LendgineAddress.sol

2:    pragma solidity >=0.5.0;

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/periphery/libraries/LendgineAddress.sol#L2

```solidity
File: src/periphery/UniswapV2/libraries/UniswapV2Library.sol

1:    pragma solidity >=0.8.0;

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L1

### [N&#x2011;05]  Inconsistent method of specifying a floating pragma
Some files use `>=`, some use `^`. The instances below are examples of the method that has the fewest instances for a specific version. Note that using `>=` without also specifying `<=` will lead to failures to compile, or external project incompatability, when the major version changes and there are breaking-changes, so `^` should be preferred regardless of the instance counts

*There is 1 instance of this issue:*

```solidity
File: src/periphery/UniswapV2/libraries/UniswapV2Library.sol

1:    pragma solidity >=0.8.0;

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L1

### [N&#x2011;06]  Non-library/interface files should use fixed compiler versions, not floating ones

*There are 4 instances of this issue:*

```solidity
File: src/core/Factory.sol

2:    pragma solidity ^0.8.4;

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/core/Factory.sol#L2

```solidity
File: src/core/Lendgine.sol

2:    pragma solidity ^0.8.4;

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/core/Lendgine.sol#L2

```solidity
File: src/periphery/LendgineRouter.sol

2:    pragma solidity ^0.8.4;

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/periphery/LendgineRouter.sol#L2

```solidity
File: src/periphery/LiquidityManager.sol

2:    pragma solidity ^0.8.4;

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/periphery/LiquidityManager.sol#L2

### [N&#x2011;07]  Using `>`/`>=` without specifying an upper bound is unsafe
There _will_ be breaking changes in future versions of solidity, and at that point your code will no longer be compatable. While you may have the specific version to use in a configuration file, others that include your source files may not.

*There are 3 instances of this issue:*

```solidity
File: src/core/libraries/PositionMath.sol

2:    pragma solidity >=0.5.0;

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/core/libraries/PositionMath.sol#L2

```solidity
File: src/periphery/libraries/LendgineAddress.sol

2:    pragma solidity >=0.5.0;

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/periphery/libraries/LendgineAddress.sol#L2

```solidity
File: src/periphery/UniswapV2/libraries/UniswapV2Library.sol

1:    pragma solidity >=0.8.0;

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L1

### [N&#x2011;08]  Typos

*There is 1 instance of this issue:*

```solidity
File: src/periphery/LiquidityManager.sol

/// @audit liqudity
229:    /// @notice Collects interest owed to the callers liqudity position

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/periphery/LiquidityManager.sol#L229

### [N&#x2011;09]  File does not contain an SPDX Identifier

*There is 1 instance of this issue:*

```solidity
File: src/periphery/UniswapV2/libraries/UniswapV2Library.sol

0:    pragma solidity >=0.8.0;

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L0

### [N&#x2011;10]  Misplaced SPDX identifier
The SPDX identifier should be on the very first line of the file

*There is 1 instance of this issue:*

```solidity
File: src/periphery/UniswapV2/libraries/UniswapV2Library.sol

1:    pragma solidity >=0.8.0;

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L1

### [N&#x2011;11]  File is missing NatSpec

*There are 3 instances of this issue:*

```solidity
File: src/core/Factory.sol


```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/core/Factory.sol

```solidity
File: src/core/ImmutableState.sol


```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/core/ImmutableState.sol

```solidity
File: src/core/JumpRate.sol


```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/core/JumpRate.sol

### [N&#x2011;12]  NatSpec is incomplete

*There are 3 instances of this issue:*

```solidity
File: src/core/libraries/Position.sol

/// @audit Missing: '@param position'
/// @audit Missing: '@return'
67      /// @notice Helper function for determining the amount of tokens owed to a position
68      /// @param rewardPerPosition The global accrued interest
69:     function newTokensOwed(Position.Info memory position, uint256 rewardPerPosition) internal pure returns (uint256) {

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/core/libraries/Position.sol#L67-L69

```solidity
File: src/periphery/SwapHelper.sol

/// @audit Missing: '@param params'
65      /// @notice Handles swaps on Uniswap V2 or V3
66      /// @param swapType A selector for UniswapV2 or V3
67      /// @param data Extra data that is not used by all types of swaps
68      /// @return amount The amount in or amount out depending on whether the call was exact in or exact out
69:     function swap(SwapType swapType, SwapParams memory params, bytes memory data) internal returns (uint256 amount) {

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/periphery/SwapHelper.sol#L65-L69

### [N&#x2011;13]  Not using the named return variables anywhere in the function is confusing
Consider changing the variable to be an unnamed one

*There are 2 instances of this issue:*

```solidity
File: src/core/JumpRate.sol

/// @audit rate
25      function getSupplyRate(
26        uint256 borrowedLiquidity,
27        uint256 totalLiquidity
28      )
29        external
30        pure
31        override
32:       returns (uint256 rate)

/// @audit rate
40:     function utilizationRate(uint256 borrowedLiquidity, uint256 totalLiquidity) private pure returns (uint256 rate) {

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/core/JumpRate.sol#L25-L32

### [N&#x2011;14]  Duplicated `require()`/`revert()` checks should be refactored to a modifier or function
The compiler will inline the function, which will avoid `JUMP` instructions usually associated with functions

*There is 1 instance of this issue:*

```solidity
File: src/periphery/UniswapV2/libraries/UniswapV2Library.sol

79:       require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L79

### [N&#x2011;15]  Contracts should have full test coverage
While 100% code coverage does not guarantee that there are no bugs, it often will catch easy-to-find bugs, and will ensure that there are fewer regressions when the code invariably has to be modified. Furthermore, in order to get full coverage, code authors will often have to re-organize their code so that it is more modular, so that each component can be tested separately, which reduces interdependencies between modules and layers, and makes for code that is easier to reason about and audit.

*There is 1 instance of this issue:*

```solidity
File: Various Files


```

### [N&#x2011;16]  Large or complicated code bases should implement fuzzing tests
Large code bases, or code with lots of inline-assembly, complicated math, or complicated interactions between multiple contracts, should implement [fuzzing tests](https://medium.com/coinmonks/smart-contract-fuzzing-d9b88e0b0a05). Fuzzers such as Echidna require the test writer to come up with invariants which should not be violated under any circumstances, and the fuzzer tests various inputs and function calls to ensure that the invariants always hold. Even code with 100% code coverage can still have bugs due to the order of the operations a user performs, and fuzzers, with properly and extensively-written invariants, can close this testing gap significantly.

*There is 1 instance of this issue:*

```solidity
File: Various Files


```

### [N&#x2011;17]  Function ordering does not follow the Solidity style guide
According to the [Solidity style guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions), functions should be laid out in the following order :`constructor()`, `receive()`, `fallback()`, `external`, `public`, `internal`, `private`, but the cases below do not follow this pattern

*There are 3 instances of this issue:*

```solidity
File: src/core/JumpRate.sol

/// @audit getBorrowRate() came earlier
25      function getSupplyRate(
26        uint256 borrowedLiquidity,
27        uint256 totalLiquidity
28      )
29        external
30        pure
31        override
32:       returns (uint256 rate)

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/core/JumpRate.sol#L25-L32

```solidity
File: src/core/Pair.sol

/// @audit burn() came earlier
116:    function swap(address to, uint256 amount0Out, uint256 amount1Out, bytes calldata data) external override nonReentrant {

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/core/Pair.sol#L116

```solidity
File: src/periphery/Payment.sol

/// @audit sweepToken() came earlier
44      function refundETH() external payable {
45:       if (address(this).balance > 0) SafeTransferLib.safeTransferETH(msg.sender, address(this).balance);

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/periphery/Payment.sol#L44-L45

### [N&#x2011;18]  Contract does not follow the Solidity style guide's suggested layout ordering
The [style guide](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#order-of-layout) says that, within a contract, the ordering should be 1) Type declarations, 2) State variables, 3) Events, 4) Modifiers, and 5) Functions, but the contract(s) below do not follow this ordering

*There are 8 instances of this issue:*

```solidity
File: src/core/Factory.sol

/// @audit event LendgineCreated came earlier
39      mapping(address => mapping(address => mapping(uint256 => mapping(uint256 => mapping(uint256 => address)))))
40        public
41:       override getLendgine;

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/core/Factory.sol#L39-L41

```solidity
File: src/core/Lendgine.sol

/// @audit event Collect came earlier
56:     mapping(address => Position.Info) public override positions;

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/core/Lendgine.sol#L56

```solidity
File: src/core/Pair.sol

/// @audit event Swap came earlier
40:     uint120 public override reserve0;

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/core/Pair.sol#L40

```solidity
File: src/periphery/LendgineRouter.sol

/// @audit event Burn came earlier
43:     address public immutable factory;

/// @audit function constructor came earlier
65      modifier checkDeadline(uint256 deadline) {
66        if (deadline < block.timestamp) revert LivelinessError();
67        _;
68:     }

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/periphery/LendgineRouter.sol#L43

```solidity
File: src/periphery/LiquidityManager.sol

/// @audit event Collect came earlier
60:     address public immutable factory;

/// @audit function constructor came earlier
83      modifier checkDeadline(uint256 deadline) {
84        if (deadline < block.timestamp) revert LivelinessError();
85        _;
86:     }

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/periphery/LiquidityManager.sol#L60

```solidity
File: src/periphery/SwapHelper.sol

/// @audit function uniswapV3SwapCallback came earlier
49      enum SwapType {
50        UniswapV2,
51        UniswapV3
52:     }

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/periphery/SwapHelper.sol#L49-L52


___

## Excluded findings
These findings are excluded from awards calculations because there are publicly-available automated tools that find them. The valid ones appear here for completeness

## Summary

### Low Risk Issues
| |Issue|Instances|
|-|:-|:-:|
| [L&#x2011;01] | Missing checks for `address(0x0)` when assigning values to `address` state variables | 5 | 
| [L&#x2011;02] | `abi.encodePacked()` should not be used with dynamic types when passing the result to a hash function such as `keccak256()` | 1 | 

Total: 6 instances over 2 issues


### Non-critical Issues
| |Issue|Instances|
|-|:-|:-:|
| [N&#x2011;01] | `require()`/`revert()` statements should have descriptive reason strings | 3 | 
| [N&#x2011;02] | `constant`s should be defined rather than using magic numbers | 2 | 
| [N&#x2011;03] | Event is missing `indexed` fields | 10 | 

Total: 15 instances over 3 issues





## Low Risk Issues

### [L&#x2011;01]  Missing checks for `address(0x0)` when assigning values to `address` state variables

*There are 5 instances of this issue:*

```solidity
File: src/periphery/LendgineRouter.sol

/// @audit (valid but excluded finding)
58:       factory = _factory;

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/periphery/LendgineRouter.sol#L58

```solidity
File: src/periphery/LiquidityManager.sol

/// @audit (valid but excluded finding)
76:       factory = _factory;

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/periphery/LiquidityManager.sol#L76

```solidity
File: src/periphery/Payment.sol

/// @audit (valid but excluded finding)
18:       weth = _weth;

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/periphery/Payment.sol#L18

```solidity
File: src/periphery/SwapHelper.sol

/// @audit (valid but excluded finding)
30:       uniswapV2Factory = _uniswapV2Factory;

/// @audit (valid but excluded finding)
31:       uniswapV3Factory = _uniswapV3Factory;

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/periphery/SwapHelper.sol#L30

### [L&#x2011;02]  `abi.encodePacked()` should not be used with dynamic types when passing the result to a hash function such as `keccak256()`
Use `abi.encode()` instead which will pad items to 32 bytes, which will [prevent hash collisions](https://docs.soliditylang.org/en/v0.8.13/abi-spec.html#non-standard-packed-mode) (e.g. `abi.encodePacked(0x123,0x456)` => `0x123456` => `abi.encodePacked(0x1,0x23456)`, but `abi.encode(0x123,0x456)` => `0x0...1230...456`). "Unless there is a compelling reason, `abi.encode` should be preferred". If there is only one argument to `abi.encodePacked()` it can often be cast to `bytes()` or `bytes32()` [instead](https://ethereum.stackexchange.com/questions/30912/how-to-compare-strings-in-solidity#answer-82739).

If all arguments are strings and or bytes, `bytes.concat()` should be used instead

*There is 1 instance of this issue:*

```solidity
File: src/periphery/UniswapV2/libraries/UniswapV2Library.sol

/// @audit (valid but excluded finding)
22              keccak256(
23                abi.encodePacked(
24                  hex"ff",
25                  factory,
26                  keccak256(abi.encodePacked(token0, token1)),
27                  hex"e18a34eb0e04b04f7a0ac29a6e80748dca96319b42c54d679cb821dca90c6303" // init code hash
28                )
29:             )

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L22-L29

## Non-critical Issues

### [N&#x2011;01]  `require()`/`revert()` statements should have descriptive reason strings

*There are 3 instances of this issue:*

```solidity
File: src/libraries/SafeCast.sol

/// @audit (valid but excluded finding)
9:        require((z = uint120(y)) == y);

/// @audit (valid but excluded finding)
16:       require(y < 2 ** 255);

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/libraries/SafeCast.sol#L9

```solidity
File: src/periphery/SwapHelper.sol

/// @audit (valid but excluded finding)
116:          require(amountOutReceived == params.amount);

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/periphery/SwapHelper.sol#L116

### [N&#x2011;02]  `constant`s should be defined rather than using magic numbers
Even [assembly](https://github.com/code-423n4/2022-05-opensea-seaport/blob/9d7ce4d08bf3c3010304a0476a785c70c0e90ae7/contracts/lib/TokenTransferrer.sol#L35-L39) can benefit from using readable constants instead of hex/numeric literals

*There are 2 instances of this issue:*

```solidity
File: src/core/ImmutableState.sol

/// @audit 18 - (valid but excluded finding)
35:       token0Scale = 10 ** (18 - _token0Exp);

/// @audit 18 - (valid but excluded finding)
36:       token1Scale = 10 ** (18 - _token1Exp);

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/core/ImmutableState.sol#L35

### [N&#x2011;03]  Event is missing `indexed` fields
Index event fields make the field more quickly accessible [to off-chain tools](https://ethereum.stackexchange.com/questions/40396/can-somebody-please-explain-the-concept-of-event-indexing) that parse events. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). Each `event` should use three `indexed` fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

*There are 10 instances of this issue:*

```solidity
File: src/core/Lendgine.sol

/// @audit (valid but excluded finding)
25:     event Mint(address indexed sender, uint256 collateral, uint256 shares, uint256 liquidity, address indexed to);

/// @audit (valid but excluded finding)
27:     event Burn(address indexed sender, uint256 collateral, uint256 shares, uint256 liquidity, address indexed to);

/// @audit (valid but excluded finding)
29:     event Deposit(address indexed sender, uint256 size, uint256 liquidity, address indexed to);

/// @audit (valid but excluded finding)
31:     event Withdraw(address indexed sender, uint256 size, uint256 liquidity, address indexed to);

/// @audit (valid but excluded finding)
33:     event AccrueInterest(uint256 timeElapsed, uint256 collateral, uint256 liquidity);

/// @audit (valid but excluded finding)
35:     event AccruePositionInterest(address indexed owner, uint256 rewardPerPosition);

/// @audit (valid but excluded finding)
37:     event Collect(address indexed owner, address indexed to, uint256 amount);

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/core/Lendgine.sol#L25

```solidity
File: src/core/Pair.sol

/// @audit (valid but excluded finding)
21:     event Mint(uint256 amount0In, uint256 amount1In, uint256 liquidity);

/// @audit (valid but excluded finding)
23:     event Burn(uint256 amount0Out, uint256 amount1Out, uint256 liquidity, address indexed to);

/// @audit (valid but excluded finding)
25:     event Swap(uint256 amount0Out, uint256 amount1Out, uint256 amount0In, uint256 amount1In, address indexed to);

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/core/Pair.sol#L21
