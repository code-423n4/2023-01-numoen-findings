# [L-01] Nested mapping can be changed to mapping structs

## Proof of Concept

In https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Factory.sol#L39 there is a nested mapping that basically represents the components of the struct:

    struct Parameters {
    address token0;
    address token1;
    uint128 token0Exp;
    uint128 token1Exp;
    uint256 upperBound;
    }  


## Recommendation

You could shorten it by doing:

mapping(address => Parameters);




# [L-02] Decimal incompatibility

## Proof of Concept

While the protocol is compatible with most of the tokens, it does not support tokens that are more than 18 decimals, which is quite common with different tokens. So they are eventually losing users/costumers by not supporting those tokens.

https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Factory.sol#L77


## Recommendation
Add support for at least tokens > 18 decimals because < 6 it is quite rare, but it could also be a good decision



# [L-03] Unused params

## Proof of Concept

In lines: 
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Factory.sol#L79c  and 
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Factory.sol#L84

parameters it is not used for absolutely nothing. It is a waste of gas


## Recommendation

Remove the parameters variable from inside the  createLendgine function if not needed

# [L-02] Decimal incompatibility

## Proof of Concept

While the protocol is compatible with most of the tokens, it does not support tokens that are more than 18 decimals, which is quite common with different tokens. So they are eventually losing users/costumers by not supporting those tokens.

https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Factory.sol#L77


## Recommendation
Add support for at least tokens > 18 decimals because < 6 it is quite rare, but it could also be a good decision



# [L-04] Uncheked return of create

## Proof of Concept

In lines: 
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Factory.sol#L82  it is being deployed a new instance of a contract. In solidity, while creating a new instance, solidity uses the opcode create, which returns 0 if the contract already exists.  In this case, it has no high impact but it should be checked.

parameters it is not used for absolutely nothing. It is a waste of gas


## Recommendation

Check that the variable  `lendgine` != address(0);