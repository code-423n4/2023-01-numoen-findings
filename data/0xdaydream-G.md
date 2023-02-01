Using compound assignment operators for state variables (like State += X or State -= X ) it’s more expensive than using operator assignment (like State = State + Xor State = State - X).

+ periphery/LiquidityManager.sol:178:    position.tokensOwed += FullMath.mulDiv(position.size, rewardPerPositionPaid - position.rewardPerPositionPaid, 1e18);
+ periphery/LiquidityManager.sol:180:    position.size += size;
+ periphery/LiquidityManager.sol:214:    position.tokensOwed += FullMath.mulDiv(position.size, rewardPerPositionPaid - position.rewardPerPositionPaid, 1e18);
+ periphery/LiquidityManager.sol:216:    position.size -= params.size;
+ periphery/LiquidityManager.sol:238:    position.tokensOwed += FullMath.mulDiv(position.size, rewardPerPositionPaid - position.rewardPerPositionPaid, 1e18);
+ periphery/LiquidityManager.sol:242:    position.tokensOwed -= amount;
+ core/Lendgine.sol:91:    totalLiquidityBorrowed += liquidity;
+ core/Lendgine.sol:114:    totalLiquidityBorrowed -= liquidity;
+ core/Lendgine.sol:176:    totalPositionSize -= size;
+ core/Lendgine.sol:257:    rewardPerPositionStored += FullMath.mulDiv(dilutionSpeculative, 1e18, totalPositionSize);

Gas saving executing: 13 per entry

Total gas saved: 13 * 10 = 130