G1. Since we know ``rewardPerPosition >= position.rewardPerPositionPaid``, we can add "unchecked" here to save gas:
```
function newTokensOwed(Position.Info memory position, uint256 rewardPerPosition) internal pure returns (uint256) {
    return FullMath.mulDiv(position.size, unchecked{rewardPerPosition - position.rewardPerPositionPaid}, 1 ether);
  }
```

G2. https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/libraries/Position.sol#L57
This line can be eliminated to save gas. Justification: when ``sizeDelta == 0``, the value of ``sizeNext`` is no use afterwards. There is no point to assign a value to it. 


