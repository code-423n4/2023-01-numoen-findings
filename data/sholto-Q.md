**Impact**
The LiquidityManager.sol addLiquidity() function with a amount0 = params.amount0Min and amount1 = params.amount1Min. Because there is no allowance for slippage, if the zero slippage requirement is not met then the addLiquidity() function will revert. This could be caused either by frontrunning the addLiquidity call or in a situation where the liquidity pool exists but does not allow for zero slippage with the assets it is holding.

**PoC**
The zero slippage addLiquidity call is found in https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/LiquidityManager.sol#L135 

**Recommended Mitigation Steps**
One solution for this addLiquidity() issue is to add an input parameter to the function to handle a slippage allowance.