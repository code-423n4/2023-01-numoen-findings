**src/core/Lendgine.sol**
- L99 - When the balance of the msg.sender is checked since it had to have grown with the collateral, it is validated that: balanceAfter < balanceBefore + collateral otherwise reverts,
when the correct thing should be: balanceAfter != balanceBefore + collateral. I think this should be the case, since this would be seen as correct: balanceAfter > balanceBefore + collateral, something that is also invalid.


**src/periphery/LiquidityManager.sol**
- L52 - The PositionInvalidError error is created and is never used, therefore it should be eliminated.


**src/periphery/SwapHelper.sol**
- L116 - A validation is carried out with a require but no message is put for the return if it reverts, this is important since otherwise it is difficult for the user to know what happened.


**src/core/libraries/Position.sol**
- L73 - The inputs are used in another order within the function, this makes the inputs to enter less intuitive, therefore the most intuitive of the inputs would be: totalLiquiditySupplied, liquidity and totalPositionSize.


**src/libraries/Balance.sol**
- L12 - The library is called Balance and the function balance, this is quite confusing for any developer reading this code, therefore it should be named differently than the function or the library.
