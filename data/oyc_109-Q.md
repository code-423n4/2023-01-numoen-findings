

## [L-01] Unspecific Compiler Version Pragma

Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

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

## [L-02] Use of Block.timestamp

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

```
2023-01-numoen/src/core/ERC20.sol::124 => require(deadline >= block.timestamp, "PERMIT_DEADLINE_EXPIRED");
2023-01-numoen/src/core/Lendgine.sol::240 => lastUpdate = block.timestamp;
2023-01-numoen/src/core/Lendgine.sol::244 => uint256 timeElapsed = block.timestamp - lastUpdate;
2023-01-numoen/src/core/Lendgine.sol::258 => lastUpdate = block.timestamp;
```

## [L-03] _safeMint() should be used rather than _mint() wherever possible

Some tokens do not implement the ERC20 standard properly but are still accepted by most code that accepts ERC20 tokens. For example Tether (USDT)'s transfer() and transferFrom() functions do not return booleans as the specification requires, and instead have no return value. When these sorts of tokens are cast to IERC20, their function signatures do not match and therefore the calls made, revert. Use OpenZeppelin’s SafeERC20's safeTransfer()/safeTransferFrom() instead

```
2023-01-numoen/src/core/ERC20.sol::167 => function _mint(address to, uint256 amount) internal virtual {
2023-01-numoen/src/core/Lendgine.sol::93 => _mint(to, shares);
```

## [N-01] Use a more recent version of solidity

Use a solidity version of at least 0.8.4 to get bytes.concat() instead of abi.encodePacked(<bytes>,<bytes>)
Use a solidity version of at least 0.8.12 to get string.concat() instead of abi.encodePacked(<str>,<str>)
Use a solidity version of at least 0.8.13 to get the ability to use using for with a list of free functions

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

## [N-02] Event is missing indexed fields

Each event should use three indexed fields if there are three or more fields

```
2023-01-numoen/src/core/Lendgine.sol::33 => event AccrueInterest(uint256 timeElapsed, uint256 collateral, uint256 liquidity);
2023-01-numoen/src/core/Pair.sol::21 => event Mint(uint256 amount0In, uint256 amount1In, uint256 liquidity);
```

## [N-03] Missing NatSpec

Code should include NatSpec

```
2023-01-numoen/src/core/JumpRate.sol::1 => // SPDX-License-Identifier: GPL-3.0-only
```

