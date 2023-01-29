# QA report

## Low Risk
| L-N    |Issue|
|:------:|:----|
| [L&#x2011;01] | Missing non-zero address checks in constructor in `LiquidityManager` and `LendgineRouter` | 9 |

Total: 1 issue

## Non-critical

| N-C    |Issue|
|:------:|:----|
| [NC&#x2011;01] | Constants should be capitalized | 2 |
| [NC&#x2011;02] | Use x != 0 to get positive-only uint values | 6 |
| [NC&#x2011;03] | Use latest Solidity version with a stable pragma statement | 11 |
| [NC&#x2011;04] | Events, structs and custom errors declaration | 19 |
| [NC&#x2011;05] | Constants should be defined rather than using magic numbers | 31 |
| [NC&#x2011;06] | Typos in comments | 2 |
| [NC&#x2011;07] |  Incomplete NatSpec | 2 |


Total: 6 issues

## Low Risk

### [L-01] Missing non-zero address checks in constructor in `LiquidityManager` and `LendgineRouter`

In `LiquidityManager` the address variable `factory` is immutable and set in the constructor which is only callable once. If any errors are made when passing the argument for `factory` in the constructors, they will be irreversable.

```solidity
  constructor(address _factory, address _weth) Payment(_weth) {
    factory = _factory;
  }
```

```solidity
  constructor(
    address _factory,
    address _uniswapV2Factory,
    address _uniswapV3Factory,
    address _weth
  )
    SwapHelper(_uniswapV2Factory, _uniswapV3Factory)
    Payment(_weth)
  {
    factory = _factory;
  }
```

Consider adding such checks for both contracts.

## Non-critical

### \[NC-01\] Constants should be capitalized

File: `JumpRate.sol`

```solidity
  uint256 public constant override kink = 0.8 ether;

  uint256 public constant override multiplier = 1.375 ether;

  uint256 public constant override jumpMultiplier = 44.5 ether;
```

### \[NC-02\] Use x != 0 to get positive-only uint values

The "positive uint" checks in the code are not done in the best possible way, one example is `(decoded.amount0 > 0)` - if a number is expected to be of a uint type, then you can check that it is positive by doing x != 0 since uint can never be a negative number. Replace all x > 0 occurrences with x != 0 when x is a uint.

Example in `LiquidityManager`:

```solidity
if (decoded.amount0 > 0) pay(decoded.token0, decoded.payer, msg.sender, decoded.amount0);
```

### \[NC-03\] Use latest Solidity version with a stable pragma statement
Using a floating pragma ^0.8.4 statement is discouraged as code can compile to different bytecodes with different compiler versions. Use a stable pragma statement to get a deterministic bytecode. Also use latest Solidity version to get all compiler features, bugfixes and optimizations

### \[NC-04\] Events, structs and custom errors declaration

It is a best practice to be declared in the interface not in the implementation contract.

Files: `Lendgine` and `Factory`

### \[NC-05\] Constants should be defined rather than using magic numbers 

File: `Factory.sol`

```solidity
 if (token0Exp > 18 || token0Exp < 6 || token1Exp > 18 || token1Exp < 6) revert ScaleError();
 ```
 
 File: `Lendgine.sol`

```solidity
return FullMath.mulDiv(liquidity, 2 * upperBound, 1e18) / token1Scale;
```
### \[NC-06\] Typos in comments

File: `LiquidityManager.sol`

Change `liqudity` => `liquidity`

File: `ILendgine.sol`

Change `recieves` => `receives`

### \[NC-07\]  Incomplete NatSpec

`IJumpRate.sol` is missing natspec documenation, and some other contracts are missing @param comments. NatSpec documentation to all public methods and variables is essential for better understanding of the code by developers and auditors and is strongly recommended. Consider adding a full NatSpec documentation.