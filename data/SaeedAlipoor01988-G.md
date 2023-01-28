why do you need to set new values to the Parameters struct and delete it when you are passing inputs directly to line 82?

https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Factory.sol#L79
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Factory.sol#L82
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Factory.sol#L84

as I know, same as uniswapv2, you need to set new values to the Parameters struct, then from the new contract call Parameters and get values, and then delete Parameters struct values.

but I don't see any call from the lending contract to the factory contract to get Parameters struct values.
https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol

////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

In a factory contract, to create new lending, you are using the Parameters struct to set lending details and then create a new lending contract and then delete the Parameters struct.

https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Factory.sol#L79

https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Factory.sol#L84

But why don’t get new lending contract details and pass them on to the lending contract constructor? 

Lendgine newLendgine = new Lendgine (
    address token0,
    address token1,
    uint8 token0Exp,
    uint8 token1Exp,
    uint256 upperBound
)
getLendgine[token0][token1][token0Exp][token1Exp][upperBound] = address (newLendgine );

you can save gas on this transaction,

don't need Parameters struct, don't need to set Parameters struct to new values, don't need to again call Parameters struct from new lending contract to get values.

////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

In the factory contract, 
https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Factory.sol#L75

you check that token0 and token1 are not the same! but in the next line you check that token0 and token1 are not 0 address 
if (token0 == address(0) || token1 == address(0)) revert ZeroAddressError();

you don’t need to check both tokens. one token is enough.

////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

Add unchecked {} for subtractions where the operands cannot underflow because of a previous require() or if-statement

in the below line, we are checking that utilRate is more than Kink

 if (util <= kink) {}

so we can add unchecked to the below line!

https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/JumpRate.sol#L20

////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

<x> += <y> costs more gas than <x> = <x> + <y> for state variables
Using the addition operator instead of plus-equals saves 113 gas.

https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Lendgine.sol#L257