## Gas Optimization

### 1. refactor `Position.update` logic

The current implementation in `update` introduces more branches than necessary. Also some variables like `tokensOwed` and `sizeNext` are not always used and can only be declared in a certain scope (block).

Updated (optimized) version:

```solidity
if (_positionInfo.size > 0) {
  uint256 tokensOwed = newTokensOwed(_positionInfo, rewardPerPosition);
  positionInfo.tokensOwed = _positionInfo.tokensOwed + tokensOwed;
}

if (sizeDelta == 0) {
  if (_positionInfo.size == 0) revert NoPositionError();
  // don't need to assign sizeNext
} else {
  positionInfo.size = PositionMath.addDelta(_positionInfo.size, sizeDelta);
}

positionInfo.rewardPerPositionPaid = rewardPerPosition;
```

gas saving: 

- `testAddPosition() (gas: 406462)` ⇒  `406080`
- `testAddPositionEmpty() (gas: 330006)` ⇒ `329767`

### 2. Use cached value in `deposit`

In `Lendgine.deposit()`: there’s a cached `_totalPositionSize` variable, so in this check 

```tsx
if (totalLiquiditySupplied == 0 && totalPositionSize > 0) revert CompleteUtilizationError(); 
```

it would be cheaper to use the cached variable.

gas saving

- `deposit`:  100 from additional SLOAD. (from forge gas report: avg `128547` ⇒ `128473` )

### 3. Unnecessary check in `Lendgine.withdraw`

In the withdraw function, these 2 lines are not necessary:

```tsx
Position.Info memory positionInfo = positions[msg.sender]; // SLOAD
// ...
if (size > positionInfo.size) revert InsufficientPositionError();
```

Because in `position.update` it will revert anyway if size > existing size.

This additional copy to memory is actually 3 SLOAD from storage, removing these 3 lines can reduce the gas cost dramatically

Gas saving

- `withdraw`: avg: 72877 ⇒ 70405

### 4. No need to calculate `lendgine` address on the fly in `LiquidityMananger.removeLiquidity`

In `LiquidityManager.removeLiquidity`, it uses the library to calculate lendgine address like this

```tsx
address lendgine = LendgineAddress.computeAddress(
   factory, params.token0, params.token1, params.token0Exp, params.token1Exp, params.upperBound
);
```

But this is not actually necessary because all the “state” variable is stored under a mapping `positions[msg.sender][lendgine]`, so even if the user put in a fake `lendgine` address, it won’t affect the actual accounting of real pools, and the malicious won’t be able to steal liquidity from any other pool.

This current code structure is only needed for `addLiquidity` where you have a callback (pairMintCallback) invoked, and you need those parameters to check the msg.sender is legit. In `removeLiquidity`, there’s no such need, and it would be easier to just pass in lendgine.

**Optimization**:

Let the function pass in `lendgine` directly instead of calculating with 5 parameters (params.token0, params.token1, params.token0Exp, params.token1Exp, params.upperBound)