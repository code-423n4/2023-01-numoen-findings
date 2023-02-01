## Non-Critical Issues List
Issue 
## [N-01] NON-LIBRARY/INTERFACE FILES SHOULD USE FIXED COMPILER VERSIONS, NOT FLOATING ONES
## [N-02] USE OF BYTES.CONCAT() INSTEAD OF ABI.ENCODEPACKED(,)
## [N-03] NATSPEC COMMENTS SHOULD BE INCREASED IN CONTRACTS
Total: 27  contexts over 04 issues

## [N-01] NON-LIBRARY/INTERFACE FILES SHOULD USE FIXED COMPILER VERSIONS, NOT FLOATING ONES
Description:
Pragma statements can be allowed to float when a contract is intended for consumption by other developers, as in the case with contracts in a library or EthPM package. Otherwise, the developer would need to manually update the pragma in order to compile locally.
https://swcregistry.io/docs/SWC-103
Recommendation:
Ethereum Smart Contract Best Practices - Lock pragmas to specific compiler version.
https://consensys.github.io/smart-contract-best-practices/development-recommendations/solidity-specific/locking-pragmas/
Contexts: 09
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Factory.sol#L2
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LendgineRouter.sol
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/ImmutableState.sol
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/Payment.sol
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/SwapHelper.sol
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Pair.sol

## [N-02] USE OF BYTES.CONCAT() INSTEAD OF ABI.ENCODEPACKED(,)
Rather than using abi.encodePacked for appending bytes, since version 0.8.4, bytes.concat() is enabled
Since version 0.8.4 for appending bytes, bytes.concat() can be used instead of abi.encodePacked(,).
Contexts: 03
File: src/periphery/UniswapV2/libraries/UniswapV2Library.sol
            abi.encodePacked(
              keccak256(abi.encodePacked(token0, token1)),
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L23
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L27

File: src/periphery/libraries/LendgineAddress.sol
            abi.encodePacked(
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/libraries/LendgineAddress.sol#L25

## [N-03] NATSPEC COMMENTS SHOULD BE INCREASED IN CONTRACTS
Context: 15
All Contracts
Description:
It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation.
In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.
https://docs.soliditylang.org/en/v0.8.15/natspec-format.html
Recommendation:
NatSpec comments should be increased in contracts
