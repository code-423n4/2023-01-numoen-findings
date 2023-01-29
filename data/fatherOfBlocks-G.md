**src/core/Lendgine.sol**
- L163/164/165/167/176/248/250/252 - A variable is created in memory but it is only used once, therefore creating it generates unnecessary extra gas cost, it should be removed and used directly where the operation is needed.

- L201/256 - A subtraction is made between two uints, these two parameters were validated in line 252, which at least can return zero, therefore it could be unchecked, generating a lower gas expense. The same thing happens on line 198.


**src/periphery/LiquidityManager.sol**
- L107/110 - A variable is created in memory but it is only used once, therefore creating it generates an unnecessary extra gas cost, it should be eliminated and used directly where the operation is needed.

- L140/141 - The variables r0 and r1 are only used in the else of line 150, therefore they could be created directly inside the else.

- L242 - A subtraction is performed between two uints, but in line 241 the check is already performed indirectly, therefore unchecked can be used and not perform unnecessary extra validation.


**src/periphery/LendgineRouter.sol**
- L99/102/193/196/231/232 - A variable is created in memory but it is only used once, therefore creating it generates an unnecessary extra gas cost, it should be eliminated and used directly where the operation is needed .

- L198/199 - The variables r0 and r1 are only used in the else of line 208, therefore they could be created directly inside the else.


**src/core/JumpRate.sol**
- L19/20/21/34/35/37 - A variable is created in memory but it is only used once, therefore creating it generates an unnecessary extra gas cost, it should be eliminated and used directly where the operation is needed .


**src/periphery/SwapHelper.sol**
- L39/42/97/103 - A variable is created in memory but it is only used once, therefore creating it generates an unnecessary extra gas cost, it should be eliminated and used directly where the operation is needed.


**src/core/Pair.sol**
- L78/79/128/129 - A subtraction is performed between two uints, but in line 75/76/125/126 the check is already performed indirectly, therefore unchecked can be used and not perform unnecessary extra validation.


**src/periphery/UniswapV2/libraries/UniswapV2Library.sol**
- L11/60/61/78/79 - REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTES COST EXTRA GAS

- L63/64/65/80/81/82 - A variable is created in memory but it is only used once, therefore creating it generates an unnecessary extra gas cost, it should be removed and used directly where the operation is needed .
