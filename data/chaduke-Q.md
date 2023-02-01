QA1. ``LendgineRouter.mint()`` will fail if the user does not approve the contract sufficient allowance on ``token1``.

https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/LendgineRouter.sol#L142-L169

1) ``LendgineRouter.mint()`` relies on the callback function ``LendgineRouter.mintCallback`` to send ``token1`` from the user as collateral to ``Lendgine``:
```javascript
uint256 collateralIn = collateralTotal - amount1 - collateralSwap;
    if (collateralIn > decoded.collateralMax) revert AmountError();

    pay(decoded.token1, decoded.payer, msg.sender, collateralIn);
```
Therefore, it requires that the user will approve at least ``collateralTotal - amount1 - collateralSwap`` amount of ``token1`` allowance for the callback to succeed. 

2) If such allowance is not approved in advance, the callback and ``LendgineRouter.mint()`` will both revert.

Recommendation:  It is important to document allowance approval requirement as NatSpec of ``LendgineRouter.mint()``.

QA2. The address of ``Lendgine`` is calculated but not verified to have the right code deployed in ``LiquidityManager`` and ``LendgineRouter``:
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/LiquidityManager.sol#L104-L114

For example, the following code just calculate the address and uses it directly without further check:
```
function pairMintCallback(uint256, bytes calldata data) external {
    PairMintCallbackData memory decoded = abi.decode(data, (PairMintCallbackData));

    address lendgine = LendgineAddress.computeAddress(
      factory, decoded.token0, decoded.token1, decoded.token0Exp, decoded.token1Exp, decoded.upperBound
    );
    if (lendgine != msg.sender) revert ValidationError();

    if (decoded.amount0 > 0) pay(decoded.token0, decoded.payer, msg.sender, decoded.amount0);
    if (decoded.amount1 > 0) pay(decoded.token1, decoded.payer, msg.sender, decoded.amount1);
  }
```

Recommendation: Use `` EXTCODEHASH `` to check the contract exists and the  ``EXTCODEHASH`` is correct. The existence check can be performed like this:
https://soliditydeveloper.com/extcodehash
```javascript
function isContract(address account) internal view returns (bool) {
    bytes32 accountHash = 0xc5d2460186f7233c927e7db2dcc703c0e500b653ca82273b7bfad8045d85a470;

    bytes32 codeHash;    
    assembly { codeHash := extcodehash(account) }

    return (codeHash != accountHash && codeHash != 0x0);
}
We can further check the codeHash is indeed to codHash for the ``Lendgine``. 


``` 

QA3. Solidity version PRAGAMA should be locked to a particular version:
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/LendgineRouter.sol#L2

https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/LiquidityManager.sol#L2

https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Factory.sol#L2

https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/ImmutableState.sol#L2

https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/libraries/LendgineAddress.sol#L2

QA4. https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/LiquidityManager.sol#L147-L153

The first liquidity depositor can manipulate the price of token0/token1. This is possible because of the first reserve0 and reserve1 will be decided by params.amountMin and params.amount1Min:

```
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/LiquidityManager.sol#L147-L153

 if (totalLiquidity == 0) {
      amount0 = params.amount0Min;
      amount1 = params.amount1Min;
    } else {
      amount0 = FullMath.mulDivRoundingUp(params.liquidity, r0, totalLiquidity);
      amount1 = FullMath.mulDivRoundingUp(params.liquidity, r1, totalLiquidity);
    }

https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Pair.sol#L108-L110
   reserve0 = _reserve0 - SafeCast.toUint120(amount0); // SSTORE
    reserve1 = _reserve1 - SafeCast.toUint120(amount1); // SSTORE
    totalLiquidity = _totalLiquidity - liquidity; // SSTORE

```

For example, the first depositor can deposit a tiny amount of token0  and a relatively larger amount of token1. As a result, all future deposits have to deposit large amount of token1 and tiny amount of token0 (or even ZERO considering rounding error). This might not be a desirable behavior of the system.

To mitigate:
1) Constraint the minium deposit for token1:
2) The creator of a ``Lendgine`` will send a small amount of token0 and token1 and lock them in the contract forever. 




 



