## GAS-1: <X> += <Y> costs more gas than <X> = <X> + <Y> for state variables

### Description

Using the addition operator instead of plus-equals saves gas.

### Affected file

* Lendgine.sol (Line: 91, 114, 176, 257)

## GAS-2: Internal functions not called by the contract should be removed to save deployment gas

### Description

If the functions are required by an interface, the contract should inherit from that interface and use the override keyword.

### Affected file

* Balance.sol (Line: 12)
* LendgineAddress.sol (Line: 9)
* Position.sol (Line: 38, 73, 86)
* PositionMath.sol (Line: 12)
* SafeCast.sol (Line: 8, 15)
* UniswapV2Library.sol (Line: 51, 69)

## GAS-3: Internal functions only called once can be inlined to save gas

### Description

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

### Affected file

* Position.sol (Line: 69)
* UniswapV2Library.sol (Line: 17, 36)

## GAS-4: Replace modifier with function

### Description

Modifiers make code more elegant, but cost more than normal functions.

### Affected file

* LendgineRouter.sol (Line: 65)
* LiquidityManager.sol (Line: 83)

## GAS-5: Require()/Revert() strings longer than 32 bytes cost extra gas

### Description

Each extra memory word of bytes past the original 32 incurs an MSTORE which costs 3 gas.

### Affected file

* UniswapV2Library.sol (Line: 11, 60, 61, 78, 79)

## GAS-6: Splitting require() statements that use && saves gas

### Description

Saves 16 gas per instance. If you’re using the Optimizer at 200, instead of using the && operator in a single require statement to check multiple conditions, multiple require statements with 1 condition per require statement should be used to save gas.

### Affected file

* UniswapV2Library.sol (Line: 61, 79)

## GAS-7: Storage pointer to a structure is cheaper than copying each value of the structure into memory, same for array and mapping

### Description

It may not be obvious, but every time you copy a storage struct/array/mapping to a memory variable, you are literally copying each member by reading it from storage, which is expensive. And when you use the storage keyword, you are just storing a pointer to the storage, which is much cheaper.

### Affected file

* Lendgine.sol (Line: 167)

## GAS-9: Use assembly to check for address(0)

### Description

Saves 6 gas per instance if using assembly to check for address(0).

### Remediation

```
assembly {
 if iszero(_addr) {
  mstore(0x00, "zero address")
  revert(0x00, 0x20)
 }
}
```

### Affected file

* UniswapV2Library.sol (Line: 13)

## GAS-10: Use custom errors rather than revert()/require() strings to save gas

### Description

Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they’re hitby avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas.

### Affected file

* UniswapV2Library.sol (Line: 11, 13, 60, 61, 78, 79)

## GAS-11: Using > 0 costs more gas than != 0 when used on a uint in a require() statement

### Description

When dealing with unsigned integer types, comparisons with != 0 are cheaper then with > 0. This change saves 6 gas per instance.

### Affected file

* UniswapV2Library.sol (Line: 60, 61, 61, 78, 79, 79)

## GAS-12: Using Solidity version 0.8.17 will provide an overall gas optimization

### Description

Using at least 0.8.10 will save gas due to skipped extcodesize check if there is a return value.

### Affected file

* Balance.sol (Line: 2)
* Factory.sol (Line: 2)
* ImmutableState.sol (Line: 2)
* JumpRate.sol (Line: 2)
* Lendgine.sol (Line: 2)
* LendgineRouter.sol (Line: 2)
* LiquidityManager.sol (Line: 2)
* Pair.sol (Line: 2)
* Payment.sol (Line: 2)
* Position.sol (Line: 2)
* SafeCast.sol (Line: 2)
* SwapHelper.sol (Line: 2)
* UniswapV2Library.sol (Line: 1)

## GAS-13: Using private rather than public for constants saves gas

### Description

If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it’s used, and not adding another entry to the method ID table.

### Affected file

* LendgineAddress.sol (Line: 6)

## GAS-14: abi.encode() is less efficient than abi.encodePacked()

### Description

Use abi.encodePacked() where possible to save gas.

### Affected file

* Factory.sol (Line: 82)
* LendgineAddress.sol (Line: 28)
* LendgineRouter.sol (Line: 150, 268)
* LiquidityManager.sol (Line: 160)