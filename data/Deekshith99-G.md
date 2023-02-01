# [Gas Optimization]

### [G-1] X += Y can be written as X = X + Y 
There is 1 instance of it [Lendgine.sol#L91)](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Lendgine.sol#L91)
here it can be written as `totalLiquidityBorrowed += liquidity;` to   `totalLiquidityBorrowed = totalLiquidityBorrowed + liquidity;`

### [G-2] USE A MORE RECENT VERSION OF SOLIDITY
Use a Solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value.
Use a Solidity version of at least 0.8.12 to get string.concat() to be used instead of abi.encodePacked(<str>,<str>).
Use a solidity version of at least 0.8.13 to get the ability to use using for with a list of free functions.
& for better optimization also

### [G-3] SPLITTING `REQUIRE()` STATEMENTS THAT USE `&&` SAVES GAS
There are 2 instance of it
[UniswapV2Library.sol#L61](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L61)
[UniswapV2Library.sol#L79](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L79)
its good to use multiple require statments saves gas


