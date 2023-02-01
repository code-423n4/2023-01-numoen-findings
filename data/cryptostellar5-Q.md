
### 1. FLOATING PRAGMA VERSION SHOULD NOT BE USED

*Number of Instances Identified: 15*

In the contracts, floating pragmas should not be used. Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

[https://swcregistry.io/docs/SWC-103](https://swcregistry.io/docs/SWC-103)

### Proof of Concept

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Factory.sol

```
pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/ImmutableState.sol

```
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol

```
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol

```
pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Pair.sol

```
pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/libraries/Position.sol

```
pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/libraries/PositionMath.sol

```
pragma solidity >=0.5.0;
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/libraries/Balance.sol

```
pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/libraries/SafeCast.sol

```
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LendgineRouter.sol

```
pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol

```
pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/Payment.sol

```
pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/SwapHelper.sol

```
pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/libraries/LendgineAddress.sol

```
pragma solidity >=0.5.0;
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol

```
pragma solidity >=0.8.0;
```



### 2. INCLUDE RETURN PARAMETERS IN NATSPEC COMMENTS

*Number of Instances Identified: 29*

### Description

It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation. In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.

[https://docs.soliditylang.org/en/v0.8.15/natspec-format.html](https://docs.soliditylang.org/en/v0.8.15/natspec-format.html)

If Return parameters are declared, you must prefix them with `/// @return`.

Some code analysis programs do analysis by reading NatSpec details, if they can’t see the `@return` tag, they do incomplete analysis.

### Recommendation

Include return parameters in NatSpec comments

Recommendated Natspec Code Style:

```
   /// @notice information about what a function does
   /// @param param values
   /// @return info about things to be returned
```

POC :

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Factory.sol

```
63-72: function createLendgine(
    address token0,
    address token1,
    uint8 token0Exp,
    uint8 token1Exp,
    uint256 upperBound
  )
    external
    override
    returns (address lendgine)
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol

```
13: function getBorrowRate(uint256 borrowedLiquidity, uint256 totalLiquidity) public pure override returns (uint256 rate)

25-32: function getSupplyRate(
    uint256 borrowedLiquidity,
    uint256 totalLiquidity
  )
    external
    pure
    override
    returns (uint256 rate)

40: function utilizationRate(uint256 borrowedLiquidity, uint256 totalLiquidity) private pure returns (uint256 rate)
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol

```
71-79: function mint(
    address to,
    uint256 collateral,
    bytes calldata data
  )
    external
    override
    nonReentrant
    returns (uint256 shares)

105: function burn(address to, bytes calldata data) external override nonReentrant returns (uint256 collateral)

123-131: function deposit(
    address to,
    uint256 liquidity,
    bytes calldata data
  )
    external
    override
    nonReentrant
    returns (uint256 size)

152-159: function withdraw(
    address to,
    uint256 size
  )
    external
    override
    nonReentrant
    returns (uint256 amount0, uint256 amount1, uint256 liquidity)

194: function collect(address to, uint256 collateralRequested) external override nonReentrant returns (uint256 collateral)

213: function convertLiquidityToShare(uint256 liquidity) public view override returns (uint256)

219: function convertShareToLiquidity(uint256 shares) public view override returns (uint256)

224: function convertCollateralToLiquidity(uint256 collateral) public view override returns (uint256)

229: function convertLiquidityToCollateral(uint256 liquidity) public view override returns (uint256)

```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Pair.sol

```
53: function invariant(uint256 amount0, uint256 amount1, uint256 liquidity) public view override returns (bool)
93: function burn(address to, uint256 liquidity) internal returns (uint256 amount0, uint256 amount1)
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/libraries/Position.sol

```
69: function newTokensOwed(Position.Info memory position, uint256 rewardPerPosition) internal pure returns (uint256)

73-80: function convertLiquidityToPosition(
    uint256 liquidity,
    uint256 totalLiquiditySupplied,
    uint256 totalPositionSize
  )
    internal
    pure
    returns (uint256)

86-93: function convertPositionToLiquidity(
    uint256 position,
    uint256 totalLiquiditySupplied,
    uint256 totalPositionSize
  )
    internal
    pure
    returns (uint256)
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/libraries/Balance.sol

```
12: function balance(address token) internal view returns (uint256)
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/libraries/SafeCast.sol

```
8: function toUint120(uint256 y) internal pure returns (uint120 z)
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LendgineRouter.sol

```
142: function mint(MintParams calldata params) external payable checkDeadline(params.deadline) returns (uint256 shares)
257: function burn(BurnParams calldata params) external payable checkDeadline(params.deadline) returns (uint256 amount)
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol

```
230: function collect(CollectParams calldata params) external payable returns (uint256 amount)
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/libraries/LendgineAddress.sol

```
9-19: function computeAddress(
    address factory,
    address token0,
    address token1,
    uint256 token0Exp,
    uint256 token1Exp,
    uint256 upperBound
  )
    internal
    pure
    returns (address lendgine)
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol

```
10: function sortTokens(address tokenA, address tokenB) internal pure returns (address token0, address token1)

17: function pairFor(address factory, address tokenA, address tokenB) internal pure returns (address pair)

36-43: function getReserves(
    address factory,
    address tokenA,
    address tokenB
  )
    internal
    view
    returns (uint256 reserveA, uint256 reserveB)

51-58: function getAmountOut(
    uint256 amountIn,
    uint256 reserveIn,
    uint256 reserveOut
  ) 
    internal
    pure
    returns (uint256 amountOut)

69-76: function getAmountIn(
    uint256 amountOut,
    uint256 reserveIn,
    uint256 reserveOut
  ) 
    internal
    pure
    returns (uint256 amountIn)
```


### 3. ADDING A RETURN STATEMENT WHEN THE FUNCTION DEFINES A NAMED RETURN VARIABLE, IS REDUNDANT

*Number of Instances Identified: 3*

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol

```
13-23: function getBorrowRate(uint256 borrowedLiquidity, uint256 totalLiquidity) public pure override returns (uint256 rate) { 
    uint256 util = utilizationRate(borrowedLiquidity, totalLiquidity);

    if (util <= kink) {
      return (util * multiplier) / 1e18;
    } else {
      uint256 normalRate = (kink * multiplier) / 1e18;
      uint256 excessUtil = util - kink;
      return ((excessUtil * jumpMultiplier) / 1e18) + normalRate;
    }
  }

25-38: unction getSupplyRate(
    uint256 borrowedLiquidity,
    uint256 totalLiquidity
  )
    external
    pure
    override
    returns (uint256 rate)
  {
    uint256 util = utilizationRate(borrowedLiquidity, totalLiquidity);
    uint256 borrowRate = getBorrowRate(borrowedLiquidity, totalLiquidity);

    return (borrowRate * util) / 1e18;
  }

40-43: function utilizationRate(uint256 borrowedLiquidity, uint256 totalLiquidity) private pure returns (uint256 rate) { 
    if (totalLiquidity == 0) return 0;
    return (borrowedLiquidity * 1e18) / totalLiquidity;
  }
```


### 4. NAMING CONVENTION FOR CONSTANTS SHOULD BE FOLLOWED

*Number of Instances Identified: 3*

Constants should be named with all capital letters with underscores separating words. 

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol

```
7: uint256 public constant override kink = 0.8 ether;
9: uint256 public constant override multiplier = 1.375 ether;
11: uint256 public constant override jumpMultiplier = 44.5 ether;
```


### 5. USE LATEST SOLIDITY VERSION

*Number of Instances Identified: 15*

When deploying contracts, you should use the latest released version of Solidity. Apart from exceptional cases, only the latest version receives [security fixes](https://github.com/ethereum/solidity/security/policy#supported-versions). Furthermore, breaking changes as well as new features are introduced regularly.
Latest Version is 0.8.17


https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Factory.sol

```
pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/ImmutableState.sol

```
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol

```
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol

```
pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Pair.sol

```
pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/libraries/Position.sol

```
pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/libraries/PositionMath.sol

```
pragma solidity >=0.5.0;
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/libraries/Balance.sol

```
pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/libraries/SafeCast.sol

```
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LendgineRouter.sol

```
pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol

```
pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/Payment.sol

```
pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/SwapHelper.sol

```
pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/libraries/LendgineAddress.sol

```
pragma solidity >=0.5.0;
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol

```
pragma solidity >=0.8.0;
```


### 6. USE SCIENTIFIC NOTATION FOR EXPONENTS

*Number of Instances Identified: 3

Use scientific notation (e.g. 1e18) rather than exponentiation (e.g. `10**18`)

Scientific notation should be used for better code readability

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/ImmutableState.sol

```
35: token0Scale = 10 ** (18 - _token0Exp);
36: token1Scale = 10 ** (18 - _token1Exp);
```

https://github.com/code-423n4/2023-01-numoen/blob/main/src/libraries/SafeCast.sol

```
16: require(y < 2 ** 255);
```


