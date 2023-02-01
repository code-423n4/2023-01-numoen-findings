# Report
## Gas Optimizations ##
### [G-1]: Place subtractions where the operands can't underflow in unchecked {} block
**Context:**

1. ```position.tokensOwed = tokensOwed - collateral; // SSTORE``` [L201](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L201) 
1. ```totalLiquidityBorrowed = _totalLiquidityBorrowed - dilutionLP;``` [L256](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L256) 

**Description:**

Some gas can be saved by using an unchecked {} block if an underflow isn't possible because of a previous require() or if-statement.
