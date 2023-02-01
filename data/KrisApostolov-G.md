[G-01] ```x = x - y;``` costs less gas than ```x -= y;``` same for gathering You can replace all ```-=``` and ```+=``` encounters to save gas.

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L91

```solidity
totalLiquidityBorrowed += liquidity;
``` 
#### **should be changed to** 
```solidity
totalLiquidityBorrowed = totalLiquidityBorrowed  + liquidity;
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L257

```solidity
rewardPerPositionStored += FullMath.mulDiv(dilutionSpeculative, 1e18, totalPositionSize);
``` 
#### **should be changed to** 
```solidity
rewardPerPositionStored = rewardPerPositionStored + FullMath.mulDiv(dilutionSpeculative, 1e18, totalPositionSize);
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L114

```solidity
totalLiquidityBorrowed -= liquidity;
``` 
#### **should be changed to** 
```solidity
totalLiquidityBorrowed = totalLiquidityBorrowed - liquidity;
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L176

```solidity
totalPositionSize -= size;
``` 
#### **should be changed to** 
```solidity
totalPositionSize = totalPositionSize - size;
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L178

```solidity
position.tokensOwed += FullMath.mulDiv(position.size, rewardPerPositionPaid - position.rewardPerPositionPaid, 1e18);
``` 
#### **should be changed to** 
```solidity
position.tokensOwed = position.tokensOwed + FullMath.mulDiv(position.size, rewardPerPositionPaid - position.rewardPerPositionPaid, 1e18);;
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L180

```solidity
position.size += size
``` 
#### **should be changed to** 
```solidity
position.size = position.size + size;
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L214
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L238
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#216
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L242

