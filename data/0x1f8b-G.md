- [Gas](#gas)
    - [**1. Cache keccak256 result in a constant**](#1-cache-keccak256-result-in-a-constant)
    - [**2. Optimize LendgineRouter storage**](#2-optimize-lendginerouter-storage)

# Gas

## **1. Cache `keccak256` result in a constant**

It's cheaper to store the `keccak256` result instead of compute it every time.

**Affected source code:**

- [Balance.sol:14](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/libraries/Balance.sol#L14)

## **2. Optimize `LendgineRouter` storage**

It's possible to optimize the structs of the `LendgineRouter` contract moving the `swapType` close to an `address`
The following structures could be optimized moving the position of certain values in order to save some storage slots:

```diff
  struct MintCallbackData {
    address token0;
    address token1;
    uint256 token0Exp;
    uint256 token1Exp;
    uint256 upperBound;
    uint256 collateralMax;
-   SwapType swapType;
    bytes swapExtraData;
    address payer;
+   SwapType swapType;
  }
```

```diff
  struct MintParams {
    address token0;
    address token1;
    uint256 token0Exp;
    uint256 token1Exp;
    uint256 upperBound;
    uint256 amountIn;
    uint256 amountBorrow;
    uint256 sharesMin;
-   SwapType swapType;
    bytes swapExtraData;
    address recipient;
+   SwapType swapType;
    uint256 deadline;
  }
```

```diff
  struct PairMintCallbackData {
    address token0;
    address token1;
    uint256 token0Exp;
    uint256 token1Exp;
    uint256 upperBound;
    uint256 collateralMin;
    uint256 amount0Min;
    uint256 amount1Min;
-   SwapType swapType;
    bytes swapExtraData;
    address recipient;
+   SwapType swapType;
  }
```

```diff
  struct BurnParams {
    address token0;
    address token1;
    uint256 token0Exp;
    uint256 token1Exp;
    uint256 upperBound;
    uint256 shares;
    uint256 collateralMin;
    uint256 amount0Min;
    uint256 amount1Min;
-   SwapType swapType;
    bytes swapExtraData;
    address recipient;
+   SwapType swapType;
    uint256 deadline;
  }
```

**Reference:**

- https://ethdebug.github.io/solidity-data-representation

> Enums are represented by integers; the possibility listed first by 0, the next by 1, and so forth. An enum type just acts like uintN, where N is the smallest legal value large enough to accomodate all the possibilities.

- https://docs.soliditylang.org/en/v0.8.17/internals/layout_in_storage.html#storage-inplace-encoding

**Affected source code:**

- [LendgineRouter.sol:74-84](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/LendgineRouter.sol#L74-L84)
- [LendgineRouter.sol:126-139](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/LendgineRouter.sol#L126-L139)
- [LendgineRouter.sol:175-187](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/LendgineRouter.sol#L175-L187)
- [LendgineRouter.sol:240-254](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/LendgineRouter.sol#L240-L254)

**Gas difference:**

The unit test fail, so it's not possible to check the difference

```
Running 1 test for test/LendgineRouterTest.t.sol:LendgineRouterTest
[FAIL. Reason: Setup failed: Contract 0xc35DADB65012eC5796536bD9864eD8773aBc74C4 does not exist and is not marked as persistent, see `vm.makePersistent()`] setUp() (gas: 0)
Test result: FAILED. 0 passed; 1 failed; finished in 3.55s
```
