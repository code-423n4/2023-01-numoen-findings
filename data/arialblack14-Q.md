# QA REPORT

---

### Summary of low risk issues


| Number     | Issue details                                                         | Instances |
| ------------ | ----------------------------------------------------------------------- | :---------: |
| [L-1](#L1) | Use of`block.timestamp`                                               |     5     |
| [L-2](#L3) | Lock pragmas to specific compiler version.                            |    13    |

*Total: 2 issues.*

### Summary of non-critical issues


| Number       | Issue details                                                    | Instances |
| -------------- | ------------------------------------------------------------------ | :---------: |
| [NC-1](#NC1) | Constants should be defined rather than using magic numbers      |     5    |
| [NC-2](#NC2) | Compliance with solidity styler rules in`constant` expressions.  |     3     |

*Total: 2 issues.*
*Note: Issue with id NC-1's instances differ from the c4udit output.*

---

### Low Risk Issues

### <a id=L1>[L-1]</a> Use of `block.timestamp`

##### Description

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the *[Entropy Illusion](https://hacken.io/discover/most-common-smart-contract-vulnerabilities/#Entropy_Illusion)* for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

##### Recommendation

Use oracle instead of `block.timestamp`

##### *Instances (5):*

File: [2023-01-numoen/src/core/Lendgine.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Lendgine.sol#L240 )

```solidity
240: lastUpdate = block.timestamp;
244: uint256 timeElapsed = block.timestamp - lastUpdate;
258: lastUpdate = block.timestamp;
```

File: [2023-01-numoen/src/periphery/LendgineRouter.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/LendgineRouter.sol#L66 )

```solidity
66: if (deadline < block.timestamp) revert LivelinessError();
```

File: [2023-01-numoen/src/periphery/LiquidityManager.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/LiquidityManager.sol#L84 )

```solidity
84: if (deadline < block.timestamp) revert LivelinessError();
```


### <a id=L3>[L-2]</a> Lock pragmas to specific compiler version.

##### Description

Pragma statements can be allowed to float when a contract is intended for consumption by other developers, as in the case with contracts in a library or EthPM package. Otherwise, the developer would need to manually update the pragma in order to compile locally.

##### Recommendation

Ethereum Smart Contract Best Practices - Lock pragmas to specific compiler version. [solidity-specific/locking-pragmas](https://consensys.github.io/smart-contract-best-practices/development-recommendations/solidity-specific/locking-pragmas/)

##### *Instances (13):*

File: [2023-01-numoen/src/core/Factory.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Factory.sol#L2 )

```solidity
2: pragma solidity ^0.8.4;
```

File: [2023-01-numoen/src/core/ImmutableState.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/ImmutableState.sol#L2 )

```solidity
2: pragma solidity ^0.8.0;
```

File: [2023-01-numoen/src/core/JumpRate.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/JumpRate.sol#L2 )

```solidity
2: pragma solidity ^0.8.0;
```

File: [2023-01-numoen/src/core/Lendgine.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Lendgine.sol#L2 )

```solidity
2: pragma solidity ^0.8.4;
```

File: [2023-01-numoen/src/core/Pair.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Pair.sol#L2 )

```solidity
2: pragma solidity ^0.8.4;
```

File: [2023-01-numoen/src/core/libraries/Position.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/libraries/Position.sol#L2 )

```solidity
2: pragma solidity ^0.8.4;
```

File: [2023-01-numoen/src/libraries/Balance.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/libraries/Balance.sol#L2 )

```solidity
2: pragma solidity ^0.8.4;
```

File: [2023-01-numoen/src/libraries/FullMath.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/libraries/FullMath.sol#L2 )

```solidity
2: pragma solidity ^0.8.0;
```

File: [2023-01-numoen/src/libraries/SafeCast.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/libraries/SafeCast.sol#L2 )

```solidity
2: pragma solidity ^0.8.0;
```

File: [2023-01-numoen/src/periphery/LendgineRouter.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/LendgineRouter.sol#L2 )

```solidity
2: pragma solidity ^0.8.4;
```

File: [2023-01-numoen/src/periphery/LiquidityManager.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/LiquidityManager.sol#L2 )

```solidity
2: pragma solidity ^0.8.4;
```

File: [2023-01-numoen/src/periphery/Payment.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/Payment.sol#L2 )

```solidity
2: pragma solidity ^0.8.4;
```

File: [2023-01-numoen/src/periphery/SwapHelper.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/SwapHelper.sol#L2 )

```solidity
2: pragma solidity ^0.8.4;
```

### Non-critical Issues

### <a id=NC1>[NC-1]</a> Constants should be defined rather than using magic numbers

##### Description

Even [`assembly`](https://github.com/code-423n4/2022-05-opensea-seaport/blob/9d7ce4d08bf3c3010304a0476a785c70c0e90ae7/contracts/lib/TokenTransferrer.sol#L35-L39) can benefit from using readable constants instead of hex/numeric literals

##### Recommendation

You should declare the variable instead of magic number.

##### *Instances (5):*

File: [2023-01-numoen/src/core/Factory.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Factory.sol#L77 )

```solidity
77: if (token0Exp > 18 || token0Exp < 6 || token1Exp > 18 || token1Exp < 6) revert ScaleError();
```

File: [2023-01-numoen/src/core/JumpRate.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/JumpRate.sol#L17 )

```solidity
17: return (util * multiplier) / 1e18;
19: uint256 normalRate = (kink * multiplier) / 1e18;
21: return ((excessUtil * jumpMultiplier) / 1e18) + normalRate;
37: return (borrowRate * util) / 1e18;
```

### <a id=NC2>[NC-2]</a> Compliance with solidity styler rules in `constant` expressions.

##### Description

Variables declared as constant utilize the UPPER_CASE_WITH_UNDERSCORES format..

##### Recommendation

TODO: Conform to the style guide

##### *Instances (3):*

File: [2023-01-numoen/src/core/JumpRate.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/JumpRate.sol#L7 )

```solidity
7: uint256 public constant override kink = 0.8 ether;
9: uint256 public constant override multiplier = 1.375 ether;
11: uint256 public constant override jumpMultiplier = 44.5 ether;
```
