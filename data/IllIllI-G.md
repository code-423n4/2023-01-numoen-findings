## Summary

### Gas Optimizations
| |Issue|Instances|Total Gas Saved|
|-|:-|:-:|:-:|
| [G&#x2011;01] | Structs can be packed into fewer storage slots | 4 |  - |
| [G&#x2011;02] | Using `storage` instead of `memory` for structs/arrays saves gas | 3 |  12600 |
| [G&#x2011;03] | Avoid contract existence checks by using low level calls | 20 |  2000 |
| [G&#x2011;04] | State variables should be cached in stack variables rather than re-reading them from storage | 2 |  194 |
| [G&#x2011;05] | `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables | 4 |  452 |
| [G&#x2011;06] | Optimize names to save gas | 3 |  66 |
| [G&#x2011;07] | Use a more recent version of solidity | 2 |  - |
| [G&#x2011;08] | Use a more recent version of solidity | 4 |  - |
| [G&#x2011;09] | `>=` costs less gas than `>` | 3 |  9 |
| [G&#x2011;10] | Splitting `require()` statements that use `&&` saves gas | 2 |  6 |
| [G&#x2011;11] | Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead | 7 |  - |
| [G&#x2011;12] | Stack variable used as a cheaper cache for a state variable is only used once | 1 |  3 |
| [G&#x2011;13] | Multiple `if`-statements with mutually-exclusive conditions should be changed to `if`-`else` statements | 1 |  - |
| [G&#x2011;14] | Constructors can be marked `payable` | 5 |  - |

Total: 61 instances over 14 issues with **15330 gas** saved

Gas totals use lower bounds of ranges and count two iterations of each `for`-loop. All values above are runtime, not deployment, values; deployment values are listed in the individual issue descriptions. The table above as well as its gas numbers do not include any of the excluded findings.





## Gas Optimizations

### [G&#x2011;01]  Structs can be packed into fewer storage slots
Each slot saved can avoid an extra Gsset (**20000 gas**) for the first setting of the struct. Subsequent reads as well as writes have smaller gas savings

*There are 4 instances of this issue:*

```solidity
File: src/periphery/LendgineRouter.sol

/// @audit Variable ordering with 8 slots instead of the current 9:
///           uint256(32):token0Exp, uint256(32):token1Exp, uint256(32):upperBound, uint256(32):collateralMax, bytes(32):swapExtraData, address(20):token0, uint8(1):swapType, address(20):token1, address(20):payer
74      struct MintCallbackData {
75        address token0;
76        address token1;
77        uint256 token0Exp;
78        uint256 token1Exp;
79        uint256 upperBound;
80        uint256 collateralMax;
81        SwapType swapType;
82        bytes swapExtraData;
83        address payer;
84:     }

/// @audit Variable ordering with 11 slots instead of the current 12:
///           uint256(32):token0Exp, uint256(32):token1Exp, uint256(32):upperBound, uint256(32):amountIn, uint256(32):amountBorrow, uint256(32):sharesMin, bytes(32):swapExtraData, uint256(32):deadline, address(20):token0, uint8(1):swapType, address(20):token1, address(20):recipient
126     struct MintParams {
127       address token0;
128       address token1;
129       uint256 token0Exp;
130       uint256 token1Exp;
131       uint256 upperBound;
132       uint256 amountIn;
133       uint256 amountBorrow;
134       uint256 sharesMin;
135       SwapType swapType;
136       bytes swapExtraData;
137       address recipient;
138       uint256 deadline;
139:    }

/// @audit Variable ordering with 10 slots instead of the current 11:
///           uint256(32):token0Exp, uint256(32):token1Exp, uint256(32):upperBound, uint256(32):collateralMin, uint256(32):amount0Min, uint256(32):amount1Min, bytes(32):swapExtraData, address(20):token0, uint8(1):swapType, address(20):token1, address(20):recipient
175     struct PairMintCallbackData {
176       address token0;
177       address token1;
178       uint256 token0Exp;
179       uint256 token1Exp;
180       uint256 upperBound;
181       uint256 collateralMin;
182       uint256 amount0Min;
183       uint256 amount1Min;
184       SwapType swapType;
185       bytes swapExtraData;
186       address recipient;
187:    }

/// @audit Variable ordering with 12 slots instead of the current 13:
///           uint256(32):token0Exp, uint256(32):token1Exp, uint256(32):upperBound, uint256(32):shares, uint256(32):collateralMin, uint256(32):amount0Min, uint256(32):amount1Min, bytes(32):swapExtraData, uint256(32):deadline, address(20):token0, uint8(1):swapType, address(20):token1, address(20):recipient
240     struct BurnParams {
241       address token0;
242       address token1;
243       uint256 token0Exp;
244       uint256 token1Exp;
245       uint256 upperBound;
246       uint256 shares;
247       uint256 collateralMin;
248       uint256 amount0Min;
249       uint256 amount1Min;
250       SwapType swapType;
251       bytes swapExtraData;
252       address recipient;
253       uint256 deadline;
254:    }

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/periphery/LendgineRouter.sol#L74-L84

### [G&#x2011;02]  Using `storage` instead of `memory` for structs/arrays saves gas
When fetching data from a storage location, assigning the data to a `memory` variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (**2100 gas**) for *each* field of the struct/array. If the fields are read from the new memory variable, they incur an additional `MLOAD` rather than a cheap stack read. Instead of declearing the variable with the `memory` keyword, declaring the variable with the `storage` keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a `memory` variable, is if the full struct/array is being returned by the function, is being passed to a function that requires `memory`, or if the array/struct is being read from another `memory` array/struct

*There are 3 instances of this issue:*

```solidity
File: src/core/Lendgine.sol

167:      Position.Info memory positionInfo = positions[msg.sender]; // SLOAD

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/core/Lendgine.sol#L167

```solidity
File: src/core/libraries/Position.sol

47:       Position.Info memory _positionInfo = positionInfo;

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/core/libraries/Position.sol#L47

```solidity
File: src/periphery/LiquidityManager.sol

211:      Position memory position = positions[msg.sender][lendgine]; // SLOAD

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/periphery/LiquidityManager.sol#L211

### [G&#x2011;03]  Avoid contract existence checks by using low level calls
Prior to 0.8.10 the compiler inserted extra code, including `EXTCODESIZE` (**100 gas**), to check for contract existence for external function calls. In more recent solidity versions, the compiler will not insert these checks if the external call has a return value. Similar behavior can be achieved in earlier versions by using low-level calls, since low level calls never check for contract existence

*There are 20 instances of this issue:*

```solidity
File: src/core/ImmutableState.sol

/// @audit parameters()
33:       (token0, token1, _token0Exp, _token1Exp, upperBound) = Factory(msg.sender).parameters();

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/core/ImmutableState.sol#L33

```solidity
File: src/core/Pair.sol

/// @audit swapCallback()
127:      ISwapCallback(msg.sender).swapCallback(amount0Out, amount1Out, data);

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/core/Pair.sol#L127

```solidity
File: src/periphery/LendgineRouter.sol

/// @audit mint()
147:      shares = ILendgine(lendgine).mint(

/// @audit reserve0()
198:      uint256 r0 = ILendgine(msg.sender).reserve0();

/// @audit reserve1()
199:      uint256 r1 = ILendgine(msg.sender).reserve1();

/// @audit totalLiquidity()
200:      uint256 totalLiquidity = ILendgine(msg.sender).totalLiquidity();

/// @audit convertLiquidityToCollateral()
231:      uint256 collateralTotal = ILendgine(msg.sender).convertLiquidityToCollateral(liquidity);

/// @audit burn()
266:      amount = ILendgine(lendgine).burn(

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/periphery/LendgineRouter.sol#L147

```solidity
File: src/periphery/LiquidityManager.sol

/// @audit reserve0()
140:      uint256 r0 = ILendgine(lendgine).reserve0();

/// @audit reserve1()
141:      uint256 r1 = ILendgine(lendgine).reserve1();

/// @audit totalLiquidity()
142:      uint256 totalLiquidity = ILendgine(lendgine).totalLiquidity();

/// @audit deposit()
157:      uint256 size = ILendgine(lendgine).deposit(

/// @audit positions()
177:      (, uint256 rewardPerPositionPaid,) = ILendgine(lendgine).positions(address(this));

/// @audit withdraw()
208:      (uint256 amount0, uint256 amount1, uint256 liquidity) = ILendgine(lendgine).withdraw(recipient, params.size);

/// @audit positions()
213:      (, uint256 rewardPerPositionPaid,) = ILendgine(lendgine).positions(address(this));

/// @audit positions()
237:      (, uint256 rewardPerPositionPaid,) = ILendgine(params.lendgine).positions(address(this));

/// @audit collect()
246:      uint256 collectAmount = ILendgine(params.lendgine).collect(recipient, amount);

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/periphery/LiquidityManager.sol#L140

```solidity
File: src/periphery/Payment.sol

/// @audit withdraw()
30:         IWETH9(weth).withdraw(balanceWETH);

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/periphery/Payment.sol#L30

```solidity
File: src/periphery/SwapHelper.sol

/// @audit swap()
88:         IUniswapV2Pair(pair).swap(amount0Out, amount1Out, params.recipient, bytes(""));

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/periphery/SwapHelper.sol#L88

```solidity
File: src/periphery/UniswapV2/libraries/UniswapV2Library.sol

/// @audit getReserves()
46:       (uint256 reserve0, uint256 reserve1,) = IUniswapV2Pair(pairFor(factory, tokenA, tokenB)).getReserves();

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L46

### [G&#x2011;04]  State variables should be cached in stack variables rather than re-reading them from storage
The instances below point to the second+ access of a state variable within a function. Caching of a state variable replaces each Gwarmaccess (**100 gas**) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.

*There are 2 instances of this issue:*

```solidity
File: src/core/Lendgine.sol

/// @audit totalPositionSize on line 135
142:      if (totalLiquiditySupplied == 0 && totalPositionSize > 0) revert CompleteUtilizationError();

/// @audit totalLiquidityBorrowed on line 239
247:      uint256 _totalLiquidityBorrowed = totalLiquidityBorrowed; // SLOAD

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/core/Lendgine.sol#L142

### [G&#x2011;05]  `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables
Using the addition operator instead of plus-equals saves **[113 gas](https://gist.github.com/IllIllI000/cbbfb267425b898e5be734d4008d4fe8)**

*There are 4 instances of this issue:*

```solidity
File: src/core/Lendgine.sol

91:       totalLiquidityBorrowed += liquidity;

114:      totalLiquidityBorrowed -= liquidity;

176:      totalPositionSize -= size;

257:      rewardPerPositionStored += FullMath.mulDiv(dilutionSpeculative, 1e18, totalPositionSize);

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/core/Lendgine.sol#L91

### [G&#x2011;06]  Optimize names to save gas
`public`/`external` function names and `public` member variable names can be optimized to save gas. See [this](https://gist.github.com/IllIllI000/a5d8b486a8259f9f77891a919febd1a9) link for an example of how it works. Below are the interfaces/abstract contracts that can be optimized so that the most frequently-called functions use the least amount of gas possible during method lookup. Method IDs that have two leading zero bytes can save **128 gas** each during deployment, and renaming functions to have lower method IDs will save **22 gas** per call, [per sorted position shifted](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92)

*There are 3 instances of this issue:*

```solidity
File: src/periphery/LendgineRouter.sol

/// @audit mint(), burn()
20:   contract LendgineRouter is Multicall, Payment, SelfPermit, SwapHelper, IMintCallback, IPairMintCallback {

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/periphery/LendgineRouter.sol#L20

```solidity
File: src/periphery/LiquidityManager.sol

/// @audit pairMintCallback(), addLiquidity(), removeLiquidity(), collect()
16:   contract LiquidityManager is Multicall, Payment, SelfPermit, IPairMintCallback {

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/periphery/LiquidityManager.sol#L16

```solidity
File: src/periphery/Payment.sol

/// @audit unwrapWETH(), sweepToken(), refundETH()
12:   abstract contract Payment {

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/periphery/Payment.sol#L12

### [G&#x2011;07]  Use a more recent version of solidity
Use a solidity version of at least 0.8.0 to get overflow protection without `SafeMath`
Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining
Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than `revert()/require()` strings
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

*There are 2 instances of this issue:*

```solidity
File: src/core/libraries/PositionMath.sol

2:    pragma solidity >=0.5.0;

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/core/libraries/PositionMath.sol#L2

```solidity
File: src/periphery/libraries/LendgineAddress.sol

2:    pragma solidity >=0.5.0;

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/periphery/libraries/LendgineAddress.sol#L2

### [G&#x2011;08]  Use a more recent version of solidity
Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining
Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than `revert()/require()` strings
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

*There are 4 instances of this issue:*

```solidity
File: src/core/ImmutableState.sol

2:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/core/ImmutableState.sol#L2

```solidity
File: src/core/JumpRate.sol

2:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/core/JumpRate.sol#L2

```solidity
File: src/libraries/SafeCast.sol

2:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/libraries/SafeCast.sol#L2

```solidity
File: src/periphery/UniswapV2/libraries/UniswapV2Library.sol

1:    pragma solidity >=0.8.0;

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L1

### [G&#x2011;09]  `>=` costs less gas than `>`
The compiler uses opcodes `GT` and `ISZERO` for solidity code that uses `>`, but only requires `LT` for `>=`, [which saves **3 gas**](https://gist.github.com/IllIllI000/3dc79d25acccfa16dee4e83ffdc6ffde)

*There are 3 instances of this issue:*

```solidity
File: src/core/Lendgine.sol

198:      collateral = collateralRequested > tokensOwed ? tokensOwed : collateralRequested;

253:      uint256 dilutionLP = dilutionLPRequested > _totalLiquidityBorrowed ? _totalLiquidityBorrowed : dilutionLPRequested;

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/core/Lendgine.sol#L198

```solidity
File: src/periphery/LiquidityManager.sol

241:      amount = params.amountRequested > position.tokensOwed ? position.tokensOwed : params.amountRequested;

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/periphery/LiquidityManager.sol#L241

### [G&#x2011;10]  Splitting `require()` statements that use `&&` saves gas
See [this issue](https://github.com/code-423n4/2022-01-xdefi-findings/issues/128) which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper by **3 gas**

*There are 2 instances of this issue:*

```solidity
File: src/periphery/UniswapV2/libraries/UniswapV2Library.sol

61:       require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");

79:       require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L61

### [G&#x2011;11]  Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead
> When using elements that are smaller than 32 bytes, your contractâ€™s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html
Each operation involving a `uint8` costs an extra [**22-28 gas**](https://gist.github.com/IllIllI000/9388d20c70f9a4632eb3ca7836f54977) (depending on whether the other operand is also a variable of type `uint8`) as compared to ones involving `uint256`, due to the compiler having to clear the higher bits of the memory word before operating on the `uint8`, as well as the associated stack operations of doing so. Use a larger size then downcast where needed

*There are 7 instances of this issue:*

```solidity
File: src/core/Pair.sol

/// @audit uint120 reserve0
85:       reserve0 = _reserve0 + SafeCast.toUint120(amount0In); // SSTORE

/// @audit uint120 reserve1
86:       reserve1 = _reserve1 + SafeCast.toUint120(amount1In); // SSTORE

/// @audit uint120 reserve0
108:      reserve0 = _reserve0 - SafeCast.toUint120(amount0); // SSTORE

/// @audit uint120 reserve1
109:      reserve1 = _reserve1 - SafeCast.toUint120(amount1); // SSTORE

/// @audit uint120 reserve0
135:      reserve0 = _reserve0 + SafeCast.toUint120(amount0In) - SafeCast.toUint120(amount0Out); // SSTORE

/// @audit uint120 reserve1
136:      reserve1 = _reserve1 + SafeCast.toUint120(amount1In) - SafeCast.toUint120(amount1Out); // SSTORE

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/core/Pair.sol#L85

```solidity
File: src/libraries/SafeCast.sol

/// @audit uint120 z
9:        require((z = uint120(y)) == y);

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/libraries/SafeCast.sol#L9

### [G&#x2011;12]  Stack variable used as a cheaper cache for a state variable is only used once
If the variable is only accessed once, it's cheaper to use the state variable directly that one time, and save the **3 gas** the extra stack assignment would spend

*There is 1 instance of this issue:*

```solidity
File: src/core/Lendgine.sol

163:      uint256 _totalPositionSize = totalPositionSize; // SLOAD

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/core/Lendgine.sol#L163

### [G&#x2011;13]  Multiple `if`-statements with mutually-exclusive conditions should be changed to `if`-`else` statements
If two conditions are the same, their blocks should be combined

*There is 1 instance of this issue:*

```solidity
File: src/core/libraries/Position.sol

55        if (sizeDelta == 0) {
56          if (_positionInfo.size == 0) revert NoPositionError();
57          sizeNext = _positionInfo.size;
58        } else {
59          sizeNext = PositionMath.addDelta(_positionInfo.size, sizeDelta);
60        }
61    
62:       if (sizeDelta != 0) positionInfo.size = sizeNext;

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/core/libraries/Position.sol#L55-L62

### [G&#x2011;14]  Constructors can be marked `payable`
Payable functions cost less gas to execute, since the compiler does not have to add extra checks to ensure that a payment wasn't provided. A constructor can safely be marked as payable, since only the deployer would be able to pass funds, and the project itself would not pass any funds.

*There are 5 instances of this issue:*

```solidity
File: src/core/ImmutableState.sol

27      constructor() {
28:       factory = msg.sender;

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/core/ImmutableState.sol#L27-L28

```solidity
File: src/periphery/LendgineRouter.sol

49      constructor(
50        address _factory,
51        address _uniswapV2Factory,
52        address _uniswapV3Factory,
53        address _weth
54      )
55        SwapHelper(_uniswapV2Factory, _uniswapV3Factory)
56:       Payment(_weth)

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/periphery/LendgineRouter.sol#L49-L56

```solidity
File: src/periphery/LiquidityManager.sol

75:     constructor(address _factory, address _weth) Payment(_weth) {

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/periphery/LiquidityManager.sol#L75

```solidity
File: src/periphery/Payment.sol

17:     constructor(address _weth) {

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/periphery/Payment.sol#L17

```solidity
File: src/periphery/SwapHelper.sol

29:     constructor(address _uniswapV2Factory, address _uniswapV3Factory) {

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/periphery/SwapHelper.sol#L29


___

## Excluded findings
These findings are excluded from awards calculations because there are publicly-available automated tools that find them. The valid ones appear here for completeness

## Summary

### Gas Optimizations
| |Issue|Instances|Total Gas Saved|
|-|:-|:-:|:-:|
| [G&#x2011;01] | `require()`/`revert()` strings longer than 32 bytes cost extra gas | 5 |  - |
| [G&#x2011;02] | Using `> 0` costs more gas than `!= 0` when used on a `uint` in a `require()` statement | 2 |  12 |
| [G&#x2011;03] | Division by two should use bit shifting | 1 |  20 |
| [G&#x2011;04] | Use custom errors rather than `revert()`/`require()` strings to save gas | 9 |  - |

Total: 17 instances over 4 issues with **32 gas** saved

Gas totals use lower bounds of ranges and count two iterations of each `for`-loop. All values above are runtime, not deployment, values; deployment values are listed in the individual issue descriptions. The table above as well as its gas numbers do not include any of the excluded findings.





## Gas Optimizations

### [G&#x2011;01]  `require()`/`revert()` strings longer than 32 bytes cost extra gas
Each extra memory word of bytes past the original 32 [incurs an MSTORE](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#consider-having-short-revert-strings) which costs **3 gas**

*There are 5 instances of this issue:*

```solidity
File: src/periphery/UniswapV2/libraries/UniswapV2Library.sol

/// @audit (valid but excluded finding)
11:       require(tokenA != tokenB, "UniswapV2Library: IDENTICAL_ADDRESSES");

/// @audit (valid but excluded finding)
60:       require(amountIn > 0, "UniswapV2Library: INSUFFICIENT_INPUT_AMOUNT");

/// @audit (valid but excluded finding)
61:       require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");

/// @audit (valid but excluded finding)
78:       require(amountOut > 0, "UniswapV2Library: INSUFFICIENT_OUTPUT_AMOUNT");

/// @audit (valid but excluded finding)
79:       require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L11

### [G&#x2011;02]  Using `> 0` costs more gas than `!= 0` when used on a `uint` in a `require()` statement
This change saves **[6 gas](https://aws1.discourse-cdn.com/business6/uploads/zeppelin/original/2X/3/363a367d6d68851f27d2679d10706cd16d788b96.png)** per instance. The optimization works until solidity version [0.8.13](https://gist.github.com/IllIllI000/bf2c3120f24a69e489f12b3213c06c94) where there is a regression in gas costs.

*There are 2 instances of this issue:*

```solidity
File: src/periphery/UniswapV2/libraries/UniswapV2Library.sol

/// @audit (valid but excluded finding)
60:       require(amountIn > 0, "UniswapV2Library: INSUFFICIENT_INPUT_AMOUNT");

/// @audit (valid but excluded finding)
78:       require(amountOut > 0, "UniswapV2Library: INSUFFICIENT_OUTPUT_AMOUNT");

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L60

### [G&#x2011;03]  Division by two should use bit shifting
`<x> / 2` is the same as `<x> >> 1`. While the compiler uses the `SHR` opcode to accomplish both, the version that uses division incurs an overhead of [**20 gas**](https://gist.github.com/IllIllI000/ec0e4e6c4f52a6bca158f137a3afd4ff) due to `JUMP`s to and from a compiler utility function that introduces checks which can be avoided by using `unchecked {}` around the division by two

*There is 1 instance of this issue:*

```solidity
File: src/core/Pair.sol

/// @audit (valid but excluded finding)
63:       uint256 c = (scale1 * scale1) / 4;

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/core/Pair.sol#L63

### [G&#x2011;04]  Use custom errors rather than `revert()`/`require()` strings to save gas
Custom errors are available from solidity version 0.8.4. Custom errors save [**~50 gas**](https://gist.github.com/IllIllI000/ad1bd0d29a0101b25e57c293b4b0c746) each time they're hit by [avoiding having to allocate and store the revert string](https://blog.soliditylang.org/2021/04/21/custom-errors/#errors-in-depth). Not defining the strings also save deployment gas

*There are 9 instances of this issue:*

```solidity
File: src/core/libraries/PositionMath.sol

/// @audit (valid but excluded finding)
14:         require((z = x - uint256(-y)) < x, "LS");

/// @audit (valid but excluded finding)
16:         require((z = x + uint256(y)) >= x, "LA");

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/core/libraries/PositionMath.sol#L14

```solidity
File: src/periphery/Payment.sol

/// @audit (valid but excluded finding)
22:       require(msg.sender == weth, "Not WETH9");

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/periphery/Payment.sol#L22

```solidity
File: src/periphery/UniswapV2/libraries/UniswapV2Library.sol

/// @audit (valid but excluded finding)
11:       require(tokenA != tokenB, "UniswapV2Library: IDENTICAL_ADDRESSES");

/// @audit (valid but excluded finding)
13:       require(token0 != address(0), "UniswapV2Library: ZERO_ADDRESS");

/// @audit (valid but excluded finding)
60:       require(amountIn > 0, "UniswapV2Library: INSUFFICIENT_INPUT_AMOUNT");

/// @audit (valid but excluded finding)
61:       require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");

/// @audit (valid but excluded finding)
78:       require(amountOut > 0, "UniswapV2Library: INSUFFICIENT_OUTPUT_AMOUNT");

/// @audit (valid but excluded finding)
79:       require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");

```
https://github.com/code-423n4/2023-01-numoen/blob/c36e531dcadaec7dee8c61332f44870e4fafca13/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L11
