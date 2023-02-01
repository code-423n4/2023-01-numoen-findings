### Numoen Low Risk and Non Critical Issues List

### Low Risk Issue

| Number | Issues                                                                                       |
| :----: | :------------------------------------------------------------------------------------------- |
|   1.   | [zero address check](#zero-address-check)                                                    |
|   2.   | [Paramaters not defined properly in functions](#parameters-not-defined-properly-in-function) |

---

### Non Critical Issues

| Number | Issues                                                                                                    |
| :----: | :-------------------------------------------------------------------------------------------------------- |
|   3.   | [Choose Specific Compiler Version Pragma](#choose-specific-compiler-version-pragma)                       |
|   4.   | [Nat spec comment not present](#nat-spec-comment-not-present)                                             |
|   5.   | [Named return variables not used though its defined](#named-return-variables-not-used-though-its-defined) |
|   6.   | [Loss of precision due to rounding](#loss-of-precision-due-to-rounding)                                   |

---

### Low Risk Issue

1. zero address check

- Do zero address check on various functions
- https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L194

2. Parameters not defined properly in functions

- https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L104
- https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LendgineRouter.sol#L87

Recommendation:

- Define the parameters properly

---

### Non Critical Issues

3. ### Choose Specific Compiler Version Pragma

   Avoid floating pragmas for non-library contracts. While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations. A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain. It is recommended to pin to a concrete compiler version,

   e.g. 'pragma solidity ^0.8.4;' -> 'pragma solidity 0.8.17;"

4. ### Nat spec comment not present

   - There is not the presence of nat spec comments
   - Always create natspec for the functions. It will make ease in the development.

5. ### Named return variables not used though its defined

   When Named return variable are declared they should be used inside the function instead of the return statement or if not they should be removed to avoid confusion.

   - https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LendgineRouter.sol#L87

   - https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol#L25

6. ### Loss of precision due to rounding
   - Loss of precision due to rounding
   - Many functions where this problem is there
   - https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol#L40
   - https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol#L13
   - https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol#L13
