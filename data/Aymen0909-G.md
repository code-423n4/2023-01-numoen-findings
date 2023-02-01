# Gas Optimizations

## Summary

|               | Issue         | Instances     |
| :-------------: |:-------------|:-------------:|
| 1  | Usage of `uint/int` smaller than 32 bytes (256 bits) cost more gas  |  2 |
| 2  | Using `storage` instead of `memory` for struct/array saves gas |  1 |
| 3  | Rearrange the Position's `update` function to save gas  | 1|
| 4  | Use `unchecked` blocks to save gas  |  4 |
| 5  | `x += y/x -= y` costs more gas than `x = x + y/x = x - y` for state variables  |  4 |
| 6  | `public` functions not called by the contract should be declared `external` instead |  2 |


## Findings

### 1- Usage of `uint/int` smaller than 32 bytes (256 bits) cost more gas :

State variable declared as `uint/int` smaller than 32 bytes (256 bits) will cost more gas when used in the code logic as the compiler must first convert them back to `uint256` before using them, the only case where using smaller `uint/int` size matter is when you store many state variables in the same storage slot otherwise it's better to declare them as `uint256` to save gas in functions calls.

There are 2 instances of this issue :

File: core/Pair.sol [Line 40](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Pair.sol#L40)

```
uint120 public override reserve0;
```

File: core/Pair.sol [Line 43](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Pair.sol#L43)

```
uint120 public override reserve1;
```

### 2- Using `storage` instead of `memory` for struct/array saves gas :

When fetching data from a `storage` location, assigning the data to a `memory` variable causes all fields of the struct/array to be read from `storage`, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new `memory` variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declearing the variable with the `memory` keyword, declaring the variable with the `storage` keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a `memory` variable, is if the full struct/array is being returned by the function, is being passed to a function that requires `memory`, or if the array/struct is being read from another `memory` array/struct

There is 1 instance of this issue :

File: core/Lendgine.sol [Line 167](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L167)
```
Position.Info memory positionInfo = positions[msg.sender];
```

### 3- Rearrange the Position's `update` function to save gas :

The [update function](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/libraries/Position.sol#L38-L65) function in the Position contract contains many unecessary lines of code which cost more gas and can lead to stack too deep errors, thus the function should be optimized to save gas on each call and should be modified as follow :

```
function update(
    mapping(address => Position.Info) storage self,
    address owner,
    int256 sizeDelta,
    uint256 rewardPerPosition
  )
    internal
  {
    Position.Info storage positionInfo = self[owner];
    Position.Info memory _positionInfo = positionInfo;

    // revert immediately if no position  
    if (sizeDelta == 0 && _positionInfo.size == 0) revert NoPositionError();

    uint256 tokensOwed;
    if (_positionInfo.size > 0) {
      tokensOwed = newTokensOwed(_positionInfo, rewardPerPosition);
    }

    // update position size only when there is delta != 0
    if (sizeDelta != 0) positionInfo.size = PositionMath.addDelta(_positionInfo.size, sizeDelta);
    positionInfo.rewardPerPositionPaid = rewardPerPosition;
    if (tokensOwed > 0) positionInfo.tokensOwed = _positionInfo.tokensOwed + tokensOwed;
  }
```


### 4- Use `unchecked` blocks to save gas :

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block.

There are 4 instances of this issue:

File: core/JumpRate.sol [Line 20](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol#L20)
```
uint256 excessUtil = util - kink;
```

The above operation cannot underflow due to the check at [Line 16](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol#L16) so it should be marked as `unchecked`. 

File: core/Lendgine.sol [Line 201](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L201)
```
position.tokensOwed = tokensOwed - collateral;
```

The above operation cannot underflow because of the [Line 198](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L198) which ensures that we always have `tokensOwed >= collateral` , so it should be marked as `unchecked`. 


File: core/Lendgine.sol [Line 256](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L256)
```
totalLiquidityBorrowed = _totalLiquidityBorrowed - dilutionLP;
```

The above operation cannot underflow because of the [Line 253](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L253) which ensures that we always have `_totalLiquidityBorrowed >= dilutionLP` , so it should be marked as `unchecked`. 

File: periphery/LiquidityManager.sol [Line 242](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L242)
```
position.tokensOwed -= amount;
```

The above operation cannot underflow because of the [Line 241](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L241) which ensures that we always have `position.tokensOwed >= amount` , so it should be marked as `unchecked`. 

### 5- `x += y/x -= y` costs more gas than `x = x + y/x = x - y` for state variables :

Using the addition operator instead of plus-equals saves **113 gas** as explained [here](https://gist.github.com/IllIllI000/cbbfb267425b898e5be734d4008d4fe8)

There are 4 instances of this issue:

File: core/Lendgine.sol [Line 91](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L91)
```
totalLiquidityBorrowed += liquidity;
```

File: core/Lendgine.sol [Line 114](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L114)
```
totalLiquidityBorrowed -= liquidity;
```

File: core/Lendgine.sol [Line 176](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L176)
```
totalPositionSize -= size;
```

File: core/Lendgine.sol [Line 257](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L257)
```
rewardPerPositionStored += FullMath.mulDiv(dilutionSpeculative, 1e18, totalPositionSize);
```

### 6- `public` functions not called by the contract should be declared `external` instead :

The `public` functions that are not called inside the contract should be declared `external` instead to save gas.

There are 2 instances of this issue:

File: periphery/Payment.sol [Line 25](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/Payment.sol#L25)
```
function unwrapWETH(uint256 amountMinimum, address recipient) public
```

File: periphery/Payment.sol [Line 35](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/Payment.sol#L35)
```
function sweepToken(address token, uint256 amountMinimum, address recipient) public
```