



### [L01] Missing checks for `address(0x0)` when assigning values to `address` state variables



#### Findings:
```
2023-01-numoen/src/periphery/LendgineRouter.sol::58 => factory = _factory;
2023-01-numoen/src/periphery/LiquidityManager.sol::76 => factory = _factory;
2023-01-numoen/src/periphery/Payment.sol::18 => weth = _weth;
2023-01-numoen/src/periphery/SwapHelper.sol::30 => uniswapV2Factory = _uniswapV2Factory;
2023-01-numoen/src/periphery/SwapHelper.sol::31 => uniswapV3Factory = _uniswapV3Factory;
```




### [L02] Unspecific Compiler Version Pragma

#### Impact
Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.
#### Findings:
```
2023-01-numoen/src/core/Factory.sol::2 => pragma solidity ^0.8.4;
2023-01-numoen/src/core/ImmutableState.sol::2 => pragma solidity ^0.8.0;
2023-01-numoen/src/core/JumpRate.sol::2 => pragma solidity ^0.8.0;
2023-01-numoen/src/core/Lendgine.sol::2 => pragma solidity ^0.8.4;
2023-01-numoen/src/core/Pair.sol::2 => pragma solidity ^0.8.4;
2023-01-numoen/src/core/libraries/Position.sol::2 => pragma solidity ^0.8.4;
2023-01-numoen/src/libraries/Balance.sol::2 => pragma solidity ^0.8.4;
2023-01-numoen/src/libraries/SafeCast.sol::2 => pragma solidity ^0.8.0;
2023-01-numoen/src/periphery/LendgineRouter.sol::2 => pragma solidity ^0.8.4;
2023-01-numoen/src/periphery/LiquidityManager.sol::2 => pragma solidity ^0.8.4;
2023-01-numoen/src/periphery/Payment.sol::2 => pragma solidity ^0.8.4;
2023-01-numoen/src/periphery/SwapHelper.sol::2 => pragma solidity ^0.8.4;
```

#### Non-Critical Issues



### [N01] `require()`/`revert()` statements should have descriptive reason strings



#### Findings:
```
2023-01-numoen/src/libraries/SafeCast.sol::9 => require((z = uint120(y)) == y);
2023-01-numoen/src/libraries/SafeCast.sol::16 => require(y < 2 ** 255);
2023-01-numoen/src/periphery/SwapHelper.sol::116 => require(amountOutReceived == params.amount);
```


### [N02] Use a more recent version of solidity



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



### [N03] File is missing NatSpec


#### Findings:
```
2023-01-numoen/src/periphery/Payment.sol::1 => // SPDX-License-Identifier: GPL-2.0-or-later
```



### [N04] Use a more recent version of solidity

#### Impact
Use a more recent version of solidity
#### Findings:
```
2023-01-numoen/src/core/Lendgine.sol::18 => using Position for mapping(address => Position.Info);
2023-01-numoen/src/core/Lendgine.sol::19 => using Position for Position.Info;
```









### [N05] Numeric values having to do with time should use time units for readability

#### Impact
There are [units](https://docs.soliditylang.org/en/latest/units-and-global-variables.html#time-units) for seconds, minutes, hours, days, and weeks
#### Findings:
```
2023-01-numoen/src/core/Lendgine.sol::252 => uint256 dilutionLPRequested = (FullMath.mulDiv(borrowRate, _totalLiquidityBorrowed, 1e18) * timeElapsed) / 365 days;
```



### [N06] NC-library/interface files should use fixed compiler versions, not floating ones


#### Findings:
```
2023-01-numoen/src/core/Factory.sol::2 => pragma solidity ^0.8.4;
2023-01-numoen/src/core/ImmutableState.sol::2 => pragma solidity ^0.8.0;
2023-01-numoen/src/core/JumpRate.sol::2 => pragma solidity ^0.8.0;
2023-01-numoen/src/core/Lendgine.sol::2 => pragma solidity ^0.8.4;
2023-01-numoen/src/core/Pair.sol::2 => pragma solidity ^0.8.4;
2023-01-numoen/src/core/libraries/Position.sol::2 => pragma solidity ^0.8.4;
2023-01-numoen/src/libraries/Balance.sol::2 => pragma solidity ^0.8.4;
2023-01-numoen/src/libraries/SafeCast.sol::2 => pragma solidity ^0.8.0;
2023-01-numoen/src/periphery/LendgineRouter.sol::2 => pragma solidity ^0.8.4;
2023-01-numoen/src/periphery/LiquidityManager.sol::2 => pragma solidity ^0.8.4;
2023-01-numoen/src/periphery/Payment.sol::2 => pragma solidity ^0.8.4;
2023-01-numoen/src/periphery/SwapHelper.sol::2 => pragma solidity ^0.8.4;
```






