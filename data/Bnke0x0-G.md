
### [G01] State variables only set in the constructor should be declared `immutable`

#### Impact
Avoids a Gusset (20000 gas)
#### Findings:
```
2023-01-numoen/src/core/Factory.sol::56 => Parameters public override parameters;
2023-01-numoen/src/core/Lendgine.sol::59 => uint256 public override totalPositionSize;
2023-01-numoen/src/core/Lendgine.sol::62 => uint256 public override totalLiquidityBorrowed;
2023-01-numoen/src/core/Lendgine.sol::65 => uint256 public override rewardPerPositionStored;
2023-01-numoen/src/core/Lendgine.sol::68 => uint256 public override lastUpdate;
2023-01-numoen/src/core/Pair.sol::40 => uint120 public override reserve0;
2023-01-numoen/src/core/Pair.sol::43 => uint120 public override reserve1;
2023-01-numoen/src/core/Pair.sol::46 => uint256 public override totalLiquidity;
```





### [G02] `require()`/`revert()` strings longer than 32 bytes cost extra gas



#### Findings:
```
2023-01-numoen/src/periphery/UniswapV2/libraries/UniswapV2Library.sol::11 => require(tokenA != tokenB, "UniswapV2Library: IDENTICAL_ADDRESSES");
2023-01-numoen/src/periphery/UniswapV2/libraries/UniswapV2Library.sol::60 => require(amountIn > 0, "UniswapV2Library: INSUFFICIENT_INPUT_AMOUNT");
2023-01-numoen/src/periphery/UniswapV2/libraries/UniswapV2Library.sol::61 => require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");
2023-01-numoen/src/periphery/UniswapV2/libraries/UniswapV2Library.sol::78 => require(amountOut > 0, "UniswapV2Library: INSUFFICIENT_OUTPUT_AMOUNT");
2023-01-numoen/src/periphery/UniswapV2/libraries/UniswapV2Library.sol::79 => require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");
```



### [G03] Using `> 0` costs more gas than `!= 0` when used on a `uint` in a `require()` statement


#### Findings:
```
2023-01-numoen/src/periphery/UniswapV2/libraries/UniswapV2Library.sol::60 => require(amountIn > 0, "UniswapV2Library: INSUFFICIENT_INPUT_AMOUNT");
2023-01-numoen/src/periphery/UniswapV2/libraries/UniswapV2Library.sol::61 => require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");
2023-01-numoen/src/periphery/UniswapV2/libraries/UniswapV2Library.sol::78 => require(amountOut > 0, "UniswapV2Library: INSUFFICIENT_OUTPUT_AMOUNT");
2023-01-numoen/src/periphery/UniswapV2/libraries/UniswapV2Library.sol::79 => require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");
```


### [G04] Splitting `require()` statements that use `&&` Cost gas



#### Findings:
```
2023-01-numoen/src/periphery/UniswapV2/libraries/UniswapV2Library.sol::61 => require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");
2023-01-numoen/src/periphery/UniswapV2/libraries/UniswapV2Library.sol::79 => require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");
```




### [G05] Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead

#### Impact
> When using elements that are smaller than 32 bytes, your 
contract’s gas usage may be higher. This is because the EVM operates on 
32 bytes at a time. Therefore, if the element is smaller than that, the 
EVM must use more operations in order to reduce the size of the element 
from 32 bytes to the desired size.
> 

[https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html)
Use a larger size then downcast where needed
#### Findings:
```
2023-01-numoen/src/core/Factory.sol::50 => uint128 token0Exp;
2023-01-numoen/src/core/Factory.sol::51 => uint128 token1Exp;
2023-01-numoen/src/core/Factory.sol::66 => uint8 token0Exp,
2023-01-numoen/src/core/Factory.sol::67 => uint8 token1Exp,
2023-01-numoen/src/core/ImmutableState.sol::30 => uint128 _token0Exp;
2023-01-numoen/src/core/ImmutableState.sol::31 => uint128 _token1Exp;
2023-01-numoen/src/core/Pair.sol::40 => uint120 public override reserve0;
2023-01-numoen/src/core/Pair.sol::43 => uint120 public override reserve1;
2023-01-numoen/src/core/Pair.sol::71 => uint120 _reserve0 = reserve0; // SLOAD
2023-01-numoen/src/core/Pair.sol::72 => uint120 _reserve1 = reserve1; // SLOAD
2023-01-numoen/src/core/Pair.sol::94 => uint120 _reserve0 = reserve0; // SLOAD
2023-01-numoen/src/core/Pair.sol::95 => uint120 _reserve1 = reserve1; // SLOAD
2023-01-numoen/src/core/Pair.sol::119 => uint120 _reserve0 = reserve0; // SLOAD
2023-01-numoen/src/core/Pair.sol::120 => uint120 _reserve1 = reserve1; // SLOAD
2023-01-numoen/src/periphery/SwapHelper.sol::62 => uint24 fee;
2023-01-numoen/src/periphery/UniswapV2/libraries/UniswapV2Library.sol::20 => uint160( // extra cast for newer solidity
2023-01-numoen/src/periphery/libraries/LendgineAddress.sol::22 => uint160(
```




### [G06] Use custom errors rather than `revert()`/`require()` strings to save deployment gas


#### Findings:
```
2023-01-numoen/src/core/libraries/PositionMath.sol::14 => require((z = x - uint256(-y)) < x, "LS");
2023-01-numoen/src/core/libraries/PositionMath.sol::16 => require((z = x + uint256(y)) >= x, "LA");
2023-01-numoen/src/periphery/Payment.sol::22 => require(msg.sender == weth, "Not WETH9");
2023-01-numoen/src/periphery/UniswapV2/libraries/UniswapV2Library.sol::11 => require(tokenA != tokenB, "UniswapV2Library: IDENTICAL_ADDRESSES");
2023-01-numoen/src/periphery/UniswapV2/libraries/UniswapV2Library.sol::13 => require(token0 != address(0), "UniswapV2Library: ZERO_ADDRESS");
2023-01-numoen/src/periphery/UniswapV2/libraries/UniswapV2Library.sol::60 => require(amountIn > 0, "UniswapV2Library: INSUFFICIENT_INPUT_AMOUNT");
2023-01-numoen/src/periphery/UniswapV2/libraries/UniswapV2Library.sol::61 => require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");
2023-01-numoen/src/periphery/UniswapV2/libraries/UniswapV2Library.sol::78 => require(amountOut > 0, "UniswapV2Library: INSUFFICIENT_OUTPUT_AMOUNT");
2023-01-numoen/src/periphery/UniswapV2/libraries/UniswapV2Library.sol::79 => require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");
```




###  [G07] Use a more recent version of solidity

#### Impact
Use a solidity version of at least 0.8.10 to have external calls skip
 contract existence checks if the external call has a return value
#### Findings:
```
2023-01-numoen/src/core/Factory.sol::2 => pragma solidity ^0.8.4;
2023-01-numoen/src/core/ImmutableState.sol::2 => pragma solidity ^0.8.0;
2023-01-numoen/src/core/JumpRate.sol::2 => pragma solidity ^0.8.0;
2023-01-numoen/src/core/Lendgine.sol::2 => pragma solidity ^0.8.4;
2023-01-numoen/src/core/Pair.sol::2 => pragma solidity ^0.8.4;
2023-01-numoen/src/core/libraries/Position.sol::2 => pragma solidity ^0.8.4;
2023-01-numoen/src/core/libraries/PositionMath.sol::2 => pragma solidity >=0.5.0;
2023-01-numoen/src/libraries/Balance.sol::2 => pragma solidity ^0.8.4;
2023-01-numoen/src/libraries/SafeCast.sol::2 => pragma solidity ^0.8.0;
2023-01-numoen/src/periphery/LendgineRouter.sol::2 => pragma solidity ^0.8.4;
2023-01-numoen/src/periphery/LiquidityManager.sol::2 => pragma solidity ^0.8.4;
2023-01-numoen/src/periphery/Payment.sol::2 => pragma solidity ^0.8.4;
2023-01-numoen/src/periphery/SwapHelper.sol::2 => pragma solidity ^0.8.4;
2023-01-numoen/src/periphery/UniswapV2/libraries/UniswapV2Library.sol::1 => pragma solidity >=0.8.0;
2023-01-numoen/src/periphery/libraries/LendgineAddress.sol::2 => pragma solidity >=0.5.0;
```





### [G08] Using `private` rather than `public` for constants, saves gas

#### Impact
If needed, the value can be read from the verified contract source 
code. Savings are due to the compiler not having to create non-payable getter functions for deployment call data, and not adding another entry to the method ID table
#### Findings:
```
2023-01-numoen/src/core/JumpRate.sol::7 => uint256 public constant override kink = 0.8 ether;
2023-01-numoen/src/core/JumpRate.sol::9 => uint256 public constant override multiplier = 1.375 ether;
2023-01-numoen/src/core/JumpRate.sol::11 => uint256 public constant override jumpMultiplier = 44.5 ether;
```





### [G09] `abi.encode()` is less efficient than abi.encodePacked()


#### Findings:
```
2023-01-numoen/src/core/Factory.sol::82 => lendgine = address(new Lendgine{ salt: keccak256(abi.encode(token0, token1, token0Exp, token1Exp, upperBound)) }());
2023-01-numoen/src/periphery/LendgineRouter.sol::150 => abi.encode(
2023-01-numoen/src/periphery/LendgineRouter.sol::268 => abi.encode(
2023-01-numoen/src/periphery/LiquidityManager.sol::160 => abi.encode(
2023-01-numoen/src/periphery/SwapHelper.sol::108 => abi.encode(params.tokenIn)
2023-01-numoen/src/periphery/libraries/LendgineAddress.sol::28 => keccak256(abi.encode(token0, token1, token0Exp, token1Exp, upperBound)),
```







