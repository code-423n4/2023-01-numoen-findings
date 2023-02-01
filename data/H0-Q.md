## [L-01] Missing checks for address(0x0) when assigning values to address state variables
[LendgineRouter.sol L58](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/LendgineRouter.sol#L58)
[LiquidityManager.sol L76](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/LiquidityManager.sol#L76)
[Payment.sol L18](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/Payment.sol#L18)
[SwapHelper.sol L30](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/SwapHelper.sol#L30)
[SwapHelper.sol L31](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/SwapHelper.sol#L31)

## [N-01] Function follow Solidity standard naming conventions
[Pair.sol L70](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Pair.sol#L70)
[Pair.sol L93](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Pair.sol#L93)

[Position.sol L38](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/libraries/Position.sol#L38)
[Posiiton.sol L73](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/libraries/Position.sol#L73)
[Position.sol L86](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/libraries/Position.sol#L86)

[PositionMath.sol L12](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/libraries/PositionMath.sol#L12)

[Balance.sol L12](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/libraries/Balance.sol#L12) 

[SafeCast.sol L8](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/libraries/SafeCast.sol#L8)
[SafeCast.sol L15](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/libraries/SafeCast.sol#L15)

The above codes and the rest internal functions in the codebase don’t follow Solidity standard naming convention,

internal and private functions : the mixedCase format starting with an underscore (\_mixedCase starting with an underscore)

## [N-02] REQUIRE()/REVERT() STATEMENTS SHOULD HAVE DESCRIPTIVE REASON STRINGS
[SafeCast.sol L9](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/libraries/SafeCast.sol#L9)
[SafeCast.sol L16](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/libraries/SafeCast.sol#L16)
[SwapHelper.sol L116](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/SwapHelper.sol#L116)

## [N-03] PUBLIC FUNCTIONS NOT CALLED BY THE CONTRACT SHOULD BE DECLARED EXTERNAL INSTEAD
[Payment.sol L25](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/Payment.sol#L25)
[Payment.sol L35](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/Payment.sol#L35)
