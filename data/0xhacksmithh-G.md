### [Gas-01] Avoid compound assignment operator is ```STATE``` variable
*Instances(4)*
```solidity
File:  Lendgine.sol
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L91
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L114
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L176
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L275
```

### [Gas-02] Multiple address mappings can be combined into a single mapping of an addresses to a struct where appropriate
*Instances(1)*
```solidity
File:  Factory.sol
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Factory.sol#L39-L41
```

### [Gas-03] Unusable state variable
State variable ```parameters``` created and then value assigned as well inside function ```createLendgine()``` after that it was deleted without any use, 
It simply wastage of gas.
*Instances(1)*
```solidity
File:  Factory.sol
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Factory.sol#L84
```

### [Gas-04] Using solidity version 0.8.17 will provide on overall gas optimization
*Instances(All Contract Files)*

### [Gas-05] Use ```selfbalance()``` instead of ```address(this).balance```
*Instances(2)*
```solidity
File:  
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/Payment.sol#L45
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/Payment.sol#L53
```