# 1: FLOATING PRAGMA

Vulnerability details

### Impact

Contracts should be deployed using the same compiler version/flags with which they have been tested. Locking the floating pragma, i.e. by not using ^ in pragma solidity ^0.8.4, ensures that contracts do not accidentally get deployed using an older compiler version with unfixed bugs.

For reference, see https://swcregistry.io/docs/SWC-103

## Proof of Concept

https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/ImmutableState.sol#L2 

https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/JumpRate.sol#L2 

https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/Payment.sol#L2 

https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/SwapHelper.sol#L2 

https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Pair.sol#L2 

https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Factory.sol#L2 

https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Lendgine.sol#L2 

https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/LiquidityManager.sol#L2 

## Tools Used

Manual Analysis

### Recommended Mitigation Steps

Remove ^ in “pragma solidity ^0.8.4” and change it to “pragma solidity 0.8.4” to be consistent with the rest of the contracts.

# 2: USE A MORE RECENT VERSION OF SOLIDITY 

Vulnerability details

### Context:

All contracts in scope

## Description:

For security, it is best practice to use the latest Solidity version.

For the security fix list in the versions; see 

https://github.com/ethereum/solidity/blob/develop/Changelog.md

## Tools Used

Manual Analysis

### Recommended Mitigation Steps

An old version of Solidity is used, a newer version can be used (0.8.17)


# 3: USE CONSTANTS FOR NUMBERS

Vulnerability details

### Impact

Throughout the code, numbers like 1e18  are used multiple times. It is quite easy to make a mistake somewhere, also when comparing values.

## Proof of Concept

JumpRate.sol - Instances (5)

Pair.sol - Instances (3)

Lendgine.sol - Instances (4)

LiquidityManager.sol - Instances (3)

## Tools Used

Manual Analysis

### Recommended Mitigation Steps

Recommend defining constants for the numbers used throughout the code.
