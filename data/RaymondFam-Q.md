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