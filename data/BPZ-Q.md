## Floating and Outdated Pragma
Use debugged complier version . Also use more recent compiler version.

Affected Source Code Total instances : 15

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Factory.sol#L2

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L2

https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L2

https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LendgineRouter.sol#L2

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/ImmutableState.sol#L2

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol#L2

https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/Payment.sol#L2

https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/SwapHelper.sol#L2

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Pair.sol#L2

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/libraries/PositionMath.sol#L2

https://github.com/code-423n4/2023-01-numoen/blob/main/src/libraries/Balance.sol#L2

https://github.com/code-423n4/2023-01-numoen/blob/main/src/libraries/SafeCast.sol#L2

https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/libraries/LendgineAddress.sol#L2

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/libraries/Position.sol#L2

https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L1


## State variables should be decleared before events (Order of Layout)

Total instances : 1

https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LendgineRouter.sol#L43

For more read...
    1. [Soliditydocs](https://docs.soliditylang.org/en/v0.8.15/style-guide.html#order-of-layout)
    

## Internal and private functions should have an underscore prefix with mixedCase(Naming convention)

Affected Source Code
Total instances : 14

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol#L40

https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/Payment.sol#L52

https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/SwapHelper.sol#L69

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Pair.sol#L70

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Pair.sol#L93

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/libraries/PositionMath.sol#L12

https://github.com/code-423n4/2023-01-numoen/blob/main/src/libraries/Balance.sol#L12

https://github.com/code-423n4/2023-01-numoen/blob/main/src/libraries/SafeCast.sol#L8

https://github.com/code-423n4/2023-01-numoen/blob/main/src/libraries/SafeCast.sol#L15

https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/libraries/LendgineAddress.sol#L9

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/libraries/Position.sol#L38

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/libraries/Position.sol#L73

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/libraries/Position.sol#L86

https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L10

https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L17


 For more read...
    1. [Soliditydocs](https://docs.soliditylang.org/en/v0.8.15/style-guide.html#function-names)
    2. [Solidity Style](https://www.notion.so/Solidity-Style-44daebebfbd645b0b9cbad7075ba42fe)
    