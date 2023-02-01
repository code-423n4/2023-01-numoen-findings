## Summary

### Gas Optimizations
| |Issue|Contexts|
|-|:-|:-|
| [GAS&#x2011;1]| `<x> += <y>` Costs More Gas Than `<x> = <x> + <y>` For State Variables | 12 |
| [GAS&#x2011;2]| Splitting `require()` Statements That Use `&&` Saves Gas | 1 |
| [GAS&#x2011;3]| Use cached state variable  | 1 |
| [GAS&#x2011;4] | Use assembly to check for zero address | 2 |

## Gas Optimizations

### Gas-1: `<x> += <y>` Costs More Gas Than `<x> = <x> + <y>` For State Variables

#### Proof Of Concept


```solidity
76: balanceOf[msg.sender] -= amount;
81: balanceOf[to] += amount;
95: balanceOf[from] -= amount;
100: balanceOf[to] += amount;
168: totalSupply += amount;
173: balanceOf[to] += amount;
180: balanceOf[from] -= amount;
185: totalSupply -= amount;
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/ERC20.sol




```solidity
91: totalLiquidityBorrowed += liquidity;
114: totalLiquidityBorrowed -= liquidity;
176: totalPositionSize -= size;
257: rewardPerPositionStored += FullMath.mulDiv(dilutionSpeculative, 1e18, totalPositionSize);
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol


### Gas-2: Splitting `require()` Statements That Use `&&` Saves Gas

#### Proof Of Concept

```solidity
139: require(recoveredAddress != address(0) && recoveredAddress == owner, "INVALID_SIGNER");
```
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/ERC20.sol#L139

### Gas-3: Use cached state variable

#### Proof Of Concept

```solidity
142: if (totalLiquiditySupplied == 0 && totalPositionSize > 0) revert CompleteUtilizationError();
```

Should use _totalPositionSize

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L142


### Gas-4: Use assembly to check for zero address

#### Proof Of Concept

```solidity
75: if (token0 == address(0) || token1 == address(0)) revert ZeroAddressError();
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Factory.sol#L75

```solidity
require(recoveredAddress != address(0) && recoveredAddress == owner, "INVALID_SIGNER");
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/ERC20.sol#L139