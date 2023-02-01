# Report

---

## Gas Optimizations

---

|     | Issues                                                                                                                                      |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------ |
| 1   | [Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead](#usage-of-uintsints-smaller-than-32-bytes-256-bits-incurs-overhead) |
| 2   | [Use a more recent version of solidity](#use-a-more-recent-version-of-solidity)                                                            |
| 3   | [Pack the variables and parameters properly to save gas](#pack-the-variables-and-parameters-properly-to-save-gas)                          |
| 4   | [USING PRIVATE RATHER THAN PUBLIC FOR CONSTANTS, SAVES GAS](#using-private-rather-than-public-for-constants-saves-gas)                     |

---

Total: 1 instances over 1 issue with estimation that good amount of gas is saved on deployment.

---

1. ### Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

   When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

   - Present in :

   https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Factory.sol#L66

```diff
-    function createLendgine(
-    address token0,
-    address token1,
-    uint8 token0Exp,
-    uint8 token1Exp,
-   uint256 upperBound
-  )

+   function createLendgine(
+    address token0,
+   address token1,
+    uint256 token0Exp,
+    uint256 token1Exp,
+   uint256 upperBound
  )

  By considering the packing , you can also use 128

```

2. ### Use a more recent version of solidity

- Use a Solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value.

3. ### Pack the variables and parameters properly to save gas
   Packing or arranging the variables properly will save gas.

- https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L120

```diff
 struct AddLiquidityParams {
   address token0;
   address token1;
   uint256 token0Exp;
   uint256 token1Exp;
   uint256 upperBound;
   uint256 liquidity;
   uint256 amount0Min;
   uint256 amount1Min;
   uint256 sizeMin;
-  address recipient;
   uint256 deadline;
+  address recipient;
 }

```

Here when arranged, gas are saved on fuction call

4. ### USING PRIVATE RATHER THAN PUBLIC FOR CONSTANTS, SAVES GAS

If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it’s used, and not adding another entry to the method ID table

- https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol#L7
- https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol#L9
- https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol#L11
