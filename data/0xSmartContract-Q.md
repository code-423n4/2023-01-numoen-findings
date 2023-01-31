## Summary
### Low Risk Issues List
| Number |Issues Details|Context|
|:--:|:-------|:--:|
|[L-01]|Some tokens can have 2 addresses, so should be done check other require| 1 |
|[L-02]|` createLendgine ` event is missing parameters| 1 |
|[L-03]|Missing Event for  initialize | 5 |
|[L-04]|Omissions in Events| 1 |
|[L-05]|Lack of control to assign 0 values in the value assignments of critical state variables in the constructor| 4 |
|[L-06]|Add to Event-Emit for critical function | 1 |
|[L-07]|Solmate's SafeTransferLib doesn't check whether the ERC20 contract exists | 29 |
|[L-08]|Loss of precision due to rounding| 1 |
|[L-09]|Cross-chain replay attacks are possible with  `computeAddress `| 1 |

Total 9 issues


### Non-Critical Issues List
| Number |Issues Details|Context|
|:--:|:-------|:--:|
| [NC-01] |Insufficient coverage|1|
| [NC-02] |NatSpec comments should be increased in contracts |All Contracts|
| [NC-03] |`Function writing` that does not comply with the `Solidity Style Guide`| All Contracts |
| [NC-04] |Include return parameters in NatSpec comments| All Contracts |
| [NC-05] |Tokens accidentally sent to the contract cannot be recovered| |
| [NC-06] |Take advantage of Custom Error's return value property | 37 |
| [NC-07] |Repeated validation logic can be converted into a function modifier| 4 |
| [NC-08] |Use a more recent version of Solidity| All contracts |
| [NC-09] |For functions, follow Solidity standard naming conventions (internal function style rule) | 12 |
| [NC-10] |Floating pragma| 12  |
| [NC-11] |Project Upgrade and Stop Scenario should be| |
| [NC-12] |Use descriptive names for Contracts and Libraries |  |
| [NC-13] |Use SMTChecker |  |
| [NC-14] |Add NatSpec Mapping comment| 3 |
| [NC-15] |Require messages are too short or not | 6 |
| [NC-16] |Use underscores for number literals | 2 |

Total 16 issues

### [L-01] Some tokens can have 2 addresses, so should be done check other require



### Impact

In the `Factory.createLendgine()` function, the Pair consisting of address token0 and token1 is checked for uniqueness;

```solidity

src/core/Factory.sol:
  62    /// @inheritdoc IFactory
  63:   function createLendgine(
  64:     address token0,
  65:     address token1,
  66:     uint8 token0Exp,
  67:     uint8 token1Exp,
  68:     uint256 upperBound
  69:   )
  70:     external
  71:     override
  72:     returns (address lendgine)
  73:   {
  74:     if (token0 == token1) revert SameTokenError();
  75:     if (token0 == address(0) || token1 == address(0)) revert ZeroAddressError();
  76:     if (getLendgine[token0][token1][token0Exp][token1Exp][upperBound] != address(0)) revert DeployedError();
  77:     if (token0Exp > 18 || token0Exp < 6 || token1Exp > 18 || token1Exp < 6) revert ScaleError();
  78: 
  79:     parameters =
  80:       Parameters({token0: token0, token1: token1, token0Exp: token0Exp, token1Exp: token1Exp, upperBound: upperBound});
  81: 
  82:     lendgine = address(new Lendgine{ salt: keccak256(abi.encode(token0, token1, token0Exp, token1Exp, upperBound)) }());
  83: 
  84:     delete parameters;
  85: 
  86:     getLendgine[token0][token1][token0Exp][token1Exp][upperBound] = lendgine;
  87:     emit LendgineCreated(token0, token1, token0Exp, token1Exp, upperBound, lendgine);
  88:   }
  89: }

```

But some tokens have 2 addresses through which they can be interacted with.

https://medium.com/chainsecurity/trueusd-compound-vulnerability-bc5b696d29e2

One of the two addresses can be used when interacting with the relevant token, so a check should be added additionally to prevent tokens that can be interacted with address 2




### Recommended Mitigation Steps

The name and symbol of Token0 and Token1 can be stored in a mapping and a require can be added to check with every existing Token0 and Token1


### [L-02] ` createLendgine ` event is missing parameters


The ` Factory.sol` contract has very important function; ` createLendgine ` 


However, only amounts are published in emits, whereas msg.sender must be specified in every transaction, a contract or web page listening to events cannot react to users, emit does not serve the purpose.
Basically, this event cannot be used


```solidity
src/core/Factory.sol:
  62    /// @inheritdoc IFactory
  63:   function createLendgine(
  64:     address token0,
  65:     address token1,
  66:     uint8 token0Exp,
  67:     uint8 token1Exp,
  68:     uint256 upperBound
  69:   )
  70:     external
  71:     override
  72:     returns (address lendgine)
  73:   {
  74:     if (token0 == token1) revert SameTokenError();
  75:     if (token0 == address(0) || token1 == address(0)) revert ZeroAddressError();
  76:     if (getLendgine[token0][token1][token0Exp][token1Exp][upperBound] != address(0)) revert DeployedError();
  77:     if (token0Exp > 18 || token0Exp < 6 || token1Exp > 18 || token1Exp < 6) revert ScaleError();
  78: 
  79:     parameters =
  80:       Parameters({token0: token0, token1: token1, token0Exp: token0Exp, token1Exp: token1Exp, upperBound: upperBound});
  81: 
  82:     lendgine = address(new Lendgine{ salt: keccak256(abi.encode(token0, token1, token0Exp, token1Exp, upperBound)) }());
  83: 
  84:     delete parameters;
  85: 
  86:     getLendgine[token0][token1][token0Exp][token1Exp][upperBound] = lendgine;
  87:     emit LendgineCreated(token0, token1, token0Exp, token1Exp, upperBound, lendgine);
  88:   }
  89: }
```



### Recommended Mitigation Steps
add `msg.sender` parameter in event-emit

### [L-03] Missing Event for  initialize

**Context:**
```solidity

5 result
src/core/ImmutableState.sol:
  26  
  27:   constructor() {
  28:     factory = msg.sender;
  29: 
  30:     uint128 _token0Exp;
  31:     uint128 _token1Exp;
  32: 
  33:     (token0, token1, _token0Exp, _token1Exp, upperBound) = Factory(msg.sender).parameters();
  34: 
  35:     token0Scale = 10 ** (18 - _token0Exp);
  36:     token1Scale = 10 ** (18 - _token1Exp);
  37:   }
  38: }


src/periphery/LendgineRouter.sol:
  48  
  49:   constructor(
  50:     address _factory,
  51:     address _uniswapV2Factory,
  52:     address _uniswapV3Factory,
  53:     address _weth
  54:   )
  55:     SwapHelper(_uniswapV2Factory, _uniswapV3Factory)
  56:     Payment(_weth)
  57:   {
  58:     factory = _factory;
  59:   }


src/periphery/LiquidityManager.sol:
  74  
  75:   constructor(address _factory, address _weth) Payment(_weth) {
  76:     factory = _factory;
  77:   }

src/periphery/Payment.sol:
  16  
  17:   constructor(address _weth) {
  18:     weth = _weth;
  19:   }

src/periphery/SwapHelper.sol:
  29:   constructor(address _uniswapV2Factory, address _uniswapV3Factory) {
  30:     uniswapV2Factory = _uniswapV2Factory;
  31:     uniswapV3Factory = _uniswapV3Factory;
  32:   }

```

**Description:**
Events help non-contract tools to track changes, and events prevent users from being surprised by changes
Issuing event-emit during initialization is a detail that many projects skip

**Recommendation:**
Add Event-Emit

### [L-04] Omissions in Events

Throughout the codebase, events are generally emitted when sensitive changes are made to the contracts. However, some events are missing important parameters

The events should include the new value and old value where possible:

```diff

src/core/Lendgine.sol:
  265    /// @param owner The address that this position belongs to
  266:   function _accruePositionInterest(address owner) private {
  267:     uint256 _rewardPerPositionStored = rewardPerPositionStored; // SLOAD
  268: 
  269:     positions.update(owner, 0, _rewardPerPositionStored);
  270: 
+             OldOwner = self[owner];
- 271:     emit AccruePositionInterest(owner, _rewardPerPositionStored);
+ 271:     emit AccruePositionInterest(OldOwner, owner,  _rewardPerPositionStored);

  272:   }


```


### [L-05] Lack of control to assign 0 values in the value assignments of critical state variables in the constructor


Critical values should be check 0 value in constructor
If values set the 0 by mistake, even for a while, it will cause a loss to the project.

```solidity

4 result

src/periphery/LendgineRouter.sol:
  48  
  49:   constructor(
  50:     address _factory,
  51:     address _uniswapV2Factory,
  52:     address _uniswapV3Factory,
  53:     address _weth
  54:   )
  55:     SwapHelper(_uniswapV2Factory, _uniswapV3Factory)
  56:     Payment(_weth)
  57:   {
  58:     factory = _factory;
  59:   }


src/periphery/LiquidityManager.sol:
  74  
  75:   constructor(address _factory, address _weth) Payment(_weth) {
  76:     factory = _factory;
  77:   }

src/periphery/Payment.sol:
  16  
  17:   constructor(address _weth) {
  18:     weth = _weth;
  19:   }

src/periphery/SwapHelper.sol:
  29:   constructor(address _uniswapV2Factory, address _uniswapV3Factory) {
  30:     uniswapV2Factory = _uniswapV2Factory;
  31:     uniswapV3Factory = _uniswapV3Factory;
  32:   }

```

### [L-06] Add to Event-Emit for critical function


```solidity

src/periphery/LendgineRouter.sol:
   86    /// @notice Transfer the necessary amount of token1 to mint an option position
   87:   function mintCallback(
   88:     uint256 collateralTotal,
   89:     uint256 amount0,
   90:     uint256 amount1,
   91:     uint256,
   92:     bytes calldata data
   93:   )
   94:     external
   95:     override
   96:   {
   97:     MintCallbackData memory decoded = abi.decode(data, (MintCallbackData));
   98: 
   99:     address lendgine = LendgineAddress.computeAddress(
  100:       factory, decoded.token0, decoded.token1, decoded.token0Exp, decoded.token1Exp, decoded.upperBound
  101:     );
  102:     if (lendgine != msg.sender) revert ValidationError();
  103: 
  104:     // swap all token0 to token1
  105:     uint256 collateralSwap = swap(
  106:       decoded.swapType,
  107:       SwapParams({
  108:         tokenIn: decoded.token0,
  109:         tokenOut: decoded.token1,
  110:         amount: SafeCast.toInt256(amount0),
  111:         recipient: msg.sender
  112:       }),
  113:       decoded.swapExtraData
  114:     );
  115: 
  116:     // send token1 back
  117:     SafeTransferLib.safeTransfer(decoded.token1, msg.sender, amount1);
  118: 
  119:     // pull the rest of tokens from the user
  120:     uint256 collateralIn = collateralTotal - amount1 - collateralSwap;
  121:     if (collateralIn > decoded.collateralMax) revert AmountError();
  122: 
  123:     pay(decoded.token1, decoded.payer, msg.sender, collateralIn);
  124:   }
```


### [L-07] Solmate's SafeTransferLib doesn't check whether the ERC20 contract exists

Solmate's SafeTransferLib, which is often used to interact with non-compliant/unsafe ERC20 tokens, does not check whether the ERC20 contract exists. The following code will not revert in case the token doesn't exist (yet).

This is stated in the Solmate library:
https://github.com/transmissions11/solmate/blob/main/src/utils/SafeTransferLib.sol#L9



```solidity
29 results 

src/core/Lendgine.sol:
   13  import { Position } from "./libraries/Position.sol";
   14: import { SafeTransferLib } from "../libraries/SafeTransferLib.sol";
   15  import { SafeCast } from "../libraries/SafeCast.sol";

  115      _burn(address(this), shares);
  116:     SafeTransferLib.safeTransfer(token1, to, collateral); // optimistically transfer
  117      mint(liquidity, data);

  201        position.tokensOwed = tokensOwed - collateral; // SSTORE
  202:       SafeTransferLib.safeTransfer(token1, to, collateral);
  203      }

src/core/Pair.sol:
   13  import { SafeCast } from "../libraries/SafeCast.sol";
   14: import { SafeTransferLib } from "../libraries/SafeTransferLib.sol";
   15  

  101  
  102:     if (amount0 > 0) SafeTransferLib.safeTransfer(token0, to, amount0);
  103:     if (amount1 > 0) SafeTransferLib.safeTransfer(token1, to, amount1);
  104  

  121  
  122:     if (amount0Out > 0) SafeTransferLib.safeTransfer(token0, to, amount0Out);
  123:     if (amount1Out > 0) SafeTransferLib.safeTransfer(token1, to, amount1Out);
  124  

src/periphery/LendgineRouter.sol:
   15  import { SafeCast } from "../libraries/SafeCast.sol";
   16: import { SafeTransferLib } from "../libraries/SafeTransferLib.sol";
   17  

  116      // send token1 back
  117:     SafeTransferLib.safeTransfer(decoded.token1, msg.sender, amount1);
  118  

  165  
  166:     SafeTransferLib.safeTransfer(lendgine, params.recipient, shares);
  167  

  227      // pay token1
  228:     SafeTransferLib.safeTransfer(decoded.token1, msg.sender, amount1);
  229  

  235      if (decoded.recipient != address(this)) {
  236:       SafeTransferLib.safeTransfer(decoded.token1, decoded.recipient, collateralOut);
  237      }

  263  
  264:     SafeTransferLib.safeTransferFrom(lendgine, msg.sender, lendgine, params.shares);
  265  

src/periphery/Payment.sol:
   6  import { Balance } from "./../libraries/Balance.sol";
   7: import { SafeTransferLib } from "./../libraries/SafeTransferLib.sol";
   8  

  30        IWETH9(weth).withdraw(balanceWETH);
  31:       SafeTransferLib.safeTransferETH(recipient, balanceWETH);
  32      }

  39      if (balanceToken > 0) {
  40:       SafeTransferLib.safeTransfer(token, recipient, balanceToken);
  41      }

  44    function refundETH() external payable {
  45:     if (address(this).balance > 0) SafeTransferLib.safeTransferETH(msg.sender, address(this).balance);
  46    }

  55        IWETH9(weth).deposit{value: value}(); // wrap only what is needed to pay
  56:       SafeTransferLib.safeTransfer(weth, recipient, value);
  57      } else if (payer == address(this)) {
  58        // pay with tokens already in the contract (for the exact input multihop case)
  59:       SafeTransferLib.safeTransfer(token, recipient, value);
  60      } else {
  61        // pull payment
  62:       SafeTransferLib.safeTransferFrom(token, payer, recipient, value);
  63      }

src/periphery/SwapHelper.sol:
   8  import { PoolAddress } from "./UniswapV3/libraries/PoolAddress.sol";
   9: import { SafeTransferLib } from "../libraries/SafeTransferLib.sol";
  10  import { TickMath } from "./UniswapV3/libraries/TickMath.sol";

  41  
  42:     SafeTransferLib.safeTransfer(tokenIn, msg.sender, amount0Delta > 0 ? uint256(amount0Delta) : uint256(amount1Delta));
  43    }

  86  
  87:       SafeTransferLib.safeTransfer(params.tokenIn, pair, amountIn);
  88        IUniswapV2Pair(pair).swap(amount0Out, amount1Out, params.recipient, bytes(""));

```


### Recommended Mitigation Steps

Add a contract exist control in functions;
```js
pragma solidity >=0.8.0;

function isContract(address _addr) private returns (bool isContract) {
    isContract = _addr.code.length > 0;
}

```

### [L-08] Loss of precision due to rounding

Add scalar so roundings are negligible

```solidity
src/periphery/UniswapV2/libraries/UniswapV2Library.sol:
 65:     amountOut = numerator / denominator;
```


```solidity

src/periphery/UniswapV2/libraries/UniswapV2Library.sol:
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
  65:     amountOut = numerator / denominator;
  66:   }

```


### [L-09]  Cross-chain replay attacks are possible with  `computeAddress `


The computeAddress() function can generate the same address on different networks if the network is forked (Ethereum network has been forked in the past and may be forked in the future), funds may be compromised

https://mirror.xyz/0xbuidlerdao.eth/lOE5VN-BHI0olGOXe27F0auviIuoSlnou_9t3XRJseY

There is no chain.id in the keccak256data



```solidity
src/periphery/libraries/LendgineAddress.sol:
   8  
   9:   function computeAddress(
  10:     address factory,
  11:     address token0,
  12:     address token1,
  13:     uint256 token0Exp,
  14:     uint256 token1Exp,
  15:     uint256 upperBound
  16:   )
  17:     internal
  18:     pure
  19:     returns (address lendgine)
  20:   {
  21:     lendgine = address(
  22:       uint160(
  23:         uint256(
  24:           keccak256(
  25:             abi.encodePacked(
  26:               hex"ff",
  27:               factory,
  28:               keccak256(abi.encode(token0, token1, token0Exp, token1Exp, upperBound)),
  29:               bytes32(INIT_CODE_HASH)
  30:             )
  31:           )
  32:         )
  33:       )
  34:     );
  35:   }
  36  }



```

Similar issue from Harpieio;
https://github.com/Harpieio/contracts/pull/4/commits/de24a50349ec014163180ba60b5305098f42eb14


### Recommended Mitigation Steps
Include the chain.id in keccak256

### [N-01] Insufficient coverage

**Description:**
The test coverage rate of the project is ~60%. Testing all functions is best practice in terms of security criteria.
Due to its capacity, test coverage is expected to be 100%.


```js
| File                                                   | % Lines          | % Statements     | % Branches      | % Funcs        |
|--------------------------------------------------------|------------------|------------------|-----------------|----------------|
| src/core/ERC20.sol                                     | 70.37% (19/27)   | 66.67% (20/30)   | 33.33% (2/6)    | 62.50% (5/8)   |
| src/core/Factory.sol                                   | 100.00% (9/9)    | 92.31% (12/13)   | 87.50% (7/8)    | 100.00% (1/1)  |
| src/core/JumpRate.sol                                  | 72.73% (8/11)    | 64.71% (11/17)   | 75.00% (3/4)    | 66.67% (2/3)   |
| src/core/Lendgine.sol                                  | 100.00% (80/80)  | 99.04% (103/104) | 96.15% (25/26)  | 84.62% (11/13) |
| src/core/Pair.sol                                      | 100.00% (51/51)  | 93.24% (69/74)   | 77.27% (17/22)  | 100.00% (4/4)  |
| src/core/libraries/Position.sol                        | 0.00% (0/16)     | 0.00% (0/19)     | 0.00% (0/10)    | 0.00% (0/4)    |
| src/core/libraries/PositionMath.sol                    | 0.00% (0/3)      | 0.00% (0/3)      | 0.00% (0/6)     | 0.00% (0/1)    |
| src/libraries/Balance.sol                              | 100.00% (4/4)    | 80.00% (4/5)     | 50.00% (1/2)    | 100.00% (1/1)  |
| src/libraries/FullMath.sol                             | 0.00% (0/30)     | 0.00% (0/32)     | 0.00% (0/8)     | 0.00% (0/2)    |
| src/libraries/SafeCast.sol                             | 0.00% (0/3)      | 0.00% (0/3)      | 0.00% (0/4)     | 0.00% (0/2)    |
| src/libraries/SafeTransferLib.sol                      | 40.00% (4/10)    | 25.00% (3/12)    | 14.29% (1/7)    | 50.00% (2/4)   |
| src/periphery/LendgineRouter.sol                       | 0.00% (0/39)     | 0.00% (0/60)     | 0.00% (0/16)    | 0.00% (0/4)    |
| src/periphery/LiquidityManager.sol                     | 6.12% (3/49)     | 8.45% (6/71)     | 6.25% (1/16)    | 50.00% (2/4)   |
| src/periphery/Multicall.sol                            | 0.00% (0/6)      | 0.00% (0/10)     | 0.00% (0/4)     | 0.00% (0/1)    |
| src/periphery/Payment.sol                              | 0.00% (0/16)     | 0.00% (0/21)     | 0.00% (0/14)    | 0.00% (0/4)    |
| src/periphery/SelfPermit.sol                           | 0.00% (0/2)      | 0.00% (0/2)      | 100.00% (0/0)   | 0.00% (0/2)    |
| src/periphery/SwapHelper.sol                           | 0.00% (0/23)     | 0.00% (0/30)     | 0.00% (0/6)     | 0.00% (0/2)    |
| src/periphery/UniswapV2/libraries/UniswapV2Library.sol | 0.00% (0/19)     | 0.00% (0/27)     | 0.00% (0/12)    | 0.00% (0/5)    |
| src/periphery/UniswapV3/libraries/PoolAddress.sol      | 0.00% (0/4)      | 0.00% (0/5)      | 0.00% (0/4)     | 0.00% (0/2)    |
| src/periphery/libraries/LendgineAddress.sol            | 100.00% (1/1)    | 100.00% (1/1)    | 100.00% (0/0)   | 100.00% (1/1)  |
| Total                                                  | 39.56% (180/455) | 38.40% (230/599) | 28.08% (57/203) | 37.97% (30/79) |

```


### [N-02] NatSpec comments should be increased in contracts

**Context:**
All Contracts

**Description:**
It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation.
In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.
https://docs.soliditylang.org/en/v0.8.15/natspec-format.html

**Recommendation:**
NatSpec comments should be increased in contracts

### [N-03] `Function writing` that does not comply with the `Solidity Style Guide`

**Context:**
All Contracts

**Description:**
Order of Functions; ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. But there are contracts in the project that do not comply with this.

https://docs.soliditylang.org/en/v0.8.17/style-guide.html

Functions should be grouped according to their visibility and ordered:

 constructor
receive function (if exists)
fallback function (if exists)
external
public
internal
private
within a grouping, place the view and pure functions last

### [N-04] Include return parameters in NatSpec comments

**Context:**
All Contracts

**Description:**
It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation. In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.

https://docs.soliditylang.org/en/v0.8.15/natspec-format.html

**Recommendation:**
Include return parameters in NatSpec comments

_Recommendation  Code Style: (from Uniswap3)_
```js
    /// @notice Adds liquidity for the given recipient/tickLower/tickUpper position
    /// @dev The caller of this method receives a callback in the form of IUniswapV3MintCallback#uniswapV3MintCallback
    /// in which they must pay any token0 or token1 owed for the liquidity. The amount of token0/token1 due depends
    /// on tickLower, tickUpper, the amount of liquidity, and the current price.
    /// @param recipient The address for which the liquidity will be created
    /// @param tickLower The lower tick of the position in which to add liquidity
    /// @param tickUpper The upper tick of the position in which to add liquidity
    /// @param amount The amount of liquidity to mint
    /// @param data Any data that should be passed through to the callback
    /// @return amount0 The amount of token0 that was paid to mint the given amount of liquidity. Matches the value in the callback
    /// @return amount1 The amount of token1 that was paid to mint the given amount of liquidity. Matches the value in the callback
    function mint(
        address recipient,
        int24 tickLower,
        int24 tickUpper,
        uint128 amount,
        bytes calldata data
    ) external returns (uint256 amount0, uint256 amount1);
```

### [N-05] Tokens accidentally sent to the contract cannot be recovered


It can't be recovered if the tokens accidentally arrive at the contract address, which has happened to many popular projects, so I recommend adding a recovery code to your critical contracts.

### Recommended Mitigation Steps

Add this code:

```solidity
 /**
  * @notice Sends ERC20 tokens trapped in contract to external address
  * @dev Onlyowner is allowed to make this function call
  * @param account is the receiving address
  * @param externalToken is the token being sent
  * @param amount is the quantity being sent
  * @return boolean value indicating whether the operation succeeded.
  *
 */
  function rescueERC20(address account, address externalToken, uint256 amount) public onlyOwner returns (bool) {
    IERC20(externalToken).transfer(account, amount);
    return true;
  }
}

```


### [N-06] Take advantage of Custom Error's return value property

An important feature of Custom Error is that values such as address, tokenID, msg.value can 
be written inside the `()` sign, this kind of approach provides a serious advantage in 
debugging and examining the revert details of dapps such as tenderly.


```solidity
37 results - 8 files

src/core/Factory.sol:
  74:     if (token0 == token1) revert SameTokenError();
  75:     if (token0 == address(0) || token1 == address(0)) revert ZeroAddressError();
  76:     if (getLendgine[token0][token1][token0Exp][token1Exp][upperBound] != address(0)) revert DeployedError();
  77:     if (token0Exp > 18 || token0Exp < 6 || token1Exp > 18 || token1Exp < 6) revert ScaleError();

src/core/Lendgine.sol:
   86:     if (collateral == 0 || liquidity == 0 || shares == 0) revert InputError();
   87:     if (liquidity > totalLiquidity) revert CompleteUtilizationError();
   89:     if (totalSupply > 0 && totalLiquidityBorrowed == 0) revert CompleteUtilizationError();
   99:     if (balanceAfter < balanceBefore + collateral) revert InsufficientInputError();
  112:     if (collateral == 0 || liquidity == 0 || shares == 0) revert InputError();
  140:     if (liquidity == 0 || size == 0) revert InputError();
  142:     if (totalLiquiditySupplied == 0 && totalPositionSize > 0) revert CompleteUtilizationError();
  170:     if (liquidity == 0 || size == 0) revert InputError();
  172:     if (size > positionInfo.size) revert InsufficientPositionError();
  173:     if (liquidity > _totalLiquidity) revert CompleteUtilizationError();

src/core/Pair.sol:
   59:     if (scale1 > 2 * upperBound) revert InvariantError();
   82:       revert InvariantError();
  100:     if (amount0 == 0 && amount1 == 0) revert InsufficientOutputError();
  106:     if (!invariant(_reserve0 - amount0, _reserve1 - amount1, _totalLiquidity - liquidity)) revert InvariantError();
  117:     if (amount0Out == 0 && amount1Out == 0) revert InsufficientOutputError();
  132:       revert InvariantError();

src/core/libraries/Position.sol:
  56:       if (_positionInfo.size == 0) revert NoPositionError();

src/libraries/Balance.sol:
  15:     if (!success || data.length < 32) revert BalanceReturnError();

src/periphery/LendgineRouter.sol:
   66:     if (deadline < block.timestamp) revert LivelinessError();
  102:     if (lendgine != msg.sender) revert ValidationError();
  121:     if (collateralIn > decoded.collateralMax) revert AmountError();
  164:     if (shares < params.sharesMin) revert AmountError();
  196:     if (lendgine != msg.sender) revert ValidationError();
  213:     if (amount0 < decoded.amount0Min || amount1 < decoded.amount1Min) revert AmountError();
  233:     if (collateralOut < decoded.collateralMin) revert AmountError();

src/periphery/LiquidityManager.sol:
   84:     if (deadline < block.timestamp) revert LivelinessError();
  110:     if (lendgine != msg.sender) revert ValidationError();
  155:     if (amount0 < params.amount0Min || amount1 < params.amount1Min) revert AmountError();
  173:     if (size < params.sizeMin) revert AmountError();
  209:     if (amount0 < params.amount0Min || amount1 < params.amount1Min) revert AmountError();
  247:     if (collectAmount != amount) revert CollectError(); // extra check for safety

src/periphery/Payment.sol:
  27:     if (balanceWETH < amountMinimum) revert InsufficientOutputError();
  37:     if (balanceToken < amountMinimum) revert InsufficientOutputError();

```
### [N-07] Repeated validation logic can be converted into a function modifier

If a query or logic is repeated over many lines, using a modifier improves the readability and reusability of the code


```solidity

4 results

src/core/Factory.sol:
  75:     if (token0 == address(0) || token1 == address(0)) revert ZeroAddressError();

src/periphery/LendgineRouter.sol:
  262:     address recipient = params.recipient == address(0) ? address(this) : params.recipient;

src/periphery/LiquidityManager.sol:
  206:     address recipient = params.recipient == address(0) ? address(this) : params.recipient;
  233:     address recipient = params.recipient == address(0) ? address(this) : params.recipient;
```


### [N-08] Use a more recent version of Solidity

**Context:**
All contracts

**Description:**
For security, it is best practice to use the latest Solidity version.
For the security fix list in the versions;
https://github.com/ethereum/solidity/blob/develop/Changelog.md


**Recommendation:**
Old version of Solidity is used `(0.8.0 - 0.8.4)`, newer version can be used `(0.8.17)` 



### [N-09] For functions, follow Solidity standard naming conventions (internal function style rule)

**Context:**
```solidity
12 results - 8 files

src/core/Pair.sol:
  70:   function mint(uint256 liquidity, bytes calldata data) internal {
  93:   function burn(address to, uint256 liquidity) internal returns (uint256 amount0, uint256 amount1) {

src/core/libraries/Position.sol:
  38:   function update(mapping(address => Position.Info) storage self,address owner,int256 sizeDelta,uint256 rewardPerPosition) internal
  63:   function newTokensOwed(Position.Info memory position, uint256 rewardPerPosition) internal pure returns (uint256) {

src/core/libraries/PositionMath.sol:
  12:   function addDelta(uint256 x, int256 y) internal pure returns (uint256 z) {

src/libraries/Balance.sol:
  12:   function balance(address token) internal view returns (uint256) {

src/libraries/SafeCast.sol:
   8:   function toUint120(uint256 y) internal pure returns (uint120 z) {
  15:   function toInt256(uint256 y) internal pure returns (int256 z) {

src/periphery/Payment.sol:
  52:   function pay(address token, address payer, address recipient, uint256 value) internal {

src/periphery/SwapHelper.sol:
  69:   function swap(SwapType swapType, SwapParams memory params, bytes memory data) internal returns (uint256 amount) {

src/periphery/UniswapV2/libraries/UniswapV2Library.sol:
  10:   function sortTokens(address tokenA, address tokenB) internal pure returns (address token0, address token1) {
  17:   function pairFor(address factory, address tokenA, address tokenB) internal pure returns (address pair) {

```


**Description:**
The above codes don't follow Solidity's standard naming convention,

internal and private functions : the mixedCase format starting with an underscore (_mixedCase starting with an underscore)


### [N-10] Floating pragma

**Description:**
Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.
https://swcregistry.io/docs/SWC-103

Floating Pragma List: 
```solidity
12 results

src/core/Factory.sol:
  2: pragma solidity ^0.8.4;

src/core/ImmutableState.sol:
  2: pragma solidity ^0.8.0;

src/core/JumpRate.sol:
  2: pragma solidity ^0.8.0;

src/core/Lendgine.sol:
  2: pragma solidity ^0.8.4;

src/core/Pair.sol:
  2: pragma solidity ^0.8.4;

src/core/libraries/Position.sol:
  2: pragma solidity ^0.8.4;

src/libraries/Balance.sol:
  2: pragma solidity ^0.8.4;

src/libraries/SafeCast.sol:
  2: pragma solidity ^0.8.0;

src/periphery/LendgineRouter.sol:
  2: pragma solidity ^0.8.4;

src/periphery/LiquidityManager.sol:
  2: pragma solidity ^0.8.4;

src/periphery/Payment.sol:
  2: pragma solidity ^0.8.4;

src/periphery/SwapHelper.sol:
  2: pragma solidity ^0.8.4;


```


**Recommendation:**
Lock the pragma version and also consider known bugs (https://github.com/ethereum/solidity/releases) for the compiler version that is chosen.

### [N-11] Project Upgrade and Stop Scenario should be

At the start of the project, the system may need to be stopped or upgraded, I suggest you have a script beforehand and add it to the documentation.
This can also be called an " EMERGENCY STOP (CIRCUIT BREAKER) PATTERN ".

https://github.com/maxwoe/solidity_patterns/blob/master/security/EmergencyStop.sol

### [N-12] Use descriptive names for Contracts and Libraries


This codebase will be difficult to navigate, as there are no descriptive naming conventions that specify which files should contain meaningful logic.

Prefixes should be added like this by filing:

Interface I_
absctract contracts Abs_
Libraries Lib_

We recommend that you implement this or a similar agreement.

### [N-13] Use SMTChecker

The *highest* tier of smart contract behavior assurance is formal mathematical verification. All assertions that are made are guaranteed to be true across all inputs → The quality of your asserts is the quality of your verification.

https://twitter.com/0xOwenThurm/status/1614359896350425088?t=dbG9gHFigBX85Rv29lOjIQ&s=19

### [N-14]  Add NatSpec Mapping comment

**Description:**
Add NatSpec comments describing mapping keys and values


**Context:**

```solidity
3 results

src/core/Factory.sol:
  39:   mapping(address => mapping(address => mapping(uint256 => mapping(uint256 => mapping(uint256 => address)))))

src/core/Lendgine.sol:
  56:   mapping(address => Position.Info) public override positions;

src/periphery/LiquidityManager.sol:
  69:   mapping(address => mapping(address => Position)) public positions;

```


### [N-15] Require messages are too short or not


**Description:**
The correct and clear error description explains to the user why the function reverts, but the error descriptions below in the project are not self-explanatory. These error descriptions are very important in the debug features of DApps like Tenderly. Error definitions should be added to the require block, not exceeding 32 bytes.


```solidity

6 results - 4 files

src/core/libraries/PositionMath.sol:
  14:       require((z = x - uint256(-y)) < x, "LS");
  16:       require((z = x + uint256(y)) >= x, "LA");

src/libraries/SafeCast.sol:
   9:     require((z = uint120(y)) == y);
  16:     require(y < 2 ** 255);

src/periphery/LendgineRouter.sol:
  215:     // swap for required token0

src/periphery/SwapHelper.sol:
  116:         require(amountOutReceived == params.amount);

```


### [N-16] Use underscores for number literals

```diff
2 results - 1 file

src/periphery/UniswapV2/libraries/UniswapV2Library.sol:
-  64:     uint256 denominator = (reserveIn * 1000) + amountInWithFee;
+ 64:     uint256 denominator = (reserveIn * 1_000) + amountInWithFee;

- 80:     uint256 numerator = reserveIn * amountOut * 1000;
+ 80:     uint256 numerator = reserveIn * amountOut * 1_000;

```
**Description:**
 There is   occasions where certain number have been hardcoded, either in variable or in the code itself. Large numbers can become hard to read.

**Recommendation:**
Consider using underscores for number literals to improve its readability.


