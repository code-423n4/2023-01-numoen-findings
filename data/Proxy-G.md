# x = x+y (or -) is more efficient than x += y (or -=)

Saves 15 gas per instance
### Tools used
remix
### Instances
1. [src/core/Lendgine.sol#L91](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L91) - totalLiquidityBorrowed 
2. [src/core/Lendgine.sol#L114](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L114) - totalLiquidityBorrowed
3. [src/core/Lendgine.sol#L176](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L176) - totalPositionSize
4. [src/core/Lendgine.sol#L257](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L257) - rewardPerPositionStored
5. [src/periphery/LiquidityManager.sol#L178](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L178) - position.tokensOwed
6. [src/periphery/LiquidityManager.sol#L180](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L180) - position.size
7. [src/periphery/LiquidityManager.sol#L214](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L214) - position.tokensOwed
8. [src/periphery/LiquidityManager.sol#L216](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L216) - position.size
9. [src/periphery/LiquidityManager.sol#L238](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L238) - position.tokensOwed
10. [src/periphery/LiquidityManager.sol#L242](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L242) - position.tokensOwed
