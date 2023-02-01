# <X> += <Y> COSTS MORE GAS THAN <X> = <X> + <Y> FOR STATE VARIABLES

  ` totalLiquidityBorrowed += liquidity; `

 https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Lendgine.sol#L91

   ` rewardPerPositionStored += FullMath.mulDiv(dilutionSpeculative, 1e18, totalPositionSize);`

https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Lendgine.sol#L257

   ` position.size += size; `
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/LiquidityManager.sol#L180

#  REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTES COST EXTRA GAS
  `  require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY"); `

https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L61

  `  require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY"); `

https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L79

# SETTING THE CONSTRUCTOR TO PAYABLE
 Saves ~13 gas per instance

 ` constructor(address _factory, address _weth) Payment(_weth) { `

https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/LiquidityManager.sol#L75

` constructor(
    address _factory,
    address _uniswapV2Factory,
    address _uniswapV3Factory,
    address _weth
  )  `
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/LendgineRouter.sol#L49

  ` constructor(address _uniswapV2Factory, address _uniswapV3Factory) { `

https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/SwapHelper.sol#L29














