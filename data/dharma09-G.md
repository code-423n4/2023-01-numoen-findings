## Summary

**Gas Optimizations**

|  | Issue | Instances |
| --- | --- | --- |
| [G‑01] |  Used Cache value instead of storage value  | 1 |
| [G‑02] | Splitting require() statements that use && saves gas - (saves 8 gas per &&) | 2 |
| [G‑03] | Using storage to declare Struct variable inside function | 4 |
| [G-04] | Use nested if and, avoid multiple check combinations | 5 |
| [G-05] | x += y (x -= y) costs more gas than x = x + y (x = x - y) for state variables | 3 |

Total: 15 instances over 5 issues

### **[G-01] Use Cache value instead of storage value**

*There are 5 instances of this issue:*

**Affected source code:**

```diff
-176 totalPositionSize = -= size;
+176 totalPositionSize = _totalPositionSize - size; 
```

- [Lendgine.sol:176](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L176)

### **[G-02] Splitting `require()` statements that use `&&` saves gas - (saves 8 gas per &&)**

Instead of using the && operator in a single require statement to check multiple conditions,using multiple require statements with 1 condition per require statement will save 8 GAS per &&The gas difference would only be realized if the revert condition is realized(met).

*There are 2 instances of this issue:*

**Affected source code:**

- [UniswapV2Library.sol:61](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L61)
- [UniswapV2Library.sol:79](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L79)

### **[G-03] Using `storage` to declare Struct variable inside function**

instead of caching variable to memory. read it directly from storage.

*There are 4 instances of this issue:*

**Affected source code:**

```solidity
/LendgineRouter.sol
97:  MintCallbackData memory decoded = abi.decode(data, (MintCallbackData));
191: PairMintCallbackData memory decoded = abi.decode(data, (PairMintCallbackData));

/LiquidityManager.sol
105: PairMintCallbackData memory decoded = abi.decode(data, (PairMintCallbackData));

/SwapHelper.sol
90: UniV3Data memory uniV3Data = abi.decode(data, (UniV3Data));
```

### **[G-04] Use nested if and, avoid multiple check combinations**

Using nested is cheaper than using && multiple check combinations. There are more advantages, such as easier to read code and better coverage reports.

*There are 5 instances of this issue:*

**Affected source code:**

```solidity
/Lendgine.sol
89: if (totalSupply > 0 && totalLiquidityBorrowed == 0) revert CompleteUtilizationError();
142: if (totalLiquiditySupplied == 0 && totalPositionSize > 0) revert CompleteUtilizationError();

/Pair.sol
100: if (amount0 == 0 && amount1 == 0) revert InsufficientOutputError();
117: if (amount0Out == 0 && amount1Out == 0) revert InsufficientOutputError();

/Payment.sol
53: if (token == weth && address(this).balance >= value) {
```

Recommendation Code:

```diff
src\PaprController.sol#L290

- if (isLastCollateral && remaining != 0) {
+ if (isLastCollateral) {
+ if (remaining != 0) {
```

### **[G-05] `x += y (x -= y)` costs more gas than `x = x + y (x = x - y)` for state variables**

*There are 3 instances of this issue:*

**Affected source code:**
```solidity
/Lendgine.sol
91: totalLiquidityBorrowed += liquidity;
114: totalLiquidityBorrowed -= liquidity;
176: totalPositionSize -= size;
```