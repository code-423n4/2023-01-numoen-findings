### Table of Contents
- [FINDINGS](#findings)
- [Pack structs by putting variables that can fit together next to each other](#pack-structs-by-putting-variables-that-can-fit-together-next-to-each-other)
  - [From 9 SLOTS to 8 SLOTS(2k gas)](#from-9-slots-to-8-slots2k-gas)
  - [From 12 SLOTS to 10 SLOTS(4k gas)](#from-12-slots-to-10-slots4k-gas)
  - [From 11 SLOTS to 10 SLOTS(2k gas)](#from-11-slots-to-10-slots2k-gas)
  - [From 13 SLOTS to 11 SLOTS(4k gas saved)](#from-13-slots-to-11-slots4k-gas-saved)
  - [From 11 SLOTS to 10 SLOTS(2k gas saved)](#from-11-slots-to-10-slots2k-gas-saved)
  - [From 10 SLOTS to 9 SLOTS(2k gas saved)](#from-10-slots-to-9-slots2k-gas-saved)
- [IF's statements/ require()statements that check input arguments should be at the top of the function](#ifs-statements-requirestatements-that-check-input-arguments-should-be-at-the-top-of-the-function)
  - [Lendgine.sol.mint(): Split Some checks here](#lendginesolmint-split-some-checks-here)
  - [Lendgine.sol.burn(): Split some checks](#lendginesolburn-split-some-checks)
  - [Lendgine.sol.deposit(): Could avoid an entire SLOAD](#lendginesoldeposit-could-avoid-an-entire-sload)
  - [Lendgine.sol.withdraw(): Save ~4 SLOADs here](#lendginesolwithdraw-save-4-sloads-here)
  - [Factory.sol.createLendgine(): Check for local values first before running the storage ones](#factorysolcreatelendgine-check-for-local-values-first-before-running-the-storage-ones)
- [Using storage instead of memory for structs/arrays saves gas](#using-storage-instead-of-memory-for-structsarrays-saves-gas)
- [Avoid contract existence checks by using solidity version 0.8.10 or later(1700 gas saved)](#avoid-contract-existence-checks-by-using-solidity-version-0810-or-later1700-gas-saved)
- [Use the cached value here instead of reading from storage](#use-the-cached-value-here-instead-of-reading-from-storage)
  - [Lendgine.sol.deposit(): totalPositionSize has already been cached](#lendginesoldeposit-totalpositionsize-has-already-been-cached)
  - [Lendgine.sol.withdraw(): totalPositionSize already cached(~100 gas)](#lendginesolwithdraw-totalpositionsize-already-cached100-gas)
- [x += y costs more gas than x = x + y for state variables](#x--y-costs-more-gas-than-x--x--y-for-state-variables)
- [Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead](#usage-of-uintsints-smaller-than-32-bytes-256-bits-incurs-overhead)
- [Splitting require() statements that use \&\& saves gas - (saves 8 gas per \&\&)](#splitting-require-statements-that-use--saves-gas---saves-8-gas-per-)
- [`keccak256()` should only need to be called on a specific string literal once](#keccak256-should-only-need-to-be-called-on-a-specific-string-literal-once)
- [Use a more recent version of solidity](#use-a-more-recent-version-of-solidity)
- [Captured in C4audit: Leaving it here as the explanations might be useful to the sponsor.](#captured-in-c4audit-leaving-it-here-as-the-explanations-might-be-useful-to-the-sponsor)
  - [Using unchecked blocks to save gas](#using-unchecked-blocks-to-save-gas)
  - [poc](#poc)

## FINDINGS
NB: Some functions have been truncated where necessary to just show affected parts of the code
Through out the report some places might be denoted with audit tags to show the actual place affected.


## Pack structs by putting variables that can fit together next to each other

As the solidity EVM works with 32 bytes, variables less than 32 bytes should be packed inside a struct so that they can be stored in the same slot, this saves gas when writing to storage ~20000 gas

**Two variables are involved in packing, one is an enum type which is treated just like a uint8 and the other is deadline. Deadline has been declared as uint256 but I've proposed to reduce it to uint64 as it should be safe for the next 532 years.**

**I've given the overall gas saved factoring in the reduced size of deadline variable and  I've also highlighted the amount we should save if we are not willing to reduce the size**

**I've also added some audit tags that explain how the packing would be achieved**

https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/LendgineRouter.sol#L74-L84
### From 9 SLOTS to 8 SLOTS(2k gas)
**swapType being an enum is treated just like a uint8**
`Total slots: 1, Gas per slot: 2K, Total Gas: 2K * 1 = 2K gas`

```solidity
File: /src/periphery/LendgineRouter.sol

//@audit: swapType is an enum and can be packed with address token0
74:  struct MintCallbackData {
75:    address token0;
76:    address token1;
77:    uint256 token0Exp;
78:    uint256 token1Exp;
79:    uint256 upperBound;
80:    uint256 collateralMax;
81:    SwapType swapType;
82:    bytes swapExtraData;
83:    address payer;
84:  }
```

```diff
diff --git a/src/periphery/LendgineRouter.sol b/src/periphery/LendgineRouter.sol
index ff9295d..6317bdd 100644
--- a/src/periphery/LendgineRouter.sol
+++ b/src/periphery/LendgineRouter.sol
@@ -72,13 +72,13 @@ contract LendgineRouter is Multicall, Payment, SelfPermit, SwapHelper, IMintCall
     //////////////////////////////////////////////////////////////*/

   struct MintCallbackData {
+    SwapType swapType;
     address token0;
     address token1;
     uint256 token0Exp;
     uint256 token1Exp;
     uint256 upperBound;
     uint256 collateralMax;
-    SwapType swapType;
     bytes swapExtraData;
     address payer;
   }
```

https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/LendgineRouter.sol#L126-L139
### From 12 SLOTS to 10 SLOTS(4k gas)
**swapType being an enum is treated just like a uint8 while uint64 Should be safe for the next 532 years** 
`Total slots: 2, Gas per slot: 2K, Total Gas: 2K * 2 = 4K gas`
**If not willing to go with uint64 we could still save 2K gas from  swapType**

```solidity
File: /src/periphery/LendgineRouter.sol

//@audit: Save 2 SLOT by packing recipient,deadline and swapType together
  struct MintParams {
    address token0;
    address token1;
    uint256 token0Exp;
    uint256 token1Exp;
    uint256 upperBound;
    uint256 amountIn;
    uint256 amountBorrow;
    uint256 sharesMin;
    SwapType swapType;
    bytes swapExtraData;
    address recipient;
    uint256 deadline;
  }
```

```diff
diff --git a/src/periphery/LendgineRouter.sol b/src/periphery/LendgineRouter.sol
index ff9295d..07881c6 100644
--- a/src/periphery/LendgineRouter.sol
+++ b/src/periphery/LendgineRouter.sol
@@ -124,6 +124,7 @@ contract LendgineRouter is Multicall, Payment, SelfPermit, SwapHelper, IMintCall
   }

   struct MintParams {
+    SwapType swapType;
     address token0;
     address token1;
     uint256 token0Exp;
@@ -132,10 +133,9 @@ contract LendgineRouter is Multicall, Payment, SelfPermit, SwapHelper, IMintCall
     uint256 amountIn;
     uint256 amountBorrow;
     uint256 sharesMin;
-    SwapType swapType;
     bytes swapExtraData;
     address recipient;
-    uint256 deadline;
+    uint64 deadline;
   }
```

https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/LendgineRouter.sol#L175-L187
### From 11 SLOTS to 10 SLOTS(2k gas)
**swapType being an enum is treated just like a uint8**
`Total slots: 1, Gas per slot: 2K, Total Gas: 2K * 1 = 2K gas`

```solidity
File: /src/periphery/LendgineRouter.sol

//@audit: swapType is an enum and can be packed with address token0
175:  struct PairMintCallbackData {
176:    address token0;
177:    address token1;
178:    uint256 token0Exp;
179:    uint256 token1Exp;
180:    uint256 upperBound;
181:    uint256 collateralMin;
182:    uint256 amount0Min;
183:    uint256 amount1Min;
184:    SwapType swapType;
185:    bytes swapExtraData;
186:    address recipient;
187:  }
```

```diff
diff --git a/src/periphery/LendgineRouter.sol b/src/periphery/LendgineRouter.sol
index ff9295d..3a4e04f 100644
--- a/src/periphery/LendgineRouter.sol
+++ b/src/periphery/LendgineRouter.sol
@@ -173,6 +173,7 @@ contract LendgineRouter is Multicall, Payment, SelfPermit, SwapHelper, IMintCall
     //////////////////////////////////////////////////////////////*/

   struct PairMintCallbackData {
+    SwapType swapType;
     address token0;
     address token1;
     uint256 token0Exp;
@@ -181,7 +182,6 @@ contract LendgineRouter is Multicall, Payment, SelfPermit, SwapHelper, IMintCall
     uint256 collateralMin;
     uint256 amount0Min;
     uint256 amount1Min;
-    SwapType swapType;
     bytes swapExtraData;
     address recipient;
   }
```


https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/LendgineRouter.sol#L240-L254
### From 13 SLOTS to 11 SLOTS(4k gas saved)
**uint64 Should be safe for the next 532 years**
`Total slots: 2, Gas per slot: 2K, Total Gas: 2K * 2 = 4K gas`
**If not willing to go with uint64 we could still save 2K gas from  swapType**

```solidity
File: /src/periphery/LendgineRouter.sol

//@audit: Save 2 SLOT by packing recipient,deadline and swapType together
240:  struct BurnParams {
241:    address token0;
242:    address token1;
243:    uint256 token0Exp;
244:    uint256 token1Exp;
245:    uint256 upperBound;
246:    uint256 shares;
247:    uint256 collateralMin;
248:    uint256 amount0Min;
249:    uint256 amount1Min;
250:    SwapType swapType;
251:    bytes swapExtraData;
252:    address recipient;
253:    uint256 deadline;
254:  }
```

```diff
diff --git a/src/periphery/LendgineRouter.sol b/src/periphery/LendgineRouter.sol
index ff9295d..40b69ba 100644
--- a/src/periphery/LendgineRouter.sol
+++ b/src/periphery/LendgineRouter.sol
@@ -132,10 +132,10 @@ contract LendgineRouter is Multicall, Payment, SelfPermit, SwapHelper, IMintCall
     uint256 amountIn;
     uint256 amountBorrow;
     uint256 sharesMin;
-    SwapType swapType;
     bytes swapExtraData;
     address recipient;
-    uint256 deadline;
+    uint64 deadline;
+    SwapType swapType;
   }
```


https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/LiquidityManager.sol#L120-L132
###  From 11 SLOTS to 10 SLOTS(2k gas saved)
**uint64 Should be safe for the next 532 years**
`Total slots: 1, Gas per slot: 2K, Total Gas: 2K * 1 = 2K gas`


```solidity
File: /src/periphery/LiquidityManager.sol

//@audit: Save 1 SLOT by packing recipient and deadline together
120:  struct AddLiquidityParams {
121:    address token0;
122:    address token1;
123:    uint256 token0Exp;
124:    uint256 token1Exp;
125:    uint256 upperBound;
126:    uint256 liquidity;
127:    uint256 amount0Min;
128:    uint256 amount1Min;
129:    uint256 sizeMin;
130:    address recipient;
131:    uint256 deadline;
132:  }
```

```diff
diff --git a/src/periphery/LiquidityManager.sol b/src/periphery/LiquidityManager.sol
index a1b6c9b..809b9e2 100644
--- a/src/periphery/LiquidityManager.sol
+++ b/src/periphery/LiquidityManager.sol
@@ -128,7 +128,7 @@ contract LiquidityManager is Multicall, Payment, SelfPermit, IPairMintCallback {
     uint256 amount1Min;
     uint256 sizeMin;
     address recipient;
-    uint256 deadline;
+    uint64 deadline;
   }
```


https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/LiquidityManager.sol#L187-L198
### From 10 SLOTS to 9 SLOTS(2k gas saved) 
**uint64 Should be safe for the next 532 years**
`Total slots: 1, Gas per slot: 2K, Total Gas: 2K * 1 = 2K gas`

```solidity
File: /src/periphery/LiquidityManager.sol

//@audit: Save 1 SLOT by packing recipient and deadline together
187:  struct RemoveLiquidityParams {
188:    address token0;
189:    address token1;
190:    uint256 token0Exp;
191:    uint256 token1Exp;
192:    uint256 upperBound;
193:    uint256 size;
194:    uint256 amount0Min;
195:    uint256 amount1Min;
196:    address recipient;
197:    uint256 deadline;
198:  }
```

```diff
diff --git a/src/periphery/LiquidityManager.sol b/src/periphery/LiquidityManager.sol
index a1b6c9b..da2fef3 100644
--- a/src/periphery/LiquidityManager.sol
+++ b/src/periphery/LiquidityManager.sol
@@ -194,7 +194,7 @@ contract LiquidityManager is Multicall, Payment, SelfPermit, IPairMintCallback {
     uint256 amount0Min;
     uint256 amount1Min;
     address recipient;
-    uint256 deadline;
+    uint64 deadline;
   }
```


## IF's statements/ require()statements that check input arguments should be at the top of the function

Checks that involve constants/cheap to run should come before checks that involve state variables(expensive checks), function calls, and calculations. By doing these checks first, the function is able to revert before wasting a Gcoldsload (2100 gas) in a function that may ultimately revert in the unhappy case.

**In the following I've refactored most of the if's statements to have the cheap checks come first which would save alot of gas in case the cheap checks revert**

**A lot of explanations has gone into this to explain the why and the how this would work, kindly take note**

https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Lendgine.sol#L71-L102
### Lendgine.sol.mint(): Split Some checks here
```solidity
File: /src/core/Lendgine.sol
71:  function mint(
72:    address to,
73:    uint256 collateral,
74:    bytes calldata data
75:  )

81:    _accrueInterest();

83:    uint256 liquidity = convertCollateralToLiquidity(collateral);
84:    shares = convertLiquidityToShare(liquidity);

86:    if (collateral == 0 || liquidity == 0 || shares == 0) revert InputError();
87:    if (liquidity > totalLiquidity) revert CompleteUtilizationError();
88:    // next check is for the case when liquidity is borrowed but then was completely accrued
89:    if (totalSupply > 0 && totalLiquidityBorrowed == 0) revert CompleteUtilizationError();
```

Note the check, `if (collateral == 0 || liquidity == 0 || shares == 0) revert InputError();` , we have a function argument `collateral` that is checked against `0`. As this line is ** with other variables ie `liquidity and shares` we are forced to spend some gas in calculating the values of this non function arguments to run the check. As we could just revert if `collateral is 0` we could split this check to avoid the gas spent in calculating the values of `liquidity and shares`. 

```diff
diff --git a/src/core/Lendgine.sol b/src/core/Lendgine.sol
index e8086f4..cc3a7e7 100644
--- a/src/core/Lendgine.sol
+++ b/src/core/Lendgine.sol
@@ -80,10 +80,11 @@ contract Lendgine is ERC20, JumpRate, Pair, ILendgine {
   {
     _accrueInterest();

+    if (collateral == 0 ) revert InputError();
     uint256 liquidity = convertCollateralToLiquidity(collateral);
+    if ( liquidity == 0 ) revert InputError();
     shares = convertLiquidityToShare(liquidity);
-    if (collateral == 0 || liquidity == 0 || shares == 0) revert InputError();
+    if ( shares == 0) revert InputError();
     if (liquidity > totalLiquidity) revert CompleteUtilizationError();
     // next check is for the case when liquidity is borrowed but then was completely accrued
     if (totalSupply > 0 && totalLiquidityBorrowed == 0) revert CompleteUtilizationError();
```

https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Lendgine.sol#L105-L120
### Lendgine.sol.burn(): Split some checks 
```solidity
File: /src/core/Lendgine.sol
105:  function burn(address to, bytes calldata data) external override nonReentrant returns (uint256 collateral) {
106:    _accrueInterest();

108:    uint256 shares = balanceOf[address(this)];
109:    uint256 liquidity = convertShareToLiquidity(shares);
110:    collateral = convertLiquidityToCollateral(liquidity);

112:    if (collateral == 0 || liquidity == 0 || shares == 0) revert InputError();
```
As shares is calculated independent from `collateral` and `liquidity` we could check for it first and revert if the condition is not met rather than waste gas evaluating the value of `liquidity and collateral` which are dependent on the value of `shares`. We could split all the conditions from each other as shown below.

```diff
diff --git a/src/core/Lendgine.sol b/src/core/Lendgine.sol
index e8086f4..24cb85e 100644
--- a/src/core/Lendgine.sol
+++ b/src/core/Lendgine.sol
@@ -106,10 +106,13 @@ contract Lendgine is ERC20, JumpRate, Pair, ILendgine {
     _accrueInterest();

     uint256 shares = balanceOf[address(this)];
+    if (shares == 0) revert InputError();
+
     uint256 liquidity = convertShareToLiquidity(shares);
-    collateral = convertLiquidityToCollateral(liquidity);
+    if (liquidity == 0) revert InputError();

-    if (collateral == 0 || liquidity == 0 || shares == 0) revert InputError();
+    collateral = convertLiquidityToCollateral(liquidity);
+    if (collateral == 0 ) revert InputError();
```

https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Lendgine.sol#L123-L149
### Lendgine.sol.deposit(): Could avoid an entire SLOAD
```solidity
File: /src/core/Lendgine.sol
123:  function deposit(
124:    address to,
125:    uint256 liquidity,
126:    bytes calldata data
127:  )
128:    external
129:    override
130:    nonReentrant
131:    returns (uint256 size)
132:  {
133:    _accrueInterest();

135:    uint256 _totalPositionSize = totalPositionSize; // SLOAD
136:    uint256 totalLiquiditySupplied = totalLiquidity + totalLiquidityBorrowed;

138:    size = Position.convertLiquidityToPosition(liquidity, totalLiquiditySupplied, _totalPositionSize);

140:    if (liquidity == 0 || size == 0) revert InputError();
141:    // next check is for the case when liquidity is borrowed but then was completely accrued
142:    if (totalLiquiditySupplied == 0 && totalPositionSize > 0) revert CompleteUtilizationError();
```
Here we can avoid an entire SLOAD if `liquidity == 0`. As a revert would occur if the function argument `liquidity` is `0`, we should check for it's value before wasting gas calculating the return value `size` which involves an SLOAD . Revert early and cheaply

```diff
diff --git a/src/core/Lendgine.sol b/src/core/Lendgine.sol
index e8086f4..15c278f 100644
--- a/src/core/Lendgine.sol
+++ b/src/core/Lendgine.sol
@@ -132,12 +132,13 @@ contract Lendgine is ERC20, JumpRate, Pair, ILendgine {
   {
     _accrueInterest();

+    if (liquidity == 0) revert InputError();
     uint256 _totalPositionSize = totalPositionSize; // SLOAD
     uint256 totalLiquiditySupplied = totalLiquidity + totalLiquidityBorrowed;

     size = Position.convertLiquidityToPosition(liquidity, totalLiquiditySupplied, _totalPositionSize);

-    if (liquidity == 0 || size == 0) revert InputError();
+    if (size == 0) revert InputError();
```

https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Lendgine.sol#L152-L180
### Lendgine.sol.withdraw(): Save ~4 SLOADs here

```solidity
File: /src/core/Lendgine.sol
152:  function withdraw(
153:    address to,
154:    uint256 size
155:  )
156:    external
157:    override
158:    nonReentrant
159:    returns (uint256 amount0, uint256 amount1, uint256 liquidity)
160:  {
161:    _accrueInterest();

163:    uint256 _totalPositionSize = totalPositionSize; // SLOAD
164:    uint256 _totalLiquidity = totalLiquidity; // SLOAD
165:    uint256 totalLiquiditySupplied = _totalLiquidity + totalLiquidityBorrowed;

167:    Position.Info memory positionInfo = positions[msg.sender]; // SLOAD
168:    liquidity = Position.convertPositionToLiquidity(size, totalLiquiditySupplied, _totalPositionSize);

170:    if (liquidity == 0 || size == 0) revert InputError();
```

Save more than 4 SLOADs here in case of a revert on `size == 0` Size being a function argument can be checked earlier. No need to waste gas doing other operations then revert on `size == 0`. 

```diff
diff --git a/src/core/Lendgine.sol b/src/core/Lendgine.sol
index e8086f4..6c6248b 100644
--- a/src/core/Lendgine.sol
+++ b/src/core/Lendgine.sol
@@ -160,6 +160,8 @@ contract Lendgine is ERC20, JumpRate, Pair, ILendgine {
   {
     _accrueInterest();

+    if (size == 0) revert InputError();
+
     uint256 _totalPositionSize = totalPositionSize; // SLOAD
     uint256 _totalLiquidity = totalLiquidity; // SLOAD
     uint256 totalLiquiditySupplied = _totalLiquidity + totalLiquidityBorrowed;
@@ -167,7 +169,7 @@ contract Lendgine is ERC20, JumpRate, Pair, ILendgine {
     Position.Info memory positionInfo = positions[msg.sender]; // SLOAD
     liquidity = Position.convertPositionToLiquidity(size, totalLiquiditySupplied, _totalPositionSize);

-    if (liquidity == 0 || size == 0) revert InputError();
+    if (liquidity == 0) revert InputError();

```


https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Factory.sol#L63-L88
### Factory.sol.createLendgine(): Check for local values first before running the storage ones
```solidity
File: /src/core/Factory.sol
63:  function createLendgine(
64:    address token0,
65:    address token1,
66:    uint8 token0Exp,
67:    uint8 token1Exp,
68:    uint256 upperBound
69:  )
70:    external
71:    override
72:    returns (address lendgine)
73:  {
74:    if (token0 == token1) revert SameTokenError();
75:    if (token0 == address(0) || token1 == address(0)) revert ZeroAddressError();
76:    if (getLendgine[token0][token1][token0Exp][token1Exp][upperBound] != address(0)) revert DeployedError();
77:    if (token0Exp > 18 || token0Exp < 6 || token1Exp > 18 || token1Exp < 6) revert ScaleError();
```
 Move the check that is cheaper to be checked first before checking for one that involves reading from storage. 

```diff
diff --git a/src/core/Factory.sol b/src/core/Factory.sol
index 9dfab6c..bee3af0 100644
--- a/src/core/Factory.sol
+++ b/src/core/Factory.sol
@@ -73,9 +73,10 @@ contract Factory is IFactory {
   {
     if (token0 == token1) revert SameTokenError();
     if (token0 == address(0) || token1 == address(0)) revert ZeroAddressError();
-    if (getLendgine[token0][token1][token0Exp][token1Exp][upperBound] != address(0)) revert DeployedError();
     if (token0Exp > 18 || token0Exp < 6 || token1Exp > 18 || token1Exp < 6) revert ScaleError();

+    if (getLendgine[token0][token1][token0Exp][token1Exp][upperBound] != address(0)) revert DeployedError();
+
     parameters =
       Parameters({token0: token0, token1: token1, token0Exp: token0Exp, token1Exp: token1Exp, upperBound: upperBound});
```


## Using storage instead of memory for structs/arrays saves gas

When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declearing the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct

https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Lendgine.sol#L167
```solidity
File: /src/core/Lendgine.sol
167:    Position.Info memory positionInfo = positions[msg.sender]; // SLOAD
```

```diff
diff --git a/src/core/Lendgine.sol b/src/core/Lendgine.sol
index e8086f4..6f2a2ea 100644
--- a/src/core/Lendgine.sol
+++ b/src/core/Lendgine.sol
@@ -164,7 +164,7 @@ contract Lendgine is ERC20, JumpRate, Pair, ILendgine {
     uint256 _totalLiquidity = totalLiquidity; // SLOAD
     uint256 totalLiquiditySupplied = _totalLiquidity + totalLiquidityBorrowed;

-    Position.Info memory positionInfo = positions[msg.sender]; // SLOAD
+    Position.Info storage positionInfo = positions[msg.sender]; // SLOAD
     liquidity = Position.convertPositionToLiquidity(size, totalLiquiditySupplied, _totalPositionSize);

```

https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/LiquidityManager.sol#L175
```solidity
File: /src/periphery/LiquidityManager.sol
175:    Position memory position = positions[params.recipient][lendgine]; // SLOAD
```

```diff
diff --git a/src/periphery/LiquidityManager.sol b/src/periphery/LiquidityManager.sol
index a1b6c9b..6b88eab 100644
--- a/src/periphery/LiquidityManager.sol
+++ b/src/periphery/LiquidityManager.sol
@@ -172,14 +172,13 @@ contract LiquidityManager is Multicall, Payment, SelfPermit, IPairMintCallback {
     );
     if (size < params.sizeMin) revert AmountError();

-    Position memory position = positions[params.recipient][lendgine]; // SLOAD
+    Position storage position = positions[params.recipient][lendgine]; // SLOAD

     (, uint256 rewardPerPositionPaid,) = ILendgine(lendgine).positions(address(this));
     position.tokensOwed += FullMath.mulDiv(position.size, rewardPerPositionPaid - position.rewardPerPositionPaid, 1e18);
     position.rewardPerPositionPaid = rewardPerPositionPaid;
     position.size += size;

-    positions[params.recipient][lendgine] = position; // SSTORE

     emit AddLiquidity(msg.sender, lendgine, params.liquidity, size, amount0, amount1, params.recipient);
   }
```

In the above, note the [Line 182](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/LiquidityManager.sol#L182) shown below was deleted
```solidity
182:    positions[params.recipient][lendgine] = position; // SSTORE
```
After changing to storage the above line is not needed as position would now be pointing to the storage and not just a copy

https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/LiquidityManager.sol#L211
```solidity
File: /src/periphery/LiquidityManager.sol
211:    Position memory position = positions[msg.sender][lendgine]; // SLOAD
```

https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/LiquidityManager.sol#L235
```solidity
File: /src/periphery/LiquidityManager.sol
235:    Position memory position = positions[msg.sender][params.lendgine]; // SLOAD
```

## Avoid contract existence checks by using solidity version 0.8.10 or later(1700 gas saved)

Prior to 0.8.10 the compiler inserted extra code, including EXTCODESIZE (100 gas), to check for contract existence for external calls. In more recent solidity versions, the compiler will not insert these checks if the external call has a return value

**Total Instances: 17**
**Gas Per Instance: 100**
`Total Gas saved: 17 * 100 = 1700`


https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/LiquidityManager.sol#L140-L142
```solidity
File: /src/periphery/LiquidityManager.sol

//@audit: reserve0()
140:    uint256 r0 = ILendgine(lendgine).reserve0();

//@audit: reserve1()
141:    uint256 r1 = ILendgine(lendgine).reserve1();

//@audit: totalLiquidity()
142:    uint256 totalLiquidity = ILendgine(lendgine).totalLiquidity();

//@audit: deposit(
157:    uint256 size = ILendgine(lendgine).deposit(

//@audit: positions(address(this))
177:    (, uint256 rewardPerPositionPaid,) = ILendgine(lendgine).positions(address(this));

//@audit: withdraw(recipient, params.size)
208:    (uint256 amount0, uint256 amount1, uint256 liquidity) = ILendgine(lendgine).withdraw(recipient, params.size);

//@audit: positions(address(this))
213:    (, uint256 rewardPerPositionPaid,) = ILendgine(lendgine).positions(address(this));

//@audit: positions(address(this))
237:    (, uint256 rewardPerPositionPaid,) = ILendgine(params.lendgine).positions(address(this));

//@audit: collect(recipient, amount)
246:    uint256 collectAmount = ILendgine(params.lendgine).collect(recipient, amount);

```

https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/LendgineRouter.sol#L147
```solidity
File: /src/periphery/LendgineRouter.sol

//@audit: mint(
147:    shares = ILendgine(lendgine).mint(

//@audit: reserve0()
198:    uint256 r0 = ILendgine(msg.sender).reserve0();

//@audit: reserve1()
199:    uint256 r1 = ILendgine(msg.sender).reserve1();

//@audit: totalLiquidity()
200:    uint256 totalLiquidity = ILendgine(msg.sender).totalLiquidity();

//@audit: convertLiquidityToCollateral(liquidity)
231:    uint256 collateralTotal = ILendgine(msg.sender).convertLiquidityToCollateral(liquidity);

//@audit: burn()
266:    amount = ILendgine(lendgine).burn(
```

https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/ImmutableState.sol#L33
```solidity
File: /src/core/ImmutableState.sol

//@audit: parameters()
33:    (token0, token1, _token0Exp, _token1Exp, upperBound) = Factory(msg.sender).parameters();
```

https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L46
```solidity
File: /src/periphery/UniswapV2/libraries/UniswapV2Library.sol

//@audit: getReserves()
46:    (uint256 reserve0, uint256 reserve1,) = IUniswapV2Pair(pairFor(factory, tokenA, tokenB)).getReserves();
```



## Use the cached value here instead of reading from storage
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Lendgine.sol#L123-L149
### Lendgine.sol.deposit(): totalPositionSize has already been cached
```solidity
File: /src/core/Lendgine.sol
123:  function deposit(
124:    address to,
125:    uint256 liquidity,
126:    bytes calldata data
127:  )
128:    external
129:    override
130:    nonReentrant
131:    returns (uint256 size)
132:  {
133:    _accrueInterest();

135:    uint256 _totalPositionSize = totalPositionSize; // SLOAD //@audit: Cached here

142:    if (totalLiquiditySupplied == 0 && totalPositionSize > 0) revert CompleteUtilizationError(); //@audit: totalPositionSize read from storage again instead of using the cached value
```

```diff
diff --git a/src/core/Lendgine.sol b/src/core/Lendgine.sol
index e8086f4..c2f030a 100644
--- a/src/core/Lendgine.sol
+++ b/src/core/Lendgine.sol
@@ -139,7 +139,7 @@ contract Lendgine is ERC20, JumpRate, Pair, ILendgine {
-    if (totalLiquiditySupplied == 0 && totalPositionSize > 0) revert CompleteUtilizationError();
+    if (totalLiquiditySupplied == 0 && _totalPositionSize > 0) revert CompleteUtilizationError();
```

https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Lendgine.sol#L152-L180
### Lendgine.sol.withdraw(): totalPositionSize already cached(~100 gas)
```solidity
File: /src/core/Lendgine.sol
152:  function withdraw(

163:    uint256 _totalPositionSize = totalPositionSize; // SLOAD //@audit: cached here

176:    totalPositionSize -= size; //@audit: Don't read from storage again
```

As `totalPositionSize` was already cached , we could avoid an SLOAD here

```diff
diff --git a/src/core/Lendgine.sol b/src/core/Lendgine.sol
index e8086f4..56c43ed 100644
--- a/src/core/Lendgine.sol
+++ b/src/core/Lendgine.sol
@@ -173,7 +173,7 @@ contract Lendgine is ERC20, JumpRate, Pair, ILendgine {
     if (liquidity > _totalLiquidity) revert CompleteUtilizationError();

     positions.update(msg.sender, -SafeCast.toInt256(size), rewardPerPositionStored);
-    totalPositionSize -= size;
+    totalPositionSize = _totalPositionSize - size;
     (amount0, amount1) = burn(to, liquidity);

     emit Withdraw(msg.sender, size, liquidity, to);
```

## x += y costs more gas than x = x + y for state variables
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Lendgine.sol#L91
```solidity
File: /src/core/Lendgine.sol
91:    totalLiquidityBorrowed += liquidity;
```

```diff
-    totalLiquidityBorrowed += liquidity;
+    totalLiquidityBorrowed = totalLiquidityBorrowed + liquidity;
```

https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Lendgine.sol#L114
```solidity
File: /src/core/Lendgine.sol
114:    totalLiquidityBorrowed -= liquidity;

176:    totalPositionSize -= size;

257:    rewardPerPositionStored += FullMath.mulDiv(dilutionSpeculative, 1e18, totalPositionSize);
```

## Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html Use a larger size then downcast where needed

https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Factory.sol#L63-L69
```solidity
File: /src/core/Factory.sol

//@audit: uint8 token0Exp,uint8 token1Exp
63:  function createLendgine(
64:    address token0,
65:    address token1,
66:    uint8 token0Exp,
67:    uint8 token1Exp,
68:    uint256 upperBound
69:  )
```

## Splitting require() statements that use && saves gas - (saves 8 gas per &&)

Instead of using the && operator in a single require statement to check multiple conditions,using multiple require statements with 1 condition per require statement will save 8 GAS per &&
The gas difference would only be realized if the revert condition is realized(met).
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L61
```solidity
File: /src/periphery/UniswapV2/libraries/UniswapV2Library.sol
61:    require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");

79:    require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");
```

## `keccak256()` should only need to be called on a specific string literal once

It should be saved to an immutable variable, and the variable used instead. If the hash is being used as a part of a function selector, the cast to `bytes4` should also only be done once
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/libraries/Balance.sol#L14
```solidity
File: /src/libraries/Balance.sol
14:  token.staticcall(abi.encodeWithSelector(bytes4(keccak256(bytes("balanceOf(address)"))),address(this)));
```

## Use a more recent version of solidity

Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Factory.sol#L2
```solidity
File:/src/core/Factory.sol
2:pragma solidity ^0.8.4;
```
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Lendgine.sol#L2
```solidity
File: /src/core/Lendgine.sol
2:pragma solidity ^0.8.4;
```
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/LiquidityManager.sol#L2
```solidity
File: LiquidityManager.sol
2:pragma solidity ^0.8.4;
```
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/LendgineRouter.sol#L2
```solidity
File: /src/periphery/LendgineRouter.sol
2:pragma solidity ^0.8.4;
```

## Captured in C4audit: Leaving it here as the explanations might be useful to the sponsor.
**Feel free to ignore when ranking report**

### Using unchecked blocks to save gas
Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block
[see resource](https://github.com/ethereum/solidity/issues/10695)
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Lendgine.sol#L201
```solidity
File: /src/core/Lendgine.sol
201:      position.tokensOwed = tokensOwed - collateral; // SSTORE
```

The operation `tokensOwed - collateral` would never underflow due to the check on [Line 198](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Lendgine.sol#L198) see below.
```solidity
198:    collateral = collateralRequested > tokensOwed ? tokensOwed : collateralRequested;
```

### poc
Suppose `tokensOwed` is 12, and `collateralRequested` is 5 .The value of collateral would be 
`collateral = 5 > 12 ? 12 : 5;` ie 5  and ` position.tokensOwed = 12 - 5`. 
In an event we requested more ,say `tokensOwed` is 5, and `collateralRequested` is 12, `collateral = 12 > 5 ? 5 : 12;` ie 5 and position.tokensOwed = 5 - 5`. 
It's clear no scenario would cause an underflow

```diff
diff --git a/src/core/Lendgine.sol b/src/core/Lendgine.sol
index e8086f4..93e4f55 100644
--- a/src/core/Lendgine.sol
+++ b/src/core/Lendgine.sol
@@ -198,7 +198,10 @@ contract Lendgine is ERC20, JumpRate, Pair, ILendgine {
     collateral = collateralRequested > tokensOwed ? tokensOwed : collateralRequested;

     if (collateral > 0) {
-      position.tokensOwed = tokensOwed - collateral; // SSTORE
+      unchecked {
+              position.tokensOwed = tokensOwed - collateral; // SSTORE
+      }
+
       SafeTransferLib.safeTransfer(token1, to, collateral);
     }
```

https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Lendgine.sol#L256
```solidity
File: /src/core/Lendgine.sol
256:    totalLiquidityBorrowed = _totalLiquidityBorrowed - dilutionLP;
```

The operation `_totalLiquidityBorrowed - dilutionLP` cannot underflow due to the check on [Line 253](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Lendgine.sol#L253) see below
```solidity
253:    uint256 dilutionLP = dilutionLPRequested > _totalLiquidityBorrowed ? _totalLiquidityBorrowed : dilutionLPRequested;
```

`dilutionLP` can only have two values here,one would be `_totalLiquidityBorrowed` if `dilutionLPRequested > _totalLiquidityBorrowed` which would cause line 256 to return 0,and the other value would be `dilutionLPRequested` if `dilutionLPRequested < _totalLiquidityBorrowed`. As we only get `dilutionLP` == `dilutionLPRequested` when `dilutionLPRequested` is less than `_totalLiquidityBorrowed` which in turn makes `dilutionLP` to be less than `_totalLiquidityBorrowed` , we could never underflow

```diff
diff --git a/src/core/Lendgine.sol b/src/core/Lendgine.sol
index e8086f4..cf6f3ef 100644
--- a/src/core/Lendgine.sol
+++ b/src/core/Lendgine.sol
@@ -253,7 +253,10 @@ contract Lendgine is ERC20, JumpRate, Pair, ILendgine {
     uint256 dilutionLP = dilutionLPRequested > _totalLiquidityBorrowed ? _totalLiquidityBorrowed : dilutionLPRequested;
     uint256 dilutionSpeculative = convertLiquidityToCollateral(dilutionLP);

-    totalLiquidityBorrowed = _totalLiquidityBorrowed - dilutionLP;
+    unchecked{
+      totalLiquidityBorrowed = _totalLiquidityBorrowed - dilutionLP;
+    }
+
```

https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/JumpRate.sol#L20
```solidity
File: /src/core/JumpRate.sol
20:      uint256 excessUtil = util - kink;
```
The operation ` util - kink` cannot underflow as it would only be performed when `util > kink` due to the check on [Line 16](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/JumpRate.sol#L16)
```diff
diff --git a/src/core/JumpRate.sol b/src/core/JumpRate.sol
index 5e734cd..e19c6dd 100644
--- a/src/core/JumpRate.sol
+++ b/src/core/JumpRate.sol
@@ -17,7 +17,11 @@ abstract contract JumpRate is IJumpRate {
       return (util * multiplier) / 1e18;
     } else {
       uint256 normalRate = (kink * multiplier) / 1e18;
-      uint256 excessUtil = util - kink;
+      uint256 excessUtil;
+      unchecked {
+        excessUtil = util - kink;
+      }
```
