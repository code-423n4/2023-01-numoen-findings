## [YO NC-1] Constants should be defined rather than using magic numbers

### Handle
yosuke

## Vulnerability details
### Impact

### Proof of Concept
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Factory.sol#L77
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L225
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L230
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L252
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L178
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L214
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L238
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/ImmutableState.sol#L35
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/ImmutableState.sol#L36
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol#L17
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol#L19
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol#L21
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol#L37
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol#L42
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Pair.sol#L56
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Pair.sol#L57
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Pair.sol#L59
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Pair.sol#L61
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Pair.sol#L63
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L62
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L64
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L80
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L81
https://github.com/code-423n4/2023-01-numoen/blob/main/src/libraries/SafeCast.sol#L16
https://github.com/code-423n4/2023-01-numoen/blob/main/src/libraries/Balance.sol#L15

### Recommended Mitigation Steps
Constants should be defined rather than using magic numbers

## [YO NC-2] Event is missing indexed fields

### Handle
yosuke

## Vulnerability details
### Impact

Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). Each event should use three indexed fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

### Proof of Concept
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L25-L37
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Pair.sol#L21-L25

### Recommended Mitigation Steps

ex)

before

```solidity
event SetOwner(address pendingOwner);
```

after

```solidity
event SetOwner(address indexed pendingOwner);
```

## [YO NC-3] Unlocked Pragma

### Handle
yosuke

## Vulnerability details
### Impact

Every Solidity file specifies in the header a version number of the format pragma solidity ^0.8.0. The caret (^) before the version number implies an unlocked pragma, meaning that the compiler will use the specified version or above.

It’s usually a good idea to pin a specific version to know what compiler bug fixes and optimizations were enabled at the time of compiling the contract.

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
pragma solidity 0.8.4;
```