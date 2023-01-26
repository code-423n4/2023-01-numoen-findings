Avoid compound assignment operator in state variables
====================================

Using compound assignment operator for state variables such as (`State +=X` or `State -=X`) its more expensive then using operator assignment like(`State = State + X` or `State = State - X`)

POC:

```
pragma solidity ^0.8.4;
contract TesterA {
    uint private _a;
    function testShort() public {
        _a += 1;
    }
}
contract TesterB {
    uint private _a;
    function testLong() public {
        _a = _a + 1;
    }
}
```
According to the POC above the below are the gas cost if ran in remix:
```
TesterA.testShort() = 119153 gas
TesterB.testLong() = 118146 gas

```
Gas Saved = 119153 - 118145 = 1007 gas per entry

Affected Code:
* https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L91
* https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L257
* https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L114
* https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L176
