## [N-1] Inconsistency in `SLOAD` and`SSTORE` comments in between code
In contract `Lendgine.sol` w.r.t when a state vairable is loaded on memory it is commented as SLOAD and when updated then SSTORE but it is not done consistently.
```diff
  function deposit(
.
.
    uint256 _totalPositionSize = totalPositionSize; // SLOAD
    uint256 totalLiquiditySupplied = totalLiquidity + totalLiquidityBorrowed;
.
.
    positions.update(to, SafeCast.toInt256(size), rewardPerPositionStored);
--    totalPositionSize = _totalPositionSize + size;
++    totalPositionSize = _totalPositionSize + size; // SSTORE
.
.
}
```
Also in withdraw function
```diff
function withdraw(
    .
    .
    uint256 _totalPositionSize = totalPositionSize; // SLOAD
    uint256 _totalLiquidity = totalLiquidity; // SLOAD
    uint256 totalLiquiditySupplied = _totalLiquidity + totalLiquidityBorrowed;

    Position.Info memory positionInfo = positions[msg.sender]; // SLOAD
    liquidity = Position.convertPositionToLiquidity(size, totalLiquiditySupplied, _totalPositionSize);

    if (liquidity == 0 || size == 0) revert InputError();

    if (size > positionInfo.size) revert InsufficientPositionError();
    if (liquidity > _totalLiquidity) revert CompleteUtilizationError();

    positions.update(msg.sender, -SafeCast.toInt256(size), rewardPerPositionStored);
--  totalPositionSize -= size;
++  totalPositionSize -= size; //SSTORE
    (amount0, amount1) = burn(to, liquidity);

    emit Withdraw(msg.sender, size, liquidity, to);
  }

```

