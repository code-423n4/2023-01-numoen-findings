### 1. USE MORE RECENT VERSION OF SOLIDITY

Old version of solidity is used (^0.8.0) and (^0.8.4), but newer version 0.8.17 can be used.

For security, it is recommended to use the latest solidity version.
security fix list for different solidity versions can be found in the following link.

https://github.com/ethereum/solidity/blob/develop/Changelog.md

Above issue is found in all contracts.

### 2. NATSPEC IS MISSING

NatSpec is missing for all the contracts, functions, constructors and modifiers given in the project. 

It is recommended to use the NatSpec for commenting for better readability and understanding of the code base.
	
### 3. CONSTANTS SHOULD BE DECLARED IN UPPERCASE LETTERS FOR BETTER READABILITY OF THE CODE.

    string public constant name = "Numoen Replicating Derivative";

    string public constant symbol = "NRD";

    uint8 public constant decimals = 18;
  
Above constants should be declared as:

    string public constant NAME = "Numoen Replicating Derivative";

    string public constant SYMBOL = "NRD";

    uint8 public constant DECIMALS = 18;
  
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/ERC20.sol#L23-L27

There is 1 more instance of this issue

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol#L7-L11

### 4. USING WRONG VARIABLE NAMES COULD TRIGGER COMPILTER TIME ERROR

`parmaeters` is declared as a variable inside the `Factory.sol` smart contract. It should be changed as `parameters` instead.

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Factory.sol#L79 
