# Report
## Non-Critical Issues ##
### [N-1]: Function defines a named return variable but then it uses return statements
**Context:**

1. ```return ((excessUtil * jumpMultiplier) / 1e18) + normalRate;``` [L21](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol#L21) 
1. ```return (borrowRate * util) / 1e18;``` [L37](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol#L37) 
1. ```return (borrowedLiquidity * 1e18) / totalLiquidity;``` [L42](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol#L42) 

**Recommendation:**

Choose named return variable or return statement. It is unnecessary to use both.

### [N-2]: Wrong order of functions
**Context:**

1. ```function getSupplyRate(``` [L25](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol#L25) (external function can not go after public function)
1. ```enum SwapType {``` [L49](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/SwapHelper.sol#L49) (enum definition can not go after external function)

**Description:**

According to [official solidity documentation](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions) functions should be grouped according to their visibility and ordered:

+ constructor

+ receive function (if exists)

+ fallback function (if exists)

+ external

+ public

+ internal

+ private

Within a grouping, place the view and pure functions last.

**Recommendation:**

Put the functions in the correct order according to the documentation.

### [N-3]: Typos
**Context:**

```/// @notice Collects interest owed to the callers liqudity position``` [L229](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L229) (Change **liqudity** to **liquidity**)

### [N-4]: Line is too long
**Context:**

```function collect(address to, uint256 collateralRequested) external override nonReentrant returns (uint256 collateral) {``` [L194](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L194) 

**Description:**

Maximum suggested line length is [120 characters](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-length).
