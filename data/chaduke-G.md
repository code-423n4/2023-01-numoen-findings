G1. Using unchecked for these blocks can save gas. Due to a previous check or the property of the contracts, underflow/overflow is impossible. 
1] https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Lendgine.sol#L244

2] https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Lendgine.sol#L256

3] https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/libraries/Position.sol#L70

4] https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/LiquidityManager.sol#L178

5] https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Pair.sol#L122-L123

G2. Optimization of the update() function for gas saving.
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/libraries/Position.sol#L38-L65

https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/libraries/Position.sol#L57
This (L57) line can be eliminated to save gas. Justification: when ``sizeDelta == 0``, the value of ``sizeNext`` is no use afterwards. There is no point to assign a value to it. In addition, multiple if statements can be consolidated to save gas by not doing too many tests. We reduced it from 5-if to 3-if. 

```
function update(
    mapping(address => Position.Info) storage self,
    address owner,
    int256 sizeDelta,
    uint256 rewardPerPosition
  )
    internal
{
    Position.Info storage positionInfo = self[owner];
    Position.Info memory _positionInfo = positionInfo;
    if (_positionInfo.size == 0) revert NoPositionError();  // @audit: short-circuit to save gas

    
    if (_positionInfo.size > 0) 
         positionInfo.tokensOwed = _positionInfo.tokensOwed + newTokensOwed(_positionInfo, rewardPerPosition);
    positionInfo.rewardPerPositionPaid = rewardPerPosition;
    
    if (sizeDelta != 0)  
         positionInfo.size =  PositionMath.addDelta(_positionInfo.size, sizeDelta)
}
```

G3. A short-circuit rule can be applied to the ``Drips._squeezAmt`` function: when the binary search for the ``idx`` of the first occurence of `userId` fails, we can return 0.
https://github.com/code-423n4/2023-01-drips/blob/9fd776b50f4be23ca038b1d0426e63a69c7a511d/src/Drips.sol#L470-L498
```diff
 function _squeezedAmt(
        uint256 userId,
        DripsHistory memory dripsHistory,
        uint32 squeezeStartCap,
        uint32 squeezeEndCap
    ) private view returns (uint128 squeezedAmt) {
        DripsReceiver[] memory receivers = dripsHistory.receivers;
        // Binary search for the `idx` of the first occurrence of `userId`
        uint256 idx = 0;
        for (uint256 idxCap = receivers.length; idx < idxCap;) {
            uint256 idxMid = (idx + idxCap) / 2;
            if (receivers[idxMid].userId < userId) {
                idx = idxMid + 1;
            } else {
                idxCap = idxMid;
            }
        }
+     if(receivers[idx] != userId) return 0 // @audit: short circuit

```


