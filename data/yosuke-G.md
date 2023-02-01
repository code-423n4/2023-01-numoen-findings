## [YO GAS-1] USE CUSTOM ERRORS RATHER THAN REVERT()/REQUIRE() STRINGS TO SAVE GAS

### Handle
yosuke

## Vulnerability details
### Impact
Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they’re hit by avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas.

### Proof of Concept
https://github.com/code-423n4/2023-01-numoen/blob/main/src/libraries/SafeCast.sol#L9
https://github.com/code-423n4/2023-01-numoen/blob/main/src/libraries/SafeCast.sol#L16
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/SwapHelper.sol#L116


### Recommended Mitigation Steps
USE CUSTOM ERRORS RATHER THAN REVERT()/REQUIRE() STRINGS TO SAVE GAS

## [YO GAS-2] Using private rather than public for constants and immutable, saves gas

### Handle
yosuke

## Vulnerability details
### Impact

### Proof of Concept
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L60
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LendgineRouter.sol#L43
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/ImmutableState.sol#L10-L25
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/SwapHelper.sol#L21-L23
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/Payment.sol#L13

### Recommended Mitigation Steps
ex)
before
```solidity=
address public immutable weth;
```
after
```solidity=
address private immutable weth;
```

## [YO GAS-3] **Use shift Right/Left instead of division/multiplication if possible**

### Handle
yosuke

## Vulnerability details
### Impact

### Proof of Concept
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Pair.sol#L59

### Recommended Mitigation Steps
ex)
before

```solidity
a = b / 256;
c = d * 16;
```

after

```solidity
a = b >> 8;
c = d << 4;
```

## [YO GAS-4] It is cheaper in gas to use "value!=0" instead of "value>0" for comparing variables of type uint.

### Handle
yosuke

## Vulnerability details
### Impact

### Proof of Concept
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L112
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L113


### Recommended Mitigation Steps
ex)
before
```solidity=

```
after
```solidity=

```

## [YO GAS-5]**`X = X + Y`, `X = X - Y`, `X = X * Y`, `X = X / Y` ARE MORE EFFICIENT, THAN `X += Y`, `X -= Y`, `X *= Y`, `X /= Y`**

### Handle
yosuke

## Vulnerability details
### Impact

### Proof of Concept
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L91
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L114
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L176
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L257
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L178
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L180
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L214
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L216
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L238
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L242


### Recommended Mitigation Steps
ex)
before
```solidity=
position.tokensOwed -= amount;
```
after
```solidity=
position.tokensOwed = position.tokensOwed - amount;
```

## [YO GAS-6] USE NAMED RETURNS WHERE APPROPRIATE

### Handle
yosuke

## Vulnerability details
### Impact

### Proof of Concept
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L213

Exception
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol#L40-L43

### Recommended Mitigation Steps
ex)
before
```solidity=
function convertLiquidityToShare(uint256 liquidity) public view override returns (uint256) {
    uint256 _totalLiquidityBorrowed = totalLiquidityBorrowed; // SLOAD
    return _totalLiquidityBorrowed == 0 ? liquidity : FullMath.mulDiv(liquidity, totalSupply, _totalLiquidityBorrowed);
}
```
after
```solidity=
function convertLiquidityToShare(uint256 liquidity) public view override returns (uint256 _totalLiquidityBorrowed) {
    _totalLiquidityBorrowed = totalLiquidityBorrowed; // SLOAD
    _totalLiquidityBorrowed =_totalLiquidityBorrowed == 0 ? liquidity : FullMath.mulDiv(liquidity, totalSupply, _totalLiquidityBorrowed);
}
```

## [YO GAS-7] USE FUNCTION INSTEAD OF MODIFIERS

### Handle
yosuke

## Vulnerability details
### Impact

### Proof of Concept
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L83
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LendgineRouter.sol#L65


### Recommended Mitigation Steps
USE FUNCTION INSTEAD OF MODIFIERS because of lower gas fee.
ex)
before
```solidity=
modifier checkDeadline(uint256 deadline) {
    if (deadline < block.timestamp) revert LivelinessError();
    _;
}
```
after
```solidity=
function checkDeadline(uint256 deadline) internal {
    if (deadline < block.timestamp) revert LivelinessError();
}
```

## [YO GAS-8] Use calldata instead of memory for function arguments that do not get mutated.

### Handle
yosuke

## Vulnerability details
### Impact

### Proof of Concept
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/SwapHelper.sol#L69

### Recommended Mitigation Steps
Use calldata instead of memory for function arguments that do not get mutated because of lower gas fee.
ex)
before
```solidity=
function swap(SwapType swapType, SwapParams memory params, bytes memory data) internal returns (uint256 amount) {
```
after
```solidity=
function swap(SwapType swapType, SwapParams calldata params, bytes calldata data) internal returns (uint256 amount) {
```

## [YO GAS-9] USING BOOLS FOR STORAGE INCURS OVERHEAD

### Handle
yosuke

## Vulnerability details
### Impact
Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and  pointer aliasing, and it cannot be disabled.

### Proof of Concept
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Pair.sol#L53
https://github.com/code-423n4/2023-01-numoen/blob/main/src/libraries/Balance.sol#L13
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/SwapHelper.sol#L95

### Recommended Mitigation Steps
Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000 gas) when changing from false to true, after having been true in the past.
ex)
before
```solidity=
bool zeroForOne = params.tokenIn < params.tokenOut;
```
after
```solidity=
uint256 zeroForOne = 1; //false
if(params.tokenIn < params.tokenOut) zeroForOne = 2; //true
```

## [YO GAS-10] **SPLITTING `REQUIRE()` STATEMENTS THAT USE `&&` SAVES GAS**

### Handle
yosuke

## Vulnerability details
### Impact

### Proof of Concept
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L61
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L79


### Recommended Mitigation Steps

Instead of using operator `&&` on a single `require`. Using a two `require` can save more gas.
ex)
before
```solidity=
require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");
```
after
```solidity=
require(reserveIn > 0,"something");
require(reserveOut > 0,"something");
```

## [YO GAS-11] Gas savings by moving uint8 state variable next to an address state variable declaration

### Handle
yosuke

## Vulnerability details
### Impact
A uint8 in Solidity is internally represented as a unit8 and so required only 8 bits of the 256-bits storage slot. An address variable is 160-bits. So declaring a uint8 next to an address variable lets Solidity pack them in the same storage slot thereby using one slot instead of two.

For reference, see https://mudit.blog/solidity-gas-optimization-tips/

### Proof of Concept
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Factory.sol#L63-L69


### Recommended Mitigation Steps
Move uint8 state variable declaration next to an address state variable declaration.
ex)
before
```solidity=
function createLendgine(
    address token0,
    address token1,
    uint8 token0Exp,
    uint8 token1Exp,
    uint256 upperBound
  )
```
after
```solidity=
function createLendgine(
    uint8 token0Exp,
    uint8 token1Exp,
    address token0,
    address token1,
    uint256 upperBound
  )
```

## [YO GAS-12] USE A MORE RECENT VERSION OF SOLIDITY

### Handle
yosuke

## Vulnerability details
### Impact
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value.

[Improved Inlining Heuristics in Yul Optimizer](https://blog.soliditylang.org/2022/06/15/solidity-0.8.15-release-announcement/). The compiler used to be very conservative before Solidity version `0.8.15` in deciding whether to inline a function or not. This was necessary due to the fact that inlining may easily increase stack pressure and lead to the dreaded `Stack too deep` error. In `0.8.15` the conditions necessary for inlining are relaxed. Benchmarks show that the change significantly decreases the bytecode size (which impacts the deployment cost) while the effect on the runtime gas usage is smaller.

[Overflow checks multiplications more efficiently](https://blog.soliditylang.org/2022/09/08/solidity-0.8.17-release-announcement/) in Solidity v0.8.17. Yul Optimizer: Prevent the incorrect removal of storage writes before calls to Yul functions that conditionally terminate the external EVM call; Simplify the starting offset of zero-length operations to zero. Code Generator: More efficient overflow checks for multiplication.


### Proof of Concept
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Factory.sol#L2
All files except the following
src/core/libraries/PositionMath.sol
src/periphery/UniswapV2/libraries/UniswapV2Library.sol
src/periphery/libraries/LendgineAddress.sol


### Recommended Mitigation Steps
ex)
before

```solidity
pragma solidity ^0.8.4;
```

after

```solidity
pragma solidity 0.8.17;
```