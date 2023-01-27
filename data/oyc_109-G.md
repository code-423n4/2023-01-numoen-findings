## [G-01] Use Shift Right/Left instead of Division/Multiplication if possible

A division/multiplication by any number x being a power of 2 can be calculated by shifting log2(x) to the right/left.

While the DIV opcode uses 5 gas, the SHR opcode only uses 3 gas. Furthermore, Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting.

```
2023-01-numoen/src/core/Pair.sol::63 => uint256 c = (scale1 * scale1) / 4;
```

## [G-02] Use calldata instead of memory

Use calldata instead of memory for function parameters saves gas if the function argument is only read.

```
2023-01-numoen/src/core/libraries/Position.sol::69 => function newTokensOwed(Position.Info memory position, uint256 rewardPerPosition) internal pure returns (uint256) {
```

## [G-03] Using private rather than public for constants, saves gas

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table

```
2023-01-numoen/src/core/ImmutableState.sol::10 => address public immutable override factory;
2023-01-numoen/src/core/ImmutableState.sol::13 => address public immutable override token0;
2023-01-numoen/src/core/ImmutableState.sol::16 => address public immutable override token1;
2023-01-numoen/src/core/ImmutableState.sol::19 => uint256 public immutable override token0Scale;
2023-01-numoen/src/core/ImmutableState.sol::22 => uint256 public immutable override token1Scale;
2023-01-numoen/src/core/ImmutableState.sol::25 => uint256 public immutable override upperBound;
```

## [G-04] Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

```
2023-01-numoen/src/core/ERC20.sol::27 => uint8 public constant decimals = 18;
2023-01-numoen/src/core/Factory.sol::50 => uint128 token0Exp;
2023-01-numoen/src/core/Factory.sol::51 => uint128 token1Exp;
2023-01-numoen/src/core/ImmutableState.sol::30 => uint128 _token0Exp;
2023-01-numoen/src/core/ImmutableState.sol::31 => uint128 _token1Exp;
2023-01-numoen/src/core/ReentrancyGuard.sol::9 => uint16 private locked = 1;
```

## [G-05] ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, for example when used in for- and while-loops

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop

```
2023-01-numoen/src/core/ERC20.sol::133 => keccak256(abi.encode(PERMIT_TYPEHASH, owner, spender, value, nonces[owner]++, deadline))
```

## [G-06] <x> += <y> costs more gas than <x> = <x> + <y> for state variables

use <x> = <x> + <y> or <x> = <x> - <y> instead to save gas

```
2023-01-numoen/src/core/ERC20.sol::76 => balanceOf[msg.sender] -= amount;
2023-01-numoen/src/core/ERC20.sol::81 => balanceOf[to] += amount;
2023-01-numoen/src/core/ERC20.sol::95 => balanceOf[from] -= amount;
2023-01-numoen/src/core/ERC20.sol::100 => balanceOf[to] += amount;
2023-01-numoen/src/core/ERC20.sol::168 => totalSupply += amount;
2023-01-numoen/src/core/ERC20.sol::173 => balanceOf[to] += amount;
2023-01-numoen/src/core/ERC20.sol::180 => balanceOf[from] -= amount;
2023-01-numoen/src/core/ERC20.sol::185 => totalSupply -= amount;
2023-01-numoen/src/core/Lendgine.sol::91 => totalLiquidityBorrowed += liquidity;
2023-01-numoen/src/core/Lendgine.sol::114 => totalLiquidityBorrowed -= liquidity;
2023-01-numoen/src/core/Lendgine.sol::176 => totalPositionSize -= size;
2023-01-numoen/src/core/Lendgine.sol::257 => rewardPerPositionStored += FullMath.mulDiv(dilutionSpeculative, 1e18, totalPositionSize);
```

## [G-07] abi.encode() is less efficient than abi.encodePacked()

use abi.encodePacked() where possible to save gas

```
2023-01-numoen/src/core/ERC20.sol::133 => keccak256(abi.encode(PERMIT_TYPEHASH, owner, spender, value, nonces[owner]++, deadline))
2023-01-numoen/src/core/ERC20.sol::153 => abi.encode(
2023-01-numoen/src/core/Factory.sol::82 => lendgine = address(new Lendgine{ salt: keccak256(abi.encode(token0, token1, token0Exp, token1Exp, upperBound)) }());
```

## [G-08] Use custom errors rather than revert()/require() strings to save gas

Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they're hitby avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas

```
2023-01-numoen/src/core/ERC20.sol::124 => require(deadline >= block.timestamp, "PERMIT_DEADLINE_EXPIRED");
2023-01-numoen/src/core/ERC20.sol::139 => require(recoveredAddress != address(0) && recoveredAddress == owner, "INVALID_SIGNER");
2023-01-numoen/src/core/ReentrancyGuard.sol::12 => require(locked == 1, "REENTRANCY");
2023-01-numoen/src/core/libraries/PositionMath.sol::14 => require((z = x - uint256(-y)) < x, "LS");
2023-01-numoen/src/core/libraries/PositionMath.sol::16 => require((z = x + uint256(y)) >= x, "LA");
```

## [G-09] Use a more recent version of solidity

Use a solidity version of at least 0.8.0 to get overflow protection without SafeMath
Use a solidity version of at least 0.8.2 to get compiler automatic inlining
Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

```
2023-01-numoen/src/core/ERC20.sol::2 => pragma solidity >=0.8.0;
2023-01-numoen/src/core/Factory.sol::2 => pragma solidity ^0.8.4;
2023-01-numoen/src/core/ImmutableState.sol::2 => pragma solidity ^0.8.0;
2023-01-numoen/src/core/JumpRate.sol::2 => pragma solidity ^0.8.0;
2023-01-numoen/src/core/Lendgine.sol::2 => pragma solidity ^0.8.4;
2023-01-numoen/src/core/Pair.sol::2 => pragma solidity ^0.8.4;
2023-01-numoen/src/core/ReentrancyGuard.sol::2 => pragma solidity >=0.8.0;
2023-01-numoen/src/core/interfaces/IFactory.sol::2 => pragma solidity >=0.5.0;
2023-01-numoen/src/core/interfaces/IImmutableState.sol::2 => pragma solidity >=0.5.0;
2023-01-numoen/src/core/interfaces/IJumpRate.sol::2 => pragma solidity >=0.5.0;
2023-01-numoen/src/core/interfaces/ILendgine.sol::2 => pragma solidity >=0.5.0;
2023-01-numoen/src/core/interfaces/IPair.sol::2 => pragma solidity >=0.5.0;
2023-01-numoen/src/core/interfaces/callback/IMintCallback.sol::2 => pragma solidity >=0.5.0;
2023-01-numoen/src/core/interfaces/callback/IPairMintCallback.sol::2 => pragma solidity >=0.5.0;
2023-01-numoen/src/core/interfaces/callback/ISwapCallback.sol::2 => pragma solidity >=0.5.0;
2023-01-numoen/src/core/libraries/Position.sol::2 => pragma solidity ^0.8.4;
2023-01-numoen/src/core/libraries/PositionMath.sol::2 => pragma solidity >=0.5.0;
```

## [G-10] Use bytes32 instead of string

Use bytes32 instead of string to save gas whenever possible. String is a dynamic data structure and therefore is more gas consuming then bytes32.

```
2023-01-numoen/src/core/ERC20.sol::23 => string public constant name = "Numoen Replicating Derivative";
2023-01-numoen/src/core/ERC20.sol::25 => string public constant symbol = "NRD";
```

## [G-11] Splitting require() statements that use && saves gas

Saves 16 gas per instance.
If you're using the Optimizer at 200, instead of using the && operator in a single require statement to check multiple conditions, multiple require statements with 1 condition per require statement should be used to save gas:

```
2023-01-numoen/src/core/ERC20.sol::139 => require(recoveredAddress != address(0) && recoveredAddress == owner, "INVALID_SIGNER");
```

## [G-12] Not using the named return variables when a function returns, wastes deployment gas

It is not necessary to have both a named return and a return statement.

```
2023-01-numoen/src/core/JumpRate.sol::13 => function getBorrowRate(uint256 borrowedLiquidity, uint256 totalLiquidity) public pure override returns (uint256 rate) {
2023-01-numoen/src/core/JumpRate.sol::32 => returns (uint256 rate)
2023-01-numoen/src/core/JumpRate.sol::40 => function utilizationRate(uint256 borrowedLiquidity, uint256 totalLiquidity) private pure returns (uint256 rate) {
```

## [G-13] Multiple address mappings can be combined into a single mapping of an address to a struct, where appropriate

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

```
2023-01-numoen/src/core/ERC20.sol::35 => mapping(address => uint256) public balanceOf;
2023-01-numoen/src/core/ERC20.sol::37 => mapping(address => mapping(address => uint256)) public allowance;
2023-01-numoen/src/core/ERC20.sol::50 => mapping(address => uint256) public nonces;
2023-01-numoen/src/core/Factory.sol::39 => mapping(address => mapping(address => mapping(uint256 => mapping(uint256 => mapping(uint256 => address)))))
2023-01-numoen/src/core/Lendgine.sol::18 => using Position for mapping(address => Position.Info);
2023-01-numoen/src/core/Lendgine.sol::56 => mapping(address => Position.Info) public override positions;
```

## [G-14] internal functions only called once can be inlined to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

```
2023-01-numoen/src/core/ERC20.sol::167 => function _mint(address to, uint256 amount) internal virtual {
2023-01-numoen/src/core/ERC20.sol::179 => function _burn(address from, uint256 amount) internal virtual {
2023-01-numoen/src/core/Pair.sol::70 => function mint(uint256 liquidity, bytes calldata data) internal {
2023-01-numoen/src/core/Pair.sol::93 => function burn(address to, uint256 liquidity) internal returns (uint256 amount0, uint256 amount1) {
2023-01-numoen/src/core/libraries/Position.sol::69 => function newTokensOwed(Position.Info memory position, uint256 rewardPerPosition) internal pure returns (uint256) {
2023-01-numoen/src/core/libraries/PositionMath.sol::12 => function addDelta(uint256 x, int256 y) internal pure returns (uint256 z) {
```

## [G-15] internal functions not called by the contract should be removed to save deployment gas

If the functions are required by an interface, the contract should inherit from that interface and use the override keyword

```
2023-01-numoen/src/core/ERC20.sol::167 => function _mint(address to, uint256 amount) internal virtual {
2023-01-numoen/src/core/ERC20.sol::179 => function _burn(address from, uint256 amount) internal virtual {
2023-01-numoen/src/core/Pair.sol::70 => function mint(uint256 liquidity, bytes calldata data) internal {
2023-01-numoen/src/core/Pair.sol::93 => function burn(address to, uint256 liquidity) internal returns (uint256 amount0, uint256 amount1) {
2023-01-numoen/src/core/libraries/PositionMath.sol::12 => function addDelta(uint256 x, int256 y) internal pure returns (uint256 z) {
```