### Floating Pragma
Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

Lock the pragma version and also consider [known bugs](https://github.com/ethereum/solidity/releases) for the compiler version that is chosen.

Pragma statements can be allowed to float when a contract is intended for consumption by other developers, as in the case with contracts in a library or EthPM package. Otherwise, the developer would need to manually update the pragma in order to compile locally.

*Instances (7):*
```
File: src/core/ERC20.sol
2:    pragma solidity >=0.8.0;
```
[Link to code](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/ERC20.sol)
```
File: src/core/Factory.sol
2:    pragma solidity ^0.8.4;
```
[Link to code](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Factory.sol)
```
File: src/core/ImmutableState.sol
2:    pragma solidity ^0.8.0;
```
[Link to code](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/ImmutableState.sol)
```
File: src/core/JumpRate.sol
2:    pragma solidity ^0.8.0;
```
[Link to code](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol)
```
File: src/core/Lendgine.sol
2:    pragma solidity ^0.8.4;
```
[Link to code](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol)
```
File: src/core/Pair.sol
2:    pragma solidity ^0.8.4;
```
[Link to code](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Pair.sol)
```
File: src/core/ReentrancyGuard.sol
2:    pragma solidity >=0.8.0;
```
[Link to code](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/ReentrancyGuard.sol)

*Recommendation:*
Consider locking pragma to a specific version (0.8.4).