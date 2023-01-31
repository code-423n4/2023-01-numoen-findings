### Gas Optimizations List
| Number | Optimization Details | Context |
|:--:|:-------| :-----:|
| [G-01] | Gas saving is achieved by removing the ``delete`` keyword (~30k) |1 |
| [G-02] |Structs can be packed into fewer storage slots |7 |
| [G-03] |Avoid contract existence checks by using solidity version 0.8.10 or later (2.4k gas) |24 |
| [G-04] |Gas savings can be achieved by changing the model for assigning value to the structure (130 gas)|1 |
| [G-05] |Gas saving by adding ``unchecked`` to ``getAmountOut`` function |1|
| [G-06] |State variable can be used instead of struct |1|
| [G-07] |Using `storage` instead of `memory` for `structs/arrays` saves gas |2|
| [G-08] |Setting the _constructor_ to `payable` (65 gas) |5|
| [G-09] |`x += y (x -= y)` costs more gas than `x = x + y (x = x - y)` for state variables |4|
| [G-10] |``>=`` costs less gas than ``>``|7|
| [G-11] |Use nested if and, avoid multiple check combinations|5|
| [G-12] |Use ``double require`` instead of using ``&&`` |2|
| [G-13] |Sort Solidity operations using short-circuit mode |2|
| [G-14] |Optimize names to save gas (22 gas)| All contracts |
| [G-15] |Use a more recent version of solidity | 14 |

Total 15 issues

### [G-01] Gas saving is achieved by removing the ``delete`` keyword (~30k)

30k gas savings were made by removing the ``delete`` keyword. The reason for using the ``delete`` keyword here is to reset the struct values (set to default value) in every operation. However, the struct values do not need to be zero each time the function is run. Therefore, the ``delete'' key word is unnecessary, if it is removed, around 30k gas savings will be achieved.


There is one instance of the subject:

```diff
src\core\Factory.sol:
  63   function createLendgine(
  64     address token0,
  65     address token1,
  66     uint8 token0Exp,
  67     uint8 token1Exp,
  68     uint256 upperBound
  69   )
  70     external
  71     override
  72     returns (address lendgine)
  73   {
  74     if (token0 == token1) revert SameTokenError();
  75     if (token0 == address(0) || token1 == address(0)) revert ZeroAddressError();
  76     if (getLendgine[token0][token1][token0Exp][token1Exp][upperBound] != address(0)) revert DeployedError();
  77     if (token0Exp > 18 || token0Exp < 6 || token1Exp > 18 || token1Exp < 6) revert ScaleError();
  78 
  79     parameters = 
  80       Parameters({token0: token0, token1: token1, token0Exp: token0Exp, token1Exp: token1Exp, upperBound: upperBound});
  81 
  82     lendgine = address(new Lendgine{ salt: keccak256(abi.encode(token0, token1, token0Exp, token1Exp, upperBound)) }());
  83 
- 84:     delete parameters;
  85 
  86     getLendgine[token0][token1][token0Exp][token1Exp][upperBound] = lendgine;
  87     emit LendgineCreated(token0, token1, token0Exp, token1Exp, upperBound, lendgine);
  88   }
```
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Factory.sol#L84

### [G-02] Structs can be packed into fewer storage slots

Each saved slot can avoid an extra Gsset (**20000 gas**) for the initial setting of the build.

In LiquidityManager.sol contract: ``PairMintCallbackData``, ``AddLiquidityParams``, ``RemoveLiquidityParams``

   In LendgineRouter.sol contract: ``MintCallbackData``, `MintParams``, ``PairMintCallbackData``, ``BurnParams``

If the structs _uint256 token0Exp_ and _uint256 token1Exp_ variables are changed to ``uint128 token0Exp`` and ``uint128 token1Exp``, one slot for each specified structure can be won in total **7 slots**.

7 results - 2 files:
```solidity
src\periphery\LiquidityManager.sol:
   91  
   92:   struct PairMintCallbackData {
   93:     address token0;
   94:     address token1;
   95:     uint256 token0Exp;
   96:     uint256 token1Exp;
   97:     uint256 upperBound;
   98:     uint256 amount0;
   99:     uint256 amount1;
  100:     address payer;
  101:   }
  102 

  120:   struct AddLiquidityParams {
  121:     address token0;
  122:     address token1;
  123:     uint256 token0Exp;
  124:     uint256 token1Exp;
  125:     uint256 upperBound;
  126:     uint256 liquidity;
  127:     uint256 amount0Min;
  128:     uint256 amount1Min;
  129:     uint256 sizeMin;
  130:     address recipient;
  131:     uint256 deadline;
  132:   }
  

  187:   struct RemoveLiquidityParams {
  188:     address token0;
  189:     address token1;
  190:     uint256 token0Exp;
  191:     uint256 token1Exp;
  192:     uint256 upperBound;
  193:     uint256 size;
  194:     uint256 amount0Min;
  195:     uint256 amount1Min;
  196:     address recipient;
  197:     uint256 deadline;
  198:   }
```
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L92-L101
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L120-L132
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L187-L198

```solidity
src/periphery/LendgineRouter.sol:
  73  
  74:   struct MintCallbackData {
  75:     address token0;
  76:     address token1;
  77:     uint256 token0Exp;
  78:     uint256 token1Exp;
  79:     uint256 upperBound;
  80:     uint256 collateralMax;
  81:     SwapType swapType;
  82:     bytes swapExtraData;
  83:     address payer;
  84:   }

  126:   struct MintParams {
  127:     address token0;
  128:     address token1;
  129:     uint256 token0Exp;
  130:     uint256 token1Exp;
  131:     uint256 upperBound;
  132:     uint256 amountIn;
  133:     uint256 amountBorrow;
  134:     uint256 sharesMin;
  135:     SwapType swapType;
  136:     bytes swapExtraData;
  137:     address recipient;
  138:     uint256 deadline;
  139:   }

  175:   struct PairMintCallbackData {
  176:     address token0;
  177:     address token1;
  178:     uint256 token0Exp;
  179:     uint256 token1Exp;
  180:     uint256 upperBound;
  181:     uint256 collateralMin;
  182:     uint256 amount0Min;
  183:     uint256 amount1Min;
  184:     SwapType swapType;
  185:     bytes swapExtraData;
  186:     address recipient;
  187:   }


  240:   struct BurnParams {
  241:     address token0;
  242:     address token1;
  243:     uint256 token0Exp;
  244:     uint256 token1Exp;
  245:     uint256 upperBound;
  246:     uint256 shares;
  247:     uint256 collateralMin;
  248:     uint256 amount0Min;
  249:     uint256 amount1Min;
  250:     SwapType swapType;
  251:     bytes swapExtraData;
  252:     address recipient;
  253:     uint256 deadline;
  254:   }
```
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LendgineRouter.sol#L74-L84
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LendgineRouter.sol#L126-L139
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LendgineRouter.sol#L175-L187
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LendgineRouter.sol#L240-L254

**Recommendation Code:**

1 slot can be saved by packing the "MintCallbackData" structure as suggested below.
```diff
src\periphery\LendgineRouter.sol:
  73
  74:   struct MintCallbackData {
  75:     address token0;
  76:     address token1;
- 77:     uint256 token0Exp;
+ 77:     uint128 token0Exp;
- 78:     uint256 token1Exp;
+ 78:     uint128 token1Exp;
  79:     uint256 upperBound;
  80:     uint256 collateralMax;
  81:     SwapType swapType;
  82:     bytes swapExtraData;
  83:     address payer;
  84:   }
  ```

### [G-03] Avoid contract existence checks by using solidity version 0.8.10 or later (2.4k gas)

Prior to 0.8.10 the compiler inserted extra code, including EXTCODESIZE (100 gas), to check for contract existence for external calls. In more recent solidity versions, the compiler will not insert these checks if the external call has a return value

24 results - 7 files:
```solidity
src\core\Lendgine.sol:
  //@audit mintCallback()
  96:      IMintCallback(msg.sender).mintCallback(collateral, amount0, amount1, liquidity, data);
```
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L96

```solidity
src\core\Pair.sol:
  //@audit pairMintCallback()
  77:      IPairMintCallback(msg.sender).pairMintCallback(liquidity, data);

  ///@audit swapCallback()
  127:    ISwapCallback(msg.sender).swapCallback(amount0Out, amount1Out, data);
```
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Pair.sol#L77

```solidity
src\periphery\LendgineRouter.sol:
  ///@audit mint()
  147:    shares = ILendgine(lendgine).mint(

  ///@audit reserve0()
  198:    uint256 r0 = ILendgine(msg.sender).reserve0();

  ///@audit reserve1()
  199:    uint256 r1 = ILendgine(msg.sender).reserve1();

  ///@audit totalLiquidity()
  200:    uint256 totalLiquidity = ILendgine(msg.sender).totalLiquidity();

  ///@audit convertLiquidityToCollateral()
  231:    uint256 collateralTotal = ILendgine(msg.sender).convertLiquidityToCollateral(liquidity);

  ///@audit burn()
  266:    amount = ILendgine(lendgine).burn(
```
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LendgineRouter.sol#L147

```solidity
src\periphery\LiquidityManager.sol:
  ///@audit reserve0()
  140:    uint256 r0 = ILendgine(lendgine).reserve0();

  ///@audit reserve1()
  141:    uint256 r1 = ILendgine(lendgine).reserve1();

  ///@audit totalLiquidity()
  142:    uint256 totalLiquidity = ILendgine(lendgine).totalLiquidity();

  ///@audit deposit()
  157:    uint256 size = ILendgine(lendgine).deposit(

  ///@audit positions()
  177:    (, uint256 rewardPerPositionPaid,) = ILendgine(lendgine).positions(address(this));

  ///@audit withdraw()
  208:    (uint256 amount0, uint256 amount1, uint256 liquidity) = ILendgine(lendgine).withdraw(recipient, params.size);

  ///@audit positions()
  213:    (, uint256 rewardPerPositionPaid,) = ILendgine(lendgine).positions(address(this));

  ///@audit accruePositionInterest()
  231:    ILendgine(params.lendgine).accruePositionInterest();

  ///@audit positions()
  237:    (, uint256 rewardPerPositionPaid,) = ILendgine(params.lendgine).positions(address(this));

  ///@audit collect(
  246:    uint256 collectAmount = ILendgine(params.lendgine).collect(recipient, amount);
```
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L140

```solidity
src\periphery\Payment.sol:
  ///@audit withdraw()
  30:     IWETH9(weth).withdraw(balanceWETH);

  ///@audit deposit()
  55:      IWETH9(weth).deposit{value: value}(); // wrap only what is needed to pay

  ///@audit swap()
  88:     IUniswapV2Pair(pair).swap(amount0Out, amount1Out, params.recipient, bytes(""));
```
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/Payment.sol#L30

```solidity
src\periphery\SwapHelper.sol:
  ///@audit swap()
  88:     IUniswapV2Pair(pair).swap(amount0Out, amount1Out, params.recipient, bytes(""));
```
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/SwapHelper.sol#L88

```solidity
src\periphery\UniswapV2\libraries\UniswapV2Library.sol:
  ///@audit getReserves()
  46:     (uint256 reserve0, uint256 reserve1,) = IUniswapV2Pair(pairFor(factory, tokenA, tokenB)).getReserves();
```
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L46

### [G-04] Gas savings can be achieved by changing the model for assigning value to the structure (130 gas)

By changing the pattern of assigning value to the structure, gas savings of ``~130`` per sample are achieved. In addition, this use will save gas on distribution costs.

There are one examples of this issue:

```solidity
src\core\Factory.sol:
  63   function createLendgine(

  79:     parameters = 
  80:       Parameters({token0: token0, token1: token1, token0Exp: token0Exp, token1Exp: token1Exp, upperBound: upperBound});
```
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Factory.sol#L79-L80

The following model, which is more gas efficient, can be preferred to assign value to the building elements.

```diff
src\core\Factory.sol:
  63   function createLendgine(

- 79:     parameters = 
- 80:       Parameters({token0: token0, token1: token1, token0Exp: token0Exp, token1Exp: token1Exp, upperBound: upperBound});

+          parameters. token0 =  token0 ;
+          parameters. token1 = token1;
+          parameters. token0Exp = token0Exp;
+          parameters. token1Exp = token1Exp;
+          parameters. upperBound = upperBound;
```

### [G-05] Gas saving by adding ``unchecked`` to ``getAmountOut`` function

Since the necessary 'require' checks are made in the 'getAmountOut' function, adding 'unchecked' will save approximately 100 gas.

```diff
src\periphery\UniswapV2\libraries\UniswapV2Library.sol:
  50    // given an input amount of an asset and pair reserves, returns the maximum output amount of the other asset
  51:   function getAmountOut(
  52:     uint256 amountIn,
  53:     uint256 reserveIn,
  54:     uint256 reserveOut
  55:   )
  56:     internal
  57:     pure
  58:     returns (uint256 amountOut)
  59:   {
  60:     require(amountIn > 0, "UniswapV2Library: INSUFFICIENT_INPUT_AMOUNT");
  61:     require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");
  62:     uint256 amountInWithFee = amountIn * 997;
  63:     uint256 numerator = amountInWithFee * reserveOut;
  64:     uint256 denominator = (reserveIn * 1000) + amountInWithFee;
  +       unchecked { 
  65:     amountOut = numerator / denominator;
  +       }   
  66:   }
```
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L65


### [G-06] State variable can be used instead of struct

Using the uint24 fee' state variable instead of the 'UniV3Data' structure saves gas.

1 result 1 file:
```solidity  
src\periphery\SwapHelper.sol:
  61:   struct UniV3Data {
  62:     uint24 fee;
  63:   }
```
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/SwapHelper.sol#L61-L63


### [G-07] Using `storage` instead of `memory` for `structs/arrays` saves gas

When fetching data from a storage location, assigning the data to a ``memory`` variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declearing the variable with the ``memory`` keyword, declaring the variable with the ``storage`` keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a ``memory`` variable, is if the full struct/array is being returned by the function, is being passed to a function that requires ``memory``, or if the array/struct is being read from another ``memory`` array/struct

2 results - 2 files:
```solidity
src\core\Lendgine.sol:

  167:     Position.Info memory positionInfo = positions[msg.sender]; // SLOAD
```
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L167

```solidity
src\core\libraries\Position.sol:

  47:     Position.Info memory _positionInfo = positionInfo;
```
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/libraries/Position.sol#L47


### [G-08] Setting the _constructor_ to `payable` (65 gas)

You can cut out 10 opcodes in the creation-time EVM bytecode if you declare a constructor payable. Making the constructor payable eliminates the need for an initial check of ```msg.value == 0``` and saves ```13 gas``` on deployment with no security risks.

5 results - 5 files:
```solidty
src\core\ImmutableState.sol:
  27:   constructor() {
```
github.com/code-423n4/2023-01-numoen/blob/main/src/core/ImmutableState.sol#L27

```solidty
src\periphery\LendgineRouter.sol:
  49:   constructor(
```
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LendgineRouter.sol#L49

```solidty
src\periphery\LiquidityManager.sol:
  75:   constructor(address _factory, address _weth) Payment(_weth) {
```
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L75

```solidty
src\periphery\Payment.sol:
  17:   constructor(address _weth) {
```
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/Payment.sol#L17

```solidty
src\periphery\SwapHelper.sol:
  29:   constructor(address _uniswapV2Factory, address _uniswapV3Factory) {
```
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/SwapHelper.sol#L29

**Recommendation:**
Set the constructor to ```payable```


### [G-09] `x += y (x -= y)` costs more gas than `x = x + y (x = x - y)` for state variables

4 results - 1 file:
```solidity
src\core\Lendgine.sol:
   91:     totalLiquidityBorrowed += liquidity;
  
  114:     totalLiquidityBorrowed -= liquidity;

  176:     totalPositionSize -= size;

  257:     rewardPerPositionStored += FullMath.mulDiv(dilutionSpeculative, 1e18, totalPositionSize);

```
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L91


```diff

src\core\Lendgine.sol:
- 91:     totalLiquidityBorrowed += liquidity;
+ 91:     totalLiquidityBorrowed = totalLiquidityBorrowed + liquidity;

```

`x += y (x -= y)` costs more gas than `x = x  + y (x = x - y)` for state variables.


### [G-10] ``>=`` costs less gas than ``>``

The compiler uses opcodes GT and ISZERO for solidity code that uses >, but only requires LT for >=, which saves 3 gas

7 results - 4 files:
```solidity
src\core\Lendgine.sol:
  198:    collateral = collateralRequested > tokensOwed ? tokensOwed : collateralRequested;

  253:    uint256 dilutionLP = dilutionLPRequested > _totalLiquidityBorrowed ? _totalLiquidityBorrowed : dilutionLPRequested;
```
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L198

```solidity
src\periphery\LiquidityManager.sol:
  241:    amount = params.amountRequested > position.tokensOwed ? position.tokensOwed : params.amountRequested;
```
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L241

```solidity
src\periphery\SwapHelper.sol:
  42:     SafeTransferLib.safeTransfer(tokenIn, msg.sender, amount0Delta > 0 ? uint256(amount0Delta) : uint256(amount1Delta));
  
  76:     amount = params.amount > 0
  77         ? UniswapV2Library.getAmountOut(uint256(params.amount), reserveIn, reserveOut)
  78         : UniswapV2Library.getAmountIn(uint256(-params.amount), reserveIn, reserveOut);
  
  81:     params.amount > 0 ? (uint256(params.amount), amount) : (amount, uint256(-params.amount));
```
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/SwapHelper.sol#L42

```solidity
src\periphery\UniswapV2\libraries\UniswapV2Library.sol:
  12:     (token0, token1) = tokenA < tokenB ? (tokenA, tokenB) : (tokenB, tokenA);
```
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L12


### [G-11] Use nested if and, avoid multiple check combinations

Using nested is cheaper than using && multiple check combinations. There are more advantages, such as easier to read code and better coverage reports.

5 results - 3 files:
```solidity
src\core\Lendgine.sol:
   89:     if (totalSupply > 0 && totalLiquidityBorrowed == 0) revert CompleteUtilizationError();

  142:     if (totalLiquiditySupplied == 0 && totalPositionSize > 0) revert CompleteUtilizationError();
```
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L89

```solidity
src\core\Pair.sol:
  100:     if (amount0 == 0 && amount1 == 0) revert InsufficientOutputError();

  117:     if (amount0Out == 0 && amount1Out == 0) revert InsufficientOutputError();
```
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Pair.sol#L100

```solidity
src\periphery\Payment.sol:
  53:     if (token == weth && address(this).balance >= value) {
```
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/Payment.sol#L53


**Recomendation Code:**
```diff
src\periphery\Payment.sol:
- 53:     if (token == weth && address(this).balance >= value) {
+          if (token == weth) {
+              if (address(this).balance >= value) {
+              }
+          }
```

### [G-12] Use ``double require`` instead of using ``&&``

Using double require instead of operator && can save more gas
When having a require statement with 2 or more expressions needed, place the expression that cost less gas first.


2 results - 1 file:
```solidity
src\periphery\UniswapV2\libraries\UniswapV2Library.sol:
  61:     require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");
  79:     require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");
```
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L61

**Recommendation Code:**
 ```diff
src\periphery\UniswapV2\libraries\UniswapV2Library.sol:
- 61:     require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");
+         require(reserveIn > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");
+         require(reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");
```

### [G-13] Sort Solidity operations using short-circuit mode

Short-circuiting is a solidity contract development model that uses ```OR/AND``` logic to sequence different cost operations. It puts low gas cost operations in the front and high gas cost operations in the back, so that if the front is low If the cost operation is feasible, you can skip (short-circuit) the subsequent high-cost Ethereum virtual machine operation.

```
//f(x) is a low gas cost operation 
//g(y) is a high gas cost operation 

//Sort operations with different gas costs as follows 
f(x) || g(y) 
f(x) && g(y)
```
2 results - 2 files:
```solidity
src\libraries\Balance.sol:
  15:     if (!success || data.length < 32) revert BalanceReturnError();
```
https://github.com/code-423n4/2023-01-numoen/blob/main/src/libraries/Balance.sol#L15

```solidity
src\periphery\Payment.sol:
  53:     if (token == weth && address(this).balance >= value) {
```
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/Payment.sol#L53

### [G-14] Optimize names to save gas (22 gas)

Contracts most called functions could simply save gas by function ordering via ```Method ID```. Calling a function at runtime will be cheaper if the function is positioned earlier in the order (has a relatively lower Method ID) because ```22 gas``` are added to the cost of a function for every position that came before it. The caller can save on gas if you prioritize most called functions. 

**Context:** 
All Contracts


**Recommendation:** 
Find a lower ```method ID``` name for the most called functions for example Call() vs. Call1() is cheaper by ```22 gas```
For example, the function IDs in the ```LendgineRouter.sol``` contract will be the most used; A lower method ID may be given.

**Proof of Consept:**
https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92

LendgineRouter.sol function names can be named and sorted according to METHOD ID

```js
Sighash   |   Function Signature
========================
2dde7c24  =>  mintCallback(uint256,uint256,uint256,uint256,bytes)
0df9e5b8  =>  mint(MintParams)
ef4ae61c  =>  pairMintCallback(uint256,bytes)
4c5aaed3  =>  burn(BurnParams)
```

### [G-15] Use a more recent version of solidity

In 0.8.15 the conditions necessary for inlining are relaxed. Benchmarks show that the change significantly decreases the bytecode size (which impacts the deployment cost) while the effect on the runtime gas usage is smaller.

In 0.8.17 prevent the incorrect removal of storage writes before calls to Yul functions that conditionally terminate the external EVM call; Simplify the starting offset of zero-length operations to zero. More efficient overflow checks for multiplication.

14 results - 14 files:
```solidity
src\core\Factory.sol:
  2: pragma solidity ^0.8.4;
```
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Factory.sol#L2

```solidity
src\core\ImmutableState.sol:
  2: pragma solidity ^0.8.0;
```
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/ImmutableState.sol#L2

```solidity
src\core\JumpRate.sol:
  2: pragma solidity ^0.8.0;
```
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol#L2

```solidity
src\core\Lendgine.sol:
  2: pragma solidity ^0.8.4;
```
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L2

```solidity
src\core\Pair.sol:
  2: pragma solidity ^0.8.4;
```
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L2

```solidity
src\core\libraries\Position.sol:
  2: pragma solidity ^0.8.4;
```
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/libraries/Position.sol#L2

```solidity
src\libraries\Balance.sol:
  2: pragma solidity ^0.8.4;
```
https://github.com/code-423n4/2023-01-numoen/blob/main/src/libraries/Balance.sol#L2

```solidity
src\libraries\SafeCast.sol:
  2: pragma solidity ^0.8.0;
```
https://github.com/code-423n4/2023-01-numoen/blob/main/src/libraries/SafeCast.sol#L2

```solidity
src\periphery\LendgineRouter.sol:
  2: pragma solidity ^0.8.4;
```
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LendgineRouter.sol#L2

```solidity
src\periphery\LiquidityManager.sol:
  2: pragma solidity ^0.8.4;
```
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L2

```solidity
src\periphery\Payment.sol:
  2: pragma solidity ^0.8.4;
```
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/Payment.sol#L2

```solidity
src\periphery\SwapHelper.sol:
  2: pragma solidity ^0.8.4;
```
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/SwapHelper.sol#L2

```solidity
src\periphery\libraries\LendgineAddress.sol:
  2: pragma solidity >=0.5.0;
```
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/libraries/LendgineAddress.sol#L2

```solidity
src\periphery\UniswapV2\libraries\UniswapV2Library.sol:
  1: pragma solidity >=0.8.0;
```
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L1