https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Factory.sol#L50 

Low Level Vulnerability 

The parameters token0Exp and token1Exp are defined as uint8 and have a maximum value of 255. If a large value is passed, the revert in the "if (token0Exp > 18 || token0Exp < 6 || token1Exp > 18 || token1Exp < 6) revert ScaleError();" statement will not be triggered and this can lead to unexpected behavior.