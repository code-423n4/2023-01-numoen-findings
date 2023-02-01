## Gas Optimizations


| |Issue|Instances|
|-|:-|:-:|
| [GAS-1](#GAS-1) | USE CALLDATA INSTEAD OF MEMORY FOR FUNCTION ARGUMENTS THAT DO NOT GET MUTATED | 1 |
| [GAS-2](#GAS-2) | USAGE OF UINTS/INTS SMALLER THAN 32 BYTES (256 BITS) INCURS OVERHEAD | 3 |
| [GAS-3](#GAS-3) | X += Y COSTS MORE GAS THAN X = X + Y FOR STATE VARIABLES | 10 |
| [GAS-4](#GAS-4) | SPLITTING REQUIRE() STATEMENTS THAT USE && SAVES GAS | 2 |
| [GAS-5](#GAS-5) | NOT USING THE NAMED RETURN VARIABLES WHEN A FUNCTION RETURNS, WASTES DEPLOYMENT GAS | 3 |
| [GAS-6](#GAS-6) | INTERNAL FUNCTIONS ONLY CALLED ONCE CAN BE INLINED TO SAVE GAS | 4 |
| [GAS-7](#GAS-7) | USE ASSEMBLY WHEN GETTING A CONTRACT’S BALANCE OF ETH | 3 |

### [GAS-1] USE CALLDATA INSTEAD OF MEMORY FOR FUNCTION ARGUMENTS THAT DO NOT GET MUTATED

Mark data types as `calldata` instead of `memory` where possible. This makes it so that the data is not automatically loaded into memory. If the data passed into the function does not need to be changed (like updating values in an array), it can be passed in as `calldata`. The one exception to this is if the argument must later be passed into another function that takes an argument that specifies `memory` storage.

*Instances (1):*
```solidity
File: src/core/libraries/Position.sol

69:     function newTokensOwed(Position.Info memory position, uint256 rewardPerPosition) internal pure returns (uint256) {

```
[Link to Code](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/libraries/Position.sol#L69)

### [GAS-2] USAGE OF UINTS/INTS SMALLER THAN 32 BYTES (256 BITS) INCURS OVERHEAD

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

*Instances (3):*
```solidity
File: src/core/Pair.sol

40:     uint120 public override reserve0;

43:     uint120 public override reserve1;

```
[Link to Code](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Pair.sol#L40-L43)

```solidity
File: src/periphery/SwapHelper.sol

62:     uint24 fee;

```
[Link to Code](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/SwapHelper.sol#L62)

### [GAS-3] X += Y COSTS MORE GAS THAN X = X + Y FOR STATE VARIABLES

Same goes subtraction as well. X -= Y costs more Gas than X = X - Y.

*Instances (10):*
```solidity
File: src/core/Lendgine.sol

91:     totalLiquidityBorrowed += liquidity;

114:    totalLiquidityBorrowed -= liquidity;

176:    totalPositionSize -= size;

257:    rewardPerPositionStored += FullMath.mulDiv(dilutionSpeculative, 1e18, totalPositionSize);

```
[Link to Code](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol)

```solidity
File: src/periphery/LiquidityManager.sol

178:     position.tokensOwed += FullMath.mulDiv(position.size, rewardPerPositionPaid - position.rewardPerPositionPaid, 1e18);

180:     position.size += size;

214:     position.tokensOwed += FullMath.mulDiv(position.size, rewardPerPositionPaid - position.rewardPerPositionPaid, 1e18);

216:     position.size -= params.size;

238:     position.tokensOwed += FullMath.mulDiv(position.size, rewardPerPositionPaid - position.rewardPerPositionPaid, 1e18);

242:     position.tokensOwed -= amount;

```
[Link to Code](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol)

### [GAS-4] SPLITTING REQUIRE() STATEMENTS THAT USE && SAVES GAS

Saves 16 gas per instance. 

*Instances (2):*
```solidity
File: src/periphery/UniswapV2/libraries/UniswapV2Library.sol

61:     require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");

79:     require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");

```
[Link to Code](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol)

### [GAS-5] NOT USING THE NAMED RETURN VARIABLES WHEN A FUNCTION RETURNS, WASTES DEPLOYMENT GAS

*Instances (3):*
```solidity
File: src/core/JumpRate.sol

13:     function getBorrowRate(uint256 borrowedLiquidity, uint256 totalLiquidity) public pure override returns (uint256 rate) {

32:     returns (uint256 rate)

40:     function utilizationRate(uint256 borrowedLiquidity, uint256 totalLiquidity) private pure returns (uint256 rate) {

```
[Link to Code](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol)

### [GAS-6] INTERNAL FUNCTIONS ONLY CALLED ONCE CAN BE INLINED TO SAVE GAS

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

*Instances (4):*
```solidity
File: src/core/libraries/Position.sol

69:    function newTokensOwed(Position.Info memory position, uint256 rewardPerPosition) internal pure returns (uint256) {

73:    function convertLiquidityToPosition(

86:    function convertPositionToLiquidity(

```
[Link to Code](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/libraries/Position.sol)

```solidity
File: src/core/libraries/PositionMath.sol

12:    function addDelta(uint256 x, int256 y) internal pure returns (uint256 z) {

```
[Link to Code](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/libraries/PositionMath.sol#L12)

### [GAS-7] USE ASSEMBLY WHEN GETTING A CONTRACT’S BALANCE OF ETH.

You can use `selfbalance()` instead of `address(this).balance` when getting your contract’s balance of ETH to save gas.

*Instance (3):*
```solidity
File: src/periphery/Payments.sol

45:     if (address(this).balance > 0) SafeTransferLib.safeTransferETH(msg.sender, address(this).balance);

53:     if (token == weth && address(this).balance >= value) {

```
[Link to Code](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/Payments.sol)
