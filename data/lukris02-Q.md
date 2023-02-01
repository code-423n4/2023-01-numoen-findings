# QA Report for Numoen contest
## Overview
During the audit, 6 non-critical issues were found.

№ | Title | Risk Rating  | Instance Count
--- | --- | --- | ---
NC-1 | Order of Functions | Non-Critical | 1
NC-2 | Order of Layout | Non-Critical | 1
NC-3 | Typos | Non-Critical | 1
NC-4 | Unused named return variables | Non-Critical | 3
NC-5 | Missing leading underscores  | Non-Critical | 5
NC-6 | Maximum line length exceeded | Non-Critical | 4

## Non-Critical Risk Findings(6)
### NC-1. Order of Functions
##### Description
According to [Style Guide](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#order-of-functions), ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier.  
Functions should be grouped according to their visibility and ordered:
1) constructor
2) receive function (if exists)
3) fallback function (if exists)
4) external
5) public
6) internal
7) private
##### Instances
Public function should be placed after external:
- https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol#L13-L23

##### Recommendation
Reorder functions.
#
### NC-2. Order of Layout
##### Description
According to [Order of Layout](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#order-of-layout), inside each contract, library or interface, use the following order:
1) Type declarations
2) State variables
3) Events
4) Modifiers
5) Functions
##### Instances
- https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/SwapHelper.sol#L49-L63 

##### Recommendation
Place enum and struct definitions before constructor.
#
### NC-3. Typos
##### Instances
- [```/// @notice Collects interest owed to the callers liqudity position```](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L229) => ```liquidity```

#
### NC-4. Unused named return variables
##### Description
Both named return variable(s) and return statement are used.
##### Instances
- https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol#L21
- https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol#L37
- https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol#L42

##### Recommendation
To improve clarity use only named return variables.  
For example, change:
```
function functionName() returns (uint id) {
    return x;
```
to
```
function functionName() returns (uint id) {
    id = x;
```
#
### NC-5. Missing leading underscores
##### Description
Internal constants and functions should have a leading underscore.
##### Instances
- https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/Payment.sol#L52
- https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/SwapHelper.sol#L69
- https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Pair.sol#L70
- https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Pair.sol#L93
- https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/libraries/LendgineAddress.sol#L6

##### Recommendation
Add leading underscores.
#
### NC-6. Maximum line length exceeded
##### Description
According to [Style Guide](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#maximum-line-length), maximum suggested line length is 120 characters.  Longer lines make the code harder to read.
##### Instances
- https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol#L13
- https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L194
- https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Pair.sol#L116
- https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/SwapHelper.sol#L69

##### Recommendation
Make the lines shorter.