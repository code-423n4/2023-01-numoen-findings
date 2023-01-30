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