## Parameterized custom errors
Custom errors can be parameterized to better reflect their respective error messages if need be.

For instance, the custom error instance below may be refactored as follows:

[File: Factory.sol#L26](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Factory.sol#L26)

```diff
-  error SameTokenError();
+  error SameTokenError(address token0, address token1);
```
[File: Factory.sol#L74](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Factory.sol#L74)

```diff
-    if (token0 == token1) revert SameTokenError();
+    if (token0 == token1) revert SameTokenError(token0, token1);
```
## Exact number of days in a year
Background: The true length of a year on Earth is 365.2422 days, or about 365.25 days. We keep our calendar in sync with the seasons by having most years 365 days long but making just under 1/4 of all years 366-day "leap" years.

Here is an affected code line that may be refactored as follows:

[File: Lendgine.sol#L252](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L252) 

```diff
-    uint256 dilutionLPRequested = (FullMath.mulDiv(borrowRate, _totalLiquidityBorrowed, 1e18) * timeElapsed) / 365 days;
+    uint256 dilutionLPRequested = (FullMath.mulDiv(borrowRate, _totalLiquidityBorrowed, 1e18) * timeElapsed) / 365.2422 days;
```
## Non-compliant contract layout with Solidity's Style Guide
According to Solidity's Style Guide below:

https://docs.soliditylang.org/en/v0.8.17/style-guide.html

In order to help readers identify which functions they can call, and find the constructor and fallback definitions more easily, functions should be grouped according to their visibility and ordered in the following manner:

constructor, receive function (if exists), fallback function (if exists), external, public, internal, private

And, within a grouping, place the `view` and `pure` functions last.

Additionally, inside each contract, library or interface, use the following order:

type declarations, state variables, events, modifiers, functions

Consider adhering to the above guidelines for all contract instances entailed.

## Use a more recent version of solidity
The protocol adopts version 0.8.4 on writing contracts. For better security, it is best practice to use the latest Solidity version, 0.8.17.

Security fix list in the versions can be found in the link below:

https://github.com/ethereum/solidity/blob/develop/Changelog.md