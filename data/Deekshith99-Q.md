# [QA Report]

## [NC-1] Natspec is missing
Its recommend to mention them for better understanding 
it was missing in [JumpRate.sol](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol)

## [NC - 2] USE A MORE RECENT VERSION OF SOLIDITY
For security, it is best practice to use the latest Solidity version.
currently version are set to ^0.8.4; its recommend to update them to latest stable version

## [NC-3] Use bytes.concat()
solidity version 0.8.4 introduces bytes.concat()  vs abi.encodePacked(<bytes>,<bytes>)
here are instaces of it
[Factory.sol#L82](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Factory.sol#L82)
[LiquidityManager.sol#L160](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/LiquidityManager.sol#L160)

