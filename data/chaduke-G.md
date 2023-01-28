G1. Since we know ``rewardPerPosition >= position.rewardPerPositionPaid``, we can add "unchecked" here to save gas:
```
function newTokensOwed(Position.Info memory position, uint256 rewardPerPosition) internal pure returns (uint256) {
    return FullMath.mulDiv(position.size, unchecked{rewardPerPosition - position.rewardPerPositionPaid}, 1 ether);
  }
```
