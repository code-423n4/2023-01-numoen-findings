## Summary<a name="Summary">

### Low Risk Issues
| |Issue|Contexts|
|-|:-|:-:|
| [LOW&#x2011;1](#LOW&#x2011;1) | Possible rounding issue | 1 |
| [LOW&#x2011;2](#LOW&#x2011;2) | Use `_safeMint` instead of `_mint` | 1 |
| [LOW&#x2011;3](#LOW&#x2011;3) | Missing parameter validation in `constructor` | 9 |

Total: 27 contexts over 6 issues

### Non-critical Issues
| |Issue|Contexts|
|-|:-|:-:|
| [NC&#x2011;1](#NC&#x2011;1) | Compliance with Solidity Style rules in Constant expressions | 3 |
| [NC&#x2011;2](#NC&#x2011;2) | Duplicated `require()`/`revert()` Checks Should Be Refactored To A Modifier Or Function | 2 |
| [NC&#x2011;3](#NC&#x2011;3) | Function writing that does not comply with the Solidity Style Guide  | All in-scope contracts |
| [NC&#x2011;4](#NC&#x2011;4) | NatSpec return parameters should be included in contracts | All in-scope contracts |
| [NC&#x2011;5](#NC&#x2011;5) | NatSpec comments should be increased in contracts | All in-scope contracts |
| [NC&#x2011;6](#NC&#x2011;6) | Use a more recent version of Solidity | 15 |
| [NC&#x2011;7](#NC&#x2011;7) | Use `bytes.concat()` | 2 |
| [NC&#x2011;8](#NC&#x2011;8) | Use of Block.Timestamp | 2 |

Total: 138 contexts over 18 issues

## Low Risk Issues

### <a href="#Summary">[LOW&#x2011;1]</a><a name="LOW&#x2011;1"> Possible rounding issue

There might be a rounding issue. For example, if `totalLiquidity` is `20*1e18` and `borrowedLiquidity` is `19`, would lead to return `0` value

#### <ins>Proof Of Concept</ins>


```solidity
42: return (borrowedLiquidity * 1e18) / totalLiquidity;

```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/JumpRate.sol#L42




### <a href="#Summary">[LOW&#x2011;2]</a><a name="LOW&#x2011;2"> Use `_safeMint` instead of `_mint`

According to openzepplin's ERC721, the use of `_mint` is discouraged, use _safeMint whenever possible.
https://docs.openzeppelin.com/contracts/3.x/api/token/erc721#ERC721-_mint-address-uint256-

#### <ins>Proof Of Concept</ins>


```solidity
93: _mint(to, shares);
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Lendgine.sol#L93



#### <ins>Recommended Mitigation Steps</ins>

Use `_safeMint` whenever possible instead of `_mint`



### <a href="#Summary">[LOW&#x2011;3]</a><a name="LOW&#x2011;3"> Missing parameter validation in `constructor`

Some parameters of constructors are not checked for invalid values.

#### <ins>Proof Of Concept</ins>

```solidity
50: address _factory
51: address _uniswapV2Factory
52: address _uniswapV3Factory
53: address _weth

```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/LendgineRouter.sol#L50-L53

```solidity
75: address _factory
75: address _weth

```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/LiquidityManager.sol#L75-L75

```solidity
17: address _weth

```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/Payment.sol#L17

```solidity
29: address _uniswapV2Factory
29: address _uniswapV3Factory

```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/SwapHelper.sol#L29-L29



#### <ins>Recommended Mitigation Steps</ins>

Validate the parameters.





## Non Critical Issues



### <a href="#Summary">[NC&#x2011;1]</a><a name="NC&#x2011;1"> Compliance with Solidity Style rules in Constant expressions

#### <ins>Proof Of Concept</ins>


```solidity
7: uint256 public constant override kink = 0.8 ether;
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/JumpRate.sol#L7

```solidity
9: uint256 public constant override multiplier = 1.375 ether;
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/JumpRate.sol#L9

```solidity
11: uint256 public constant override jumpMultiplier = 44.5 ether;
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/JumpRate.sol#L11



#### <ins>Recommended Mitigation Steps</ins>

Variables are declared as constant utilize the UPPER_CASE_WITH_UNDERSCORES format.






#### <a href="#Summary">[NC&#x2011;2]</a><a name="NC&#x2011;2"> Duplicated `require()`/`revert()` Checks Should Be Refactored To A Modifier Or Function

Saves deployment costs

#### <ins>Proof Of Concept</ins>

```solidity
61: require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");
79: require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");

```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L61

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L79









### <a href="#Summary">[NC&#x2011;3]</a><a name="NC&#x2011;3"> Function writing that does not comply with the Solidity Style Guide

Order of Functions; ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. But there are contracts in the project that do not comply with this.

https://docs.soliditylang.org/en/v0.8.17/style-guide.html

Functions should be grouped according to their visibility and ordered:
- constructor
- receive function (if exists)
- fallback function (if exists)
- external
- public
- internal
- private
- within a grouping, place the view and pure functions last

#### <ins>Proof Of Concept</ins>

All in-scope contracts







### <a href="#Summary">[NC&#x2011;4]</a><a name="NC&#x2011;4"> NatSpec return parameters should be included in contracts

It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation. In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.

https://docs.soliditylang.org/en/v0.8.15/natspec-format.html
#### <ins>Proof Of Concept</ins>


All in-scope contracts


#### <ins>Recommended Mitigation Steps</ins>

Include return parameters in NatSpec comments

Recommendation Code Style: (from Uniswap3)

```solidity
    /// @notice Adds liquidity for the given recipient/tickLower/tickUpper position
    /// @dev The caller of this method receives a callback in the form of IUniswapV3MintCallback#uniswapV3MintCallback
    /// in which they must pay any token0 or token1 owed for the liquidity. The amount of token0/token1 due depends
    /// on tickLower, tickUpper, the amount of liquidity, and the current price.
    /// @param recipient The address for which the liquidity will be created
    /// @param tickLower The lower tick of the position in which to add liquidity
    /// @param tickUpper The upper tick of the position in which to add liquidity
    /// @param amount The amount of liquidity to mint
    /// @param data Any data that should be passed through to the callback
    /// @return amount0 The amount of token0 that was paid to mint the given amount of liquidity. Matches the value in the callback
    /// @return amount1 The amount of token1 that was paid to mint the given amount of liquidity. Matches the value in the callback
    function mint(
        address recipient,
        int24 tickLower,
        int24 tickUpper,
        uint128 amount,
        bytes calldata data
    ) external returns (uint256 amount




### <a href="#Summary">[NC&#x2011;5]</a><a name="NC&#x2011;5"> NatSpec comments should be increased in contracts

It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation. In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability. https://docs.soliditylang.org/en/v0.8.15/natspec-format.html

#### <ins>Proof Of Concept</ins>


All in-scope contracts


#### <ins>Recommended Mitigation Steps</ins>

NatSpec comments should be increased in contracts



### <a href="#Summary">[NC&#x2011;6]</a><a name="NC&#x2011;6"> Use a more recent version of Solidity

<a href="https://blog.soliditylang.org/2021/04/21/solidity-0.8.4-release-announcement/">0.8.4</a>:
bytes.concat() instead of abi.encodePacked(<bytes>,<bytes>)

<a href="https://blog.soliditylang.org/2022/02/16/solidity-0.8.12-release-announcement/">0.8.12</a>: 
string.concat() instead of abi.encodePacked(<str>,<str>)

<a href="https://blog.soliditylang.org/2022/03/16/solidity-0.8.13-release-announcement/">0.8.13</a>: 
Ability to use using for with a list of free functions

<a href="https://blog.soliditylang.org/2022/05/18/solidity-0.8.14-release-announcement/">0.8.14</a>:

ABI Encoder: When ABI-encoding values from calldata that contain nested arrays, correctly validate the nested array length against calldatasize() in all cases.
Override Checker: Allow changing data location for parameters only when overriding external functions.

<a href="https://blog.soliditylang.org/2022/06/15/solidity-0.8.15-release-announcement/">0.8.15</a>:

Code Generation: Avoid writing dirty bytes to storage when copying bytes arrays.
Yul Optimizer: Keep all memory side-effects of inline assembly blocks.

<a href="https://blog.soliditylang.org/2022/08/08/solidity-0.8.16-release-announcement/">0.8.16</a>:

Code Generation: Fix data corruption that affected ABI-encoding of calldata values represented by tuples: structs at any nesting level; argument lists of external functions, events and errors; return value lists of external functions. The 32 leading bytes of the first dynamically-encoded value in the tuple would get zeroed when the last component contained a statically-encoded array.

<a href="https://blog.soliditylang.org/2022/09/08/solidity-0.8.17-release-announcement/">0.8.17</a>:

Yul Optimizer: Prevent the incorrect removal of storage writes before calls to Yul functions that conditionally terminate the external EVM call.

#### <ins>Proof Of Concept</ins>


```solidity
pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Factory.sol#L2

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/ImmutableState.sol#L2

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/JumpRate.sol#L2

```solidity
pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Lendgine.sol#L2

```solidity
pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Pair.sol#L2

```solidity
pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/libraries/Position.sol#L2

```solidity
pragma solidity >=0.5.0;
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/libraries/PositionMath.sol#L2

```solidity
pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/libraries/Balance.sol#L2

```solidity
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/libraries/SafeCast.sol#L2

```solidity
pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/LendgineRouter.sol#L2

```solidity
pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/LiquidityManager.sol#L2

```solidity
pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/Payment.sol#L2

```solidity
pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/SwapHelper.sol#L2

```solidity
pragma solidity >=0.5.0;
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/libraries/LendgineAddress.sol#L2

```solidity
pragma solidity >=0.8.0;
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L1



#### <ins>Recommended Mitigation Steps</ins>

Consider updating to a more recent solidity version.













### <a href="#Summary">[NC&#x2011;7]</a><a name="NC&#x2011;7"> Use `bytes.concat()`

Solidity version 0.8.4 introduces `bytes.concat()` (vs `abi.encodePacked(<bytes>,<bytes>)`)

#### <ins>Proof Of Concept</ins>


```solidity
25: abi.encodePacked(
              hex"ff",
              factory,
              keccak256(abi.encode(token0, token1, token0Exp, token1Exp, upperBound)

```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/libraries/LendgineAddress.sol#L25

```solidity
23: abi.encodePacked(
              hex"ff",
              factory,
              keccak256(abi.encodePacked(token0, token1)

```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L23




#### <ins>Recommended Mitigation Steps</ins>

Use `bytes.concat()` and upgrade to at least Solidity version 0.8.4 if required. 



### <a href="#Summary">[NC&#x2011;8]</a><a name="NC&#x2011;8"> Use of Block.Timestamp

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.
References: SWC ID: 116

#### <ins>Proof Of Concept</ins>


```solidity
66: if (deadline < block.timestamp) revert LivelinessError();
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/LendgineRouter.sol#L66

```solidity
84: if (deadline < block.timestamp) revert LivelinessError();
```

https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/LiquidityManager.sol#L84



#### <ins>Recommended Mitigation Steps</ins>
Block timestamps should not be used for entropy or generating random numbers—i.e., they should not be the deciding factor (either directly or through some derivation) for winning a game or changing an important state.

Time-sensitive logic is sometimes required; e.g., for unlocking contracts (time-locking), completing an ICO after a few weeks, or enforcing expiry dates. It is sometimes recommended to use block.number and an average block time to estimate times; with a 10 second block time, 1 week equates to approximately, 60480 blocks. Thus, specifying a block number at which to change a contract state can be more secure, as miners are unable to easily manipulate the block number.



