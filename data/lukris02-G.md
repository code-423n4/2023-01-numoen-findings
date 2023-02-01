# Gas Optimizations Report for Numoen contest
## Overview
During the audit, 2 gas issues were found.  

№ | Title | Instance Count
--- | --- | --- 
G-1 | Use unchecked blocks for subtractions where underflow is impossible | 2 
G-2 | Using ```storage``` pointer to ```struct```/```array```/```mapping``` is cheaper than using ```memory``` | 4 

## Gas Optimizations Findings(2)
### G-1. Use unchecked blocks for subtractions where underflow is impossible
##### Description
In Solidity 0.8+, there’s a default overflow and underflow check on unsigned integers. When an overflow or underflow isn’t possible (after require or if-statement), some gas can be saved by using [unchecked blocks](https://docs.soliditylang.org/en/v0.8.17/control-structures.html#checked-or-unchecked-arithmetic).
##### Instances
- https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L201
- https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L256

#
### G-2. Using ```storage``` pointer to ```struct```/```array```/```mapping``` is cheaper than using ```memory```
##### Description
When you copy a storage ```struct```/```array```/```mapping``` to a memory variable, you are copying each member by reading it from the storage, which is expensive, but when you use the ```storage``` keyword, you are just storing a pointer to the storage, which is much cheaper.
##### Instances
- https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L167
- https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L175
- https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L211
- https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L235

##### Recommendation
For example, change: 
```
Position.Info memory positionInfo = positions[msg.sender];
```  
to:  
```
Position.Info storage positionInfo = positions[msg.sender];
```