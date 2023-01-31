| Total Low issues   |
|--------------------|

| Risk   | Issues Details                                                                   | Number        |
|--------|----------------------------------------------------------------------------------|---------------|
| [L-01] | Solmate's `SafeTransferLib.sol` doesn't check whether the ERC20 contract exists  | 4             |
| [L-02] | Loss of precision due to rounding                                                | 6             |
| [L-03] | Missing Event for initialize                                                     | 5             |

| Total Non-Critical issues   |
|-----------------------------|

| Risk    | Issues Details                                                             | Number        |
|---------|----------------------------------------------------------------------------|---------------|
| [NC-01] | Lock pragmas to specific compiler version                                  | All Contracts |
| [NC-02] | Use a more recent version of solidity                                      | All Contracts |
| [NC-03] | Constants in comparisons should appear on the left side                    | 27            |
| [NC-04] | Use `bytes.concat()` and `string.concat()`                                 | 3             |
| [NC-05] | Include `@return` parameters in NatSpec comments                           | 29            |
| [NC-06] | Function writing does not comply with the `Solidity Style Guide`           | 3 Contracts   |
| [NC-07] | NatSpec comments should be increased in contracts                          | All Contracts |
| [NC-08] | Contracts should have full test coverage                                   | All Contracts |
| [NC-09] | Add NatSpec comment to `mapping`                                           | 4             |
| [NC-10] | Remove Unused Codes                                                        | 1             |
| [NC-11] | For functions, follow Solidity standard naming conventions                 | All Contracts |
| [NC-12] | Not using the named return variables anywhere in the function is confusing | 3             |
| [NC-13] | Constants should be defined rather than using magic numbers                | 4             |
| [NC-14] | Need Fuzzing test                                                          | All Contracts |

## [L-01] Solmate's `SafeTransferLib.sol` doesn't check whether the ERC20 contract exists

#### Description

Solmate's `SafeTransferLib.sol`, which is often used to interact with non-compliant/unsafe ERC20 tokens, does not check whether the ERC20 contract exists. The following code will not revert in case the token doesn't exist.

> [Refrence](https://github.com/transmissions11/solmate/blob/main/src/utils/SafeTransferLib.sol#L9) 

```solidity
/// @dev Note that none of the functions in this library check that a token has code at all! That responsibility is delegated to the caller.
```

#### Lines of code 

- [Lendgine.sol#L14](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L14)
- [LendgineRouter.sol#L16](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LendgineRouter.sol#L16)
- [Pair.sol#L14](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Pair.sol#L14)
- [SwapHelper.sol#L9](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/SwapHelper.sol#L9)

#### Recommended Mitigation Steps

Add a contract exist check to your [`SafeTransferLib.sol`](https://github.com/code-423n4/2023-01-numoen/blob/main/src/libraries/SafeTransferLib.sol) library.

## [L-02] Loss of precision due to rounding

#### Description

Loss of precision due to the nature of arithmetics and rounding errors.

```solidity
    return FullMath.mulDiv(liquidity, 2 * upperBound, 1e18) / token1Scale;
```

#### Lines of code 

- [Lendgine.sol#L230](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L230)
- [JumpRate.sol#L17-L21](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol#L17-L21)
- [JumpRate.sol#L37](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol#L37)
- [JumpRate.sol#L42](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol#L42)

## [L-03] Missing Event for initialize

#### Description

Events help non-contract tools to track changes, and events prevent users from being surprised by changes Issuing event-emit during initialization is a detail that many projects skip.

#### Lines of code 

- [ImmutableState.sol#L27-L37](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/ImmutableState.sol#L27-L37)
- [LendgineRouter.sol#L49-L59](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LendgineRouter.sol#L49-L59)
- [LiquidityManager.sol#L75-L77](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L75-L77)
- [Payment.sol#L17-L19](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/Payment.sol#L17-L19)
- [SwapHelper.sol#L29-L32](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/SwapHelper.sol#L29-L32)

#### Recommended Mitigation Steps

Add Event-Emit.

## [NC-01] Lock pragmas to specific compiler version

#### Description

Pragma statements can be allowed to float when a contract is intended for consumption by other developers, as in the case with contracts in a library or EthPM package. Otherwise, the developer would need to manually update the pragma in order to compile locally.

> Ref: https://swcregistry.io/docs/SWC-103

#### Lines of code 

- [All Contracts](https://github.com/code-423n4/2023-01-numoen/tree/main/src)

#### Recommended Mitigation Steps

[Ethereum Smart Contract Best Practices](https://consensys.github.io/smart-contract-best-practices/development-recommendations/solidity-specific/locking-pragmas/):  Lock pragmas to specific compiler version.

## [NC-02] Use a more recent version of solidity

#### Description

For security, it is best practice to use the [latest Solidity version](https://github.com/ethereum/solidity/blob/develop/Changelog.md).

```solidity
pragma solidity ^0.8.4;
```

#### Lines of code 

- [All Contracts](https://github.com/code-423n4/2023-01-numoen/tree/main/src)

#### Recommended Mitigation Steps

Old version of Solidity is used `(^0.8.4)`, newer version can be used `(0.8.17)`.

## [NC-03] Constants in comparisons should appear on the left side

#### Description

Constants in comparisons should appear on the left side, doing so will prevent typo [bug](https://www.moserware.com/2008/01/constants-on-left-are-better-but-this.html).

```solidity
    if (totalLiquidity == 0) return 0;
```

#### Lines of code 

- [Lendgine.sol#L86](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L86)
- [Lendgine.sol#L89](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L89)
- [Lendgine.sol#L112](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L112)
- [Lendgine.sol#L140](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L140)
- [Lendgine.sol#L142](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L142)
- [Lendgine.sol#L170](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L170)
- [Lendgine.sol#L239](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L239)
- [Lendgine.sol#L245](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L245)
- [LiquidityManager.sol#L147](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L147)
- [LendgineRouter.sol#L205](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LendgineRouter.sol#L205)
- [JumpRate.sol#L41](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol#L41)
- [Pair.sol#L54](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Pair.sol#L54)
- [Pair.sol#L100](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Pair.sol#L100)
- [Pair.sol#L117](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Pair.sol#L117)
- [Position.sol#L55](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/libraries/Position.sol#L55)
- [Position.sol#L56](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/libraries/Position.sol#L56)

#### Recommended Mitigation Steps

```solidity
    if (0 == totalLiquidity) return 0;
```

## [NC-04] Use `bytes.concat()` and `string.concat()`

#### Description

- `bytes.concat()` vs `abi.encodePacked(<bytes>,<bytes>)`
- `string.concat()` vs `abi.encodePacked(<string>,<string>)`

> https://docs.soliditylang.org/en/v0.8.17/types.html?highlight=bytes.concat#the-functions-bytes-concat-and-string-concat

#### Lines of code 

- [LendgineAddress.sol#L25](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/libraries/LendgineAddress.sol#L25)
- [UniswapV2Library.sol#L23](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L23)
- [UniswapV2Library.sol#L26](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L26)

#### Recommended Mitigation Steps

Use `bytes.concat()` and `string.concat()` instead.

## [NC-05] Include `@return` parameters in NatSpec comments

#### Description

If Return parameters are declared, you must prefix them with `@return`. Some code analysis programs do analysis by reading [NatSpec](https://docs.soliditylang.org/en/v0.8.15/natspec-format.html) details, if they can't see the `@return` tag, they do incomplete analysis.

#### Lines of code 

- [Factory.sol#L72](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Factory.sol#L72)
- [Lendgine.sol#L79](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L79)
- [Lendgine.sol#L105](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L105)
- [Lendgine.sol#L131](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L131)
- [Lendgine.sol#L159](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L159)
- [Lendgine.sol#L194](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L194)
- [Lendgine.sol#L213](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L213)
- [Lendgine.sol#L219](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L219)
- [Lendgine.sol#L224](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L224)
- [Lendgine.sol#L229](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L229)
- [LiquidityManager.sol#L230](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L230)
- [LendgineRouter.sol#L142](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LendgineRouter.sol#L142)
- [LendgineRouter.sol#L25](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LendgineRouter.sol#L257)
- [JumpRate.sol#L13](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol#L13)
- [JumpRate.sol#L32](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol#L32)
- [JumpRate.sol#L40](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol#L40)
- [Pair.sol#L53](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Pair.sol#L53)
- [Pair.sol#L93](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Pair.sol#L93)
- [Balance.sol#L12](https://github.com/code-423n4/2023-01-numoen/blob/main/src/libraries/Balance.sol#L12)
- [SafeCast.sol#L8](https://github.com/code-423n4/2023-01-numoen/blob/main/src/libraries/SafeCast.sol#L8)
- [LendgineAddress.sol#L19](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/libraries/LendgineAddress.sol#L19)
- [Position.sol#L69](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/libraries/Position.sol#L69)
- [Position.sol#L80](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/libraries/Position.sol#L80)
- [Position.sol#L93](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/libraries/Position.sol#L93)
- [UniswapV2Library.sol#L10](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L10)
- [UniswapV2Library.sol#L17](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L17)
- [UniswapV2Library.sol#L43](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L43)
- [UniswapV2Library.sol#L58](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L58)
- [UniswapV2Library.sol#L76](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L76)

#### Recommended Mitigation Steps

Include the `@return` argument in the NatSpec comments.

## [NC-06] Function writing does not comply with the `Solidity Style Guide`

#### Description

Ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. But there are contracts in the project that do not comply with this.

Functions should be grouped according to their visibility and ordered:

- `constructor()`
- `receive()`  
- `fallback()`  
- `external` / `public` / `internal` / `private`
- `view` / `pure`

#### Lines of code 

- [Pair.sol](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Pair.sol)
- [UniswapV2Library.sol](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol)
- [JumpRate.sol](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol)

#### Recommended Mitigation Steps

Follow [Solidity Style Guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html?highlight=Style#style-guide).

## [NC-07] NatSpec comments should be increased in contracts

#### Description

It is recommended that Solidity contracts are fully annotated using NatSpec, it is clearly stated in the Solidity official documentation.

- In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.

- Some code analysis programs do analysis by reading NatSpec details, if they can't see the tags `(@param, @dev, @return)`, they do incomplete analysis.

#### Lines of code 

- [All Contracts](https://github.com/code-423n4/2023-01-numoen/tree/main/src)

#### Recommended Mitigation Steps

Include [NatSpec](https://docs.soliditylang.org/en/v0.8.15/natspec-format.html#natspec-format) comments in the codebase.

## [NC-08] Contracts should have full test coverage

#### Description

While 100% code coverage does not guarantee that there are no bugs, it often will catch easy-to-find bugs, and will ensure that there are fewer regressions when the code invariably has to be modified. Furthermore, in order to get full coverage, code authors will often have to re-organize their code so that it is more modular, so that each component can be tested separately, which reduces interdependencies between modules and layers, and makes for code that is easier to reason about and audit.

```solidity
- What is the overall line coverage percentage provided by your tests?: 0 
```

#### Lines of code 

- [All Contracts](https://github.com/code-423n4/2023-01-numoen/tree/main/src)

#### Recommended Mitigation Steps

Line coverage percentage should be 100%.

## [NC-09] Add NatSpec comment to `mapping`  

#### Description

Add NatSpec comments describing mapping keys and values.

#### Lines of code 

- [Factory.sol#L39](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Factory.sol#L39)
- [Lendgine.sol#L18](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L18)
- [Lendgine.sol#L56](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L56)
- [LiquidityManager.sol#L69](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L69)

#### Recommended Mitigation Steps

```solidity
/// @dev  uint(timestamp) => uint(token Id)
    mapping(uint => uint) public timestampForTokenId;
```

## [NC-10] Remove Unused Codes

#### Description

The below error is unused code: 

```solidity
  error FailedApprove();
```

#### Lines of code 

- [SafeTransferLib.sol#L14](https://github.com/code-423n4/2023-01-numoen/blob/main/src/libraries/SafeTransferLib.sol#L14)

#### Recommended Mitigation Steps

I recommend removing unused codes from the codebase.

## [NC-11] For functions, follow Solidity standard naming conventions

#### Description

The protocol don't follow solidity standard naming convention.

> Ref: https://docs.soliditylang.org/en/v0.8.17/style-guide.html#naming-conventions

#### Lines of code 

- [All Contracts](https://github.com/code-423n4/2023-01-numoen/tree/main/src)

#### Recommended Mitigation Steps

Follow solidity standard naming convention.

## [NC-12] Not using the named return variables anywhere in the function is confusing

#### Description

Not using the named return variables anywhere in the function is confusing.

```solidity
  function utilizationRate(uint256 borrowedLiquidity, uint256 totalLiquidity) private pure returns (uint256 rate) {
    if (totalLiquidity == 0) return 0;
    return (borrowedLiquidity * 1e18) / totalLiquidity;
  }
```

#### Lines of code 

- [JumpRate.sol#L13](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol#L13)
- [JumpRate.sol#L32](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol#L32)
- [JumpRate.sol#L40](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol#L40)

#### Recommended Mitigation Steps

Consider changing the variable to be an unnamed one.

```solidity
  function utilizationRate(uint256 borrowedLiquidity, uint256 totalLiquidity) private pure returns (uint256) {
    if (totalLiquidity == 0) return 0;
    return (borrowedLiquidity * 1e18) / totalLiquidity;
  }
```
## [NC-13] Constants should be defined rather than using magic numbers

#### Description

```solidity
    uint256 amountInWithFee = amountIn * 997;
```
```solidity
    uint256 denominator = (reserveIn * 1000) + amountInWithFee;
```
```solidity
    uint256 numerator = reserveIn * amountOut * 1000;
```
```solidity
    uint256 denominator = (reserveOut - amountOut) * 997;
```

#### Lines of code 

- [UniswapV2Library.sol#L62](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L62)
- [UniswapV2Library.sol#L64](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L64)
- [UniswapV2Library.sol#L80](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L80)
- [UniswapV2Library.sol#L81](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L81)

## [NC-14] Need Fuzzing test

#### Description

As Alberto Cuesta Canada said: Fuzzing is not easy, the tools are rough, and the math is hard, but it is worth it. Fuzzing gives me a level of confidence in my smart contracts that I didn’t have before. Relying just on unit testing anymore and poking around in a testnet seems reckless now.

> Ref: https://medium.com/coinmonks/smart-contract-fuzzing-d9b88e0b0a05

#### Lines of code 

- [All Contracts](https://github.com/code-423n4/2023-01-numoen/tree/main/src)

#### Recommended Mitigation Steps

Use fuzzing test like Echidna.
