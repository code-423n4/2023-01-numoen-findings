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