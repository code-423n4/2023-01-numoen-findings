# There are tokens with > 18 decimals
In [createLendgine](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Factory.sol#L77), it looks like __token0Exp__ and __token1Exp__ are related to decimals. If so, there are tokens with more than 18 decimals, which might affect the protocol.

# Check to is not empty
in "Lendgine.sol", functions like __burn__ and __collect__, when calling safeTransfer, it's better to check __"to"__ is not empty
same as __burn__ and __swap__ in core/Pair.sol, also in periphery/Payment.sol and periphery/LendgineRouter.sol