# Gas Optimizations Report

|         | Issue                                                                           | Instances |
| ------- | :------------------------------------------------------------------------------ | :-------: |
| [G-001] | x += y or x -= y costs more gas than x = x + y or x = x - y for state variables |     3     |
| [G-002] | Splitting require() statements that use `&&` saves gas                          |     2     |
| [G-003] | Multiple accesses of a `mapping/array` should use a local variable cache        |     3     |

## [G-001] x += y or x -= y costs more gas than x = x + y or x = x - y for state variables

### Impact

Using the addition operator instead of plus-equals saves 113 gas. Usually does not work with struct and mappings.

### Findings

Total:3

[src/core/Lendgine.sol#L91](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L91)

```solidity
91:    totalLiquidityBorrowed += liquidity;
```

[src/core/Lendgine.sol#L114](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L114)

```solidity
114:    totalLiquidityBorrowed -= liquidity;
```

[src/core/Lendgine.sol#L176](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L176)

```solidity
176:    totalPositionSize -= size;
```

## [G-002] Splitting require() statements that use `&&` saves gas

### Impact

When using `&&` in a `require()` statement, the entire statement will be evaluated before the transaction reverts. If the first condition is false, the second condition will not be evaluated. This means that if the first condition is false, the second condition will not be evaluated, which is a waste of gas. Splitting the `require()` statement into two separate statements will save gas.

### Findings

Total:2

[src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L61](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L61)

```solidity
61:    require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");
```

[src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L79](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L79)

```solidity
79:    require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");
```

### Recommendation

Same as description

## [G-003] Multiple accesses of a `mapping/array` should use a local variable cache

### Impact

The instances below point to the second+ access of a value inside a mapping/array, within a function. Caching a mapping's value in a local storage or calldata variable when the value is accessed multiple times, saves ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations. Caching an array's struct avoids recalculating the array offsets into `memory/calldata` 

### Findings

Total:3

[src/periphery/LiquidityManager.sol#L182](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L182)

```solidity
182:    positions[params.recipient][lendgine] = position;
```

[src/periphery/LiquidityManager.sol#L218](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L218)

```solidity
218:    positions[msg.sender][lendgine] = position;
```

[src/periphery/LiquidityManager.sol#L244](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L244)

```solidity
244:    positions[msg.sender][params.lendgine] = position;
```
