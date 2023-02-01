# QA Report

## Summary

|               | Issue         | Risk     | Instances     |
| :-------------: |:-------------|:-------------:|:-------------:|
| 1 | `accruePositionInterest()` is not called if user try to collect directly from `Lendgine` contract | Low | 1 |
| 2 | Named return variables not used anywhere in the functions | NC | 3 |
| 3 | Use scientific notation| NC | 2 |


## Findings

### 1- `accruePositionInterest()` is not called if user try to collect directly from `Lendgine` contract :

#### Risk : Low

When a user tries to collect his owed tokens he can do it by two methods :

* The first is to call the `collect` function from the `LiquidityManager` contract, this function update the position interest before calling the `collect` function from the `Lendgine` contract which sends the tokens to the user.
* The second method is to call the `collect` function directly from the `Lendgine` contract, this function will not call `accruePositionInterest()` and will send the tokens immediately to the user.

Thus if a user tries to collect directly by using method 2 he will have less tokens owed to collect because the position interest was not updated.

#### Proof of Concept

Issue occurs in the instance below :

File: core/Lendgine.sol [Line 194-206](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L194-L206)
```
function collect(address to, uint256 collateralRequested) external override nonReentrant returns (uint256 collateral) {
    /** @audit
        As you can see the function proceeds to the transfer without updating the position first 
    */
    Position.Info storage position = positions[msg.sender]; // SLOAD
    uint256 tokensOwed = position.tokensOwed;

    collateral = collateralRequested > tokensOwed ? tokensOwed : collateralRequested;

    if (collateral > 0) {
        position.tokensOwed = tokensOwed - collateral; // SSTORE
        SafeTransferLib.safeTransfer(token1, to, collateral);
    }

    emit Collect(msg.sender, to, collateral);
}
```

#### Mitigation

To avoid this issue i recommend to add the `accruePositionInterest()` call to the `collect` function of the `Lendgine` contract and remove it from the `collect` function of `LiquidityManager`, this way the position interest will be updated for both cases mentioned previously.

### 2- Named return variables not used anywhere in the function :

When Named return variable are declared they should be used inside the function instead of the return statement or if not they should be removed to avoid confusion.

#### Risk : NON CRITICAL

#### Proof of Concept

Instances include:

File: core/JumpRate.sol [Line 13](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol#L13)
```
returns (uint256 rate)
```

File: core/JumpRate.sol [Line 32](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol#L32)
```
returns (uint256 rate)
```

File: core/JumpRate.sol [Line 40](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol#L40)
```
returns (uint256 rate)
```

#### Mitigation

Either use the named return variables in place of the return statement or remove them.

### 3- Use scientific notation :

When using multiples of 10 you shouldn't use decimal literals or exponentiation (e.g. 1000000, 10**18) but use scientific notation for better readability.

#### Risk : NON CRITICAL

#### Proof of Concept

Instances include:

File: periphery/UniswapV2/libraries/UniswapV2Library.sol [Line 64](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L64)

```
uint256 denominator = (reserveIn * 1000) + amountInWithFee;
```

File: periphery/UniswapV2/libraries/UniswapV2Library.sol [Line 80](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L80)

```
uint256 numerator = reserveIn * amountOut * 1000;
```

#### Mitigation
Use scientific notation for the instances aforementioned.