| | issue |
| ----------- | ----------- |
| 1 | [`<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables](#1-x--y-costs-more-gas-than-x--x--y-for-state-variables) |
| 2 | [not using the named return variables when a function returns, wastes deployment gas](#2-not-using-the-named-return-variables-when-a-function-returns-wastes-deployment-gas) |
| 3 | [Avoid a SLOAD optimistically](#3-avoid-a-sload-optimistically) |
| 4 | [use a more recent version of solidity](#4-use-a-more-recent-version-of-solidity) |
| 5 | [Ternary over if ... else](#5-ternary-over-if--else) |
| 6 | [use existing cached version of state var](#6-use-existing-cached-version-of-state-var) |
| 7 | [Non-strict inequalities are cheaper than strict ones](#7-non-strict-inequalities-are-cheaper-than-strict-ones) |
| 8 | [pre calculate parts of the code](#8-pre-calculate-parts-of-the-code) |
| 9 | [consider making a standard size for variables](#9-consider-making-a-standard-size-for-variables) |


## 1. `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables
Using the addition operator instead of plus-equals saves gas

- [Lendgine.sol#L91](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L91)
- [Lendgine.sol#L257](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L257)
- [Lendgine.sol#L114](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L114)
- [Lendgine.sol#L176](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L176)


## 2. not using the named return variables when a function returns, wastes deployment gas

- [JumpRate.sol#L13](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol#L13)
- [JumpRate.sol#L32](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol#L32)
- [JumpRate.sol#L40](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol#L40)


## 3. Avoid a SLOAD optimistically

There is a chance that the first part will be true so the second part doesn’t need to be checked, so it is better to use the part that we have instead of the part that needs to be called.

swapping these lines will have a chance to stop the `getLendgine[token0][token1][token0Exp][token1Exp][upperBound]` SLOAD
- [Factory.sol#L76-L77](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Factory.sol#L76-L77)


## 4. use a more recent version of solidity

Use a strict solidity version

Use a solidity version of at least 0.8.2 to get compiler automatic inlining
Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings
Use a solidity version of at least 0.8.10 to have `external` calls skip contract existence checks if the external call has a return value
Use a solidity version of at least 0.8.12 to get string.concat() to be used instead of abi.encodePacked(<str>,<str>)
Use a solidity version of at least 0.8.13 to get the ability to use `using for` with a list of free functions
Use a solidity version of at least 0.8.15 because the conditions necessary for inlining are relaxed. Benchmarks show that the change significantly decreases the bytecode size (which impacts the deployment cost) while the effect on the runtime gas usage is smaller.
Use a solidity version of at least 0.8.17 to  get prevention of the incorrect removal of storage writes before calls to Yul functions that conditionally terminate the external EVM call; Simplify the starting offset of zero-length operations to zero. More efficient overflow checks for multiplication.


## 5. Ternary over if ... else

Using ternary operator instead of the if else statement saves gas.

- [JumpRate.sol#L16-L22](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol#L16-L22)

- [LiquidityManager.sol#L147-L153](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L147-L153)

- [LendgineRouter.sol#L205-L211](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LendgineRouter.sol#L)

- [Position.sol#L56-L60](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/libraries/Position.sol#L56-60)


## 6. use existing cached version of state var

`totalPositionSize` is already cached inside the `_totalPositionSize` state var, use the cached version to save 100 gas
- [Lendgine.sol#L142](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L142)


## 7. Non-strict inequalities are cheaper than strict ones

In the EVM, there is no opcode for non-strict inequalities (>=, <=) and two operations are performed (> + = or < + =).
consider replacing >= with the strict counterpart > and add `- 1` to the second side

- [Pair.sol#L66](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Pair.sol#L66)


## 8. pre calculate parts of the code 

some parts of the code are doing operations on constants (e.g. (constantA * constantB) / 1e18) which will always give us the same answer so there is no need to calculate them every time the function is being used.

just calculate the answer to this part of the code and use it where it should be used.

`(kink * multiplier) / 1e18;` always gives the same answer. (with this method the `normalRate` stack variable wouldnt need to be made which also saves gas)
- [JumpRate.sol#L19](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol#L19)


## 9. consider making a standard size for variables 

`token0Exp` and `token1Exp` are getting different declarations in some of the contracts. as they are small numbers they can be put into a small uint and be in the same slot with `token0` and `token1` which are 20 bytes. as they are used together in the same places it will both save gas for slotting and reads and writes. (Each slot saved can avoid an extra Gsset (20000 gas) for the first setting of the struct and subsequent reads as well as writes have smaller gas savings)

saves two slots here each
- [LiquidityManager.sol#L92-101](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L92-101)
- [LiquidityManager.sol#L120-132](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L120-132)
- [LiquidityManager.sol#L187-L198](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L187-L198)

saves two slots here each
- [LendgineRouter.sol#L74-L84](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LendgineRouter.sol#L74-L84)
- [LendgineRouter.sol#L126-L139](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LendgineRouter.sol#L126-L139)
- [LendgineRouter.sol#L175-187](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LendgineRouter.sol#L175-187)
- [LendgineRouter.sol#L240-L254](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LendgineRouter.sol#L240-L254)

saves one slot here
- [Factory.sol#L47-L53](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Factory.sol#L47-L53)

