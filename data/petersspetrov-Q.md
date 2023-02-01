[L-01]Require statement is missing string parameter and it is not callable.
The correct and clear error description explains to the user why the function reverts, 
but the error descriptions below in the project are not self-explanatory. These error descriptions
 are very important in the debug features of DApps like Tenderly. Error definitions should be added to the require block, not exceeding 32 bytes.
 
 https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/SwapHelper.sol#L116
 
 
 ## Proof of Concept
 require(amountOutReceived == params.amount);
 
 ## Recommended Mitigation Steps:
 require(amountOutReceived == params.amount, "Error message");
 
[L-02]"if" checking must be in "else" statement
 
 https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/LiquidityManager.sol#L147-L155
 
 ## Proof of Concept
 if (totalLiquidity == 0) {
      amount0 = params.amount0Min;
      amount1 = params.amount1Min;
    } else {
      amount0 = FullMath.mulDivRoundingUp(params.liquidity, r0, totalLiquidity);
      amount1 = FullMath.mulDivRoundingUp(params.liquidity, r1, totalLiquidity);
    }

    if (amount0 < params.amount0Min || amount1 < params.amount1Min) revert AmountError();
	
## Recommended Mitigation Steps:
	if (totalLiquidity == 0) {
      amount0 = params.amount0Min;
      amount1 = params.amount1Min;
    } else {
      amount0 = FullMath.mulDivRoundingUp(params.liquidity, r0, totalLiquidity);
      amount1 = FullMath.mulDivRoundingUp(params.liquidity, r1, totalLiquidity);
	  if (amount0 < params.amount0Min || amount1 < params.amount1Min) revert AmountError();
    }
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/LendgineRouter.sol#L205-L213
  
## Proof of Concept
 if (totalLiquidity == 0) {
      amount0 = params.amount0Min;
      amount1 = params.amount1Min;
    } else {
      amount0 = FullMath.mulDivRoundingUp(params.liquidity, r0, totalLiquidity);
      amount1 = FullMath.mulDivRoundingUp(params.liquidity, r1, totalLiquidity);
    }

    if (amount0 < params.amount0Min || amount1 < params.amount1Min) revert AmountError();
	
## Recommended Mitigation Steps:
	if (totalLiquidity == 0) {
      amount0 = params.amount0Min;
      amount1 = params.amount1Min;
    } else {
      amount0 = FullMath.mulDivRoundingUp(params.liquidity, r0, totalLiquidity);
      amount1 = FullMath.mulDivRoundingUp(params.liquidity, r1, totalLiquidity);
	  if (amount0 < params.amount0Min || amount1 < params.amount1Min) revert AmountError();
    }  