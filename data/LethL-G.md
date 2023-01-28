Title: += and -= Costs More Gas
Description:
Using x += y instead of x = x + y will cost 22 more gas.
All instances entailed:
File: Lendgine.sol
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L91
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L114
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L176
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L257

File: LiquidityManager.sol
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L178
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L180
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L214
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L216
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L238
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L242