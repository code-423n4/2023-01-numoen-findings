

### [Low-01] Old pragma used
*Instances(All contract files)*
- Use a solidity version of at least 0.8.13 to get the ability to use `using for`
 with a list of free functions
- Use a solidity version of at least 0.8.12 to get `string.concat()`
 instead of `abi.encodePacked(<str>,<str>)`
- Using solidity version 0.8.17 will provide on overall gas optimization

### [NC-01] Bad Nomenclature
It inheriting ```burn()`` and there already a external function ```burn()``` defined in line 105
 
*Instances(1)*
```solidity
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L105
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L92
```

### [NC-02] Different type Pragma `solidity version` used
```solidity
>=0.5.0 used by
libraries/PositionMath.sol
```
```solidity
^0.8.4 used by
core/Factory.sol
core/Lendgine.sol
core/libraries/Position.sol
periphery/Payment.sol
periphery/LiquidityManager.sol
periphery/LendgineRouter.sol
periphery/SwapHelper.sol

```
```solidity
^0.8.0 used by
core/ImmutableState.sol
core/JumpRate.sol
```
```solidity
>=0.8.0 used by
periphery/uniswapv2/libraries/Uniswapv2Library.sol
```