

# Low

## Lack of sanity check for same value will revert
### Impact

If both values are the same, operation will revert due to division by 0 

### Vulnerability Details

There is a call of [LendgineRouter.sol#mintCallback()](https://github.com/code-423n4/2023-01-numoen/blob/c75b39dc93fe327039b097ba149e1abe90d3b2f5/src/periphery/LendgineRouter.sol#L107) that calls [SwapHelper#swap()](https://github.com/code-423n4/2023-01-numoen/blob/c75b39dc93fe327039b097ba149e1abe90d3b2f5/src/periphery/SwapHelper.sol#L78) that calls, for negative values (later converted in positive with `-` operator), function [getAmountIn](https://github.com/code-423n4/2023-01-numoen/blob/c75b39dc93fe327039b097ba149e1abe90d3b2f5/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L69-L83).

```solidity
function getAmountIn(
  uint256 amountOut,
  uint256 reserveIn,
  uint256 reserveOut
)
  internal
  pure
  returns (uint256 amountIn)
{
  require(amountOut > 0, "UniswapV2Library: INSUFFICIENT_OUTPUT_AMOUNT");
  require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");
  uint256 numerator = reserveIn * amountOut * 1000;
  uint256 denominator = (reserveOut - amountOut) * 997;
  amountIn = (numerator / denominator) + 1;
}
```

If the value is the same of reserveOut, line:
`uint256 denominator = (reserveOut - amountOut) * 997;`

`denominator` will be 0 and will revert next line
`amountIn = (numerator / denominator) + 1;`

### Recommendation

Add a sanity check for not being the same values or revert in [SwapHelper](https://github.com/code-423n4/2023-01-numoen/blob/c75b39dc93fe327039b097ba149e1abe90d3b2f5/src/periphery/SwapHelper.sol#L74-L75) or in [getAmountIn](https://github.com/code-423n4/2023-01-numoen/blob/c75b39dc93fe327039b097ba149e1abe90d3b2f5/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L69-L83).




## `block.timestamp` used as time proxy 
### Summary

Risk of using `block.timestamp` for time should be considered. 

### Details

`block.timestamp` is not an ideal proxy for time because of issues with synchronization, miner manipulation and changing block times. 

This kind of issue may affect the code allowing or reverting the code before the expected deadline, modifying the normal functioning or reverting sometimes.

### References

SWC ID: 116

### Code Snippet 

- [Lendgine.sol#L240](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Lendgine.sol#L240)
      lastUpdate = block.timestamp;

- [Lendgine.sol#L244](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Lendgine.sol#L244)
    uint256 timeElapsed = block.timestamp - lastUpdate;

- [Lendgine.sol#L258](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Lendgine.sol#L258)
    lastUpdate = block.timestamp;

- [LiquidityManager.sol#L84](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/LiquidityManager.sol#L84)
    if (deadline < block.timestamp) revert LivelinessError();

- [LendgineRouter.sol#L66](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/LendgineRouter.sol#L66)
    if (deadline < block.timestamp) revert LivelinessError();


### Recommendation

- Consider the risk of using `block.timestamp` as time proxy and evaluate if block numbers can be used as an approximation for the application logic. Both have risks that need to be factored in. 
- Consider using an oracle for precision


## Destination recipient for assets transfers may be `address(0)`

```solidity
  function sweepToken(address token, uint256 amountMinimum, address recipient) public payable {
    uint256 balanceToken = Balance.balance(token);
    if (balanceToken < amountMinimum) revert InsufficientOutputError();

    if (balanceToken > 0) {
      SafeTransferLib.safeTransfer(token, recipient, balanceToken);
    }
  }
```

- [Payment.sol#L40]https://github.com/code-423n4/2023-01-numoen/blob/c75b39dc93fe327039b097ba149e1abe90d3b2f5/src/periphery/Payment.sol#L40

- [Payment.sol#L56]https://github.com/code-423n4/2023-01-numoen/blob/c75b39dc93fe327039b097ba149e1abe90d3b2f5/src/periphery/Payment.sol#L56

- [Payment.sol#L59]https://github.com/code-423n4/2023-01-numoen/blob/c75b39dc93fe327039b097ba149e1abe90d3b2f5/src/periphery/Payment.sol#L59

- [Lendgine.sol#L116]https://github.com/code-423n4/2023-01-numoen/blob/c75b39dc93fe327039b097ba149e1abe90d3b2f5/src/core/Lendgine.sol#L116

- [Lendgine.sol#L202]https://github.com/code-423n4/2023-01-numoen/blob/c75b39dc93fe327039b097ba149e1abe90d3b2f5/src/core/Lendgine.sol#L202

- [LendgineRouter.sol#L166]https://github.com/code-423n4/2023-01-numoen/blob/c75b39dc93fe327039b097ba149e1abe90d3b2f5/src/periphery/LendgineRouter.sol#L166

- [LendgineRouter.sol#L236]https://github.com/code-423n4/2023-01-numoen/blob/c75b39dc93fe327039b097ba149e1abe90d3b2f5/src/periphery/LendgineRouter.sol#L236

- [Pair.sol#L122-L123]https://github.com/code-423n4/2023-01-numoen/blob/c75b39dc93fe327039b097ba149e1abe90d3b2f5/src/core/Pair.sol#L122-L123

- [Pair.sol#L102-L103]https://github.com/code-423n4/2023-01-numoen/blob/c75b39dc93fe327039b097ba149e1abe90d3b2f5/src/core/Pair.sol#L102-L103


### Description

The recipient of a transfer may be `address(0)`, leading to losing assets



# Informational

## Solidity compiler optimizations can be problematic

Protocol has enabled optional compiler optimizations in Solidity. There have been several optimization bugs with security implications. Moreover, optimizations are actively being developed. Solidity compiler optimizations are disabled by default, and it is unclear how many contracts in the wild actually use them.

Therefore, it is unclear how well they are being tested and exercised. High-severity security issues due to optimization bugs have occurred in the past. A high-severity bug in the emscripten-generated solc-js compiler used by Truffle and Remix persisted until late 2018. The fix for this bug was not reported in the Solidity CHANGELOG.

Another high-severity optimization bug resulting in incorrect bit shift results was patched in Solidity 0.5.6. More recently, another bug due to the incorrect caching of keccak256 was reported. A compiler audit of Solidity from November 2018 concluded that the optional optimizations may not be safe. It is likely that there are latent bugs related to optimization and that new bugs will be introduced due to future optimizations.

It's likely there is a latent or future bug in Solidity compiler optimizations—or in the Emscripten transpilation to solc-js—causes a security vulnerability in the contracts.
 
- [foundry.toml#L8](https://github.com/code-423n4/2023-01-numoen/blob/c75b39dc93fe327039b097ba149e1abe90d3b2f5/foundry.toml#L8)
optimizer = true


## Can be saved directly as bytes32

Given that is always going to be casted to bytes32 later, consider defining it directly as bytes32

- [LendgineAddress.sol#L7](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/libraries/LendgineAddress.sol#L7)
    71_695_300_681_742_793_458_567_320_549_603_773_755_938_496_017_772_337_363_704_152_556_600_186_974;


## Max value can be used rather than `2 ** n`

Rather than using 2 ** 256 or other numbers (128, 64, 32, 16, 8...), it can be used type(uint256).max (depending on the exponentiation number).

- [SafeCast.sol#L16](https://github.com/code-423n4/2023-01-numoen/blob/c75b39dc93fe327039b097ba149e1abe90d3b2f5/src/libraries/SafeCast.sol#L16)

### Recommendation

Consider replacing `2 ** n` notation to max value.



## Warning SPDX-License missing

SPDX license should be included for avoiding compiler warnings.

- [UniswapV2Library.sol#L1](https://github.com/code-423n4/2023-01-numoen/blob/c75b39dc93fe327039b097ba149e1abe90d3b2f5/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L1)


### Recommendation

Add a SPDX License to each file


## Use of hardcoded amount of days

Formula uses a hardcoded value of `365` (days) which would be wrong applied in a leap year (`366` days)

### Code Snippet

- [Lendgine.sol#L252](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Lendgine.sol#L252)
    uint256 dilutionLPRequested = (FullMath.mulDiv(borrowRate, _totalLiquidityBorrowed, 1e18) * timeElapsed) / 365 days;


### Recommendation

- Consider using an oracle for this
- Consider that that each year is like 365.25 not 365








## Different versions of pragma

`NOTE`: None of these findings where found by [4naly3er output - NC](LINKKKKKKK)

Some of the contracts include an unlocked pragma, e.g., `pragma solidity ^0.8.0`. 

Locking the pragma helps ensure that contracts are not accidentally deployed using an old compiler version with unfixed bugs.


- [Factory.sol#L2](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Factory.sol#L2)
pragma solidity ^0.8.4;

- [Lendgine.sol#L2](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Lendgine.sol#L2)
pragma solidity ^0.8.4;

- [LiquidityManager.sol#L2](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/LiquidityManager.sol#L2)
pragma solidity ^0.8.4;

- [LendgineRouter.sol#L2](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/LendgineRouter.sol#L2)
pragma solidity ^0.8.4;

- [ImmutableState.sol#L2](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/ImmutableState.sol#L2)
pragma solidity ^0.8.0;

- [JumpRate.sol#L2](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/JumpRate.sol#L2)
pragma solidity ^0.8.0;

- [Payment.sol#L2](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/Payment.sol#L2)
pragma solidity ^0.8.4;

- [SwapHelper.sol#L2](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/SwapHelper.sol#L2)
pragma solidity ^0.8.4;

- [Pair.sol#L2](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Pair.sol#L2)
pragma solidity ^0.8.4;

- [Balance.sol#L2](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/libraries/Balance.sol#L2)
pragma solidity ^0.8.4;

- [SafeCast.sol#L2](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/libraries/SafeCast.sol#L2)
pragma solidity ^0.8.0;

- [Position.sol#L2](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/libraries/Position.sol#L2)
pragma solidity ^0.8.4;


### Recommendation

Lock pragmas to a specific Solidity version. 


## Constants should be defined rather than using magic numbers

`NOTE`: None of these findings where found by [4naly3er output - NC](LINKKKKKKK)

- [UniswapV2Library.sol#L64](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913//src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L64)
    uint256 denominator = (reserveIn * 1000) + amountInWithFee;


- [UniswapV2Library.sol#L64](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913//src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L64)
    uint256 denominator = (reserveIn * 1000) + amountInWithFee;

- [UniswapV2Library.sol#L80](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913//src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L80)
    uint256 numerator = reserveIn * amountOut * 1000;

- [Lendgine.sol#L225](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913//src/core/Lendgine.sol#L225)
    return FullMath.mulDiv(collateral * token1Scale, 1e18, 2 * upperBound);

- [Lendgine.sol#L230](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913//src/core/Lendgine.sol#L230)
    return FullMath.mulDiv(liquidity, 2 * upperBound, 1e18) / token1Scale;

- [Lendgine.sol#L252](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913//src/core/Lendgine.sol#L252)
    uint256 dilutionLPRequested = (FullMath.mulDiv(borrowRate, _totalLiquidityBorrowed, 1e18) * timeElapsed) / 365 days;

- [Lendgine.sol#L257](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913//src/core/Lendgine.sol#L257)
    rewardPerPositionStored += FullMath.mulDiv(dilutionSpeculative, 1e18, totalPositionSize);

- [LiquidityManager.sol#L178](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913//src/periphery/LiquidityManager.sol#L178)
    position.tokensOwed += FullMath.mulDiv(position.size, rewardPerPositionPaid - position.rewardPerPositionPaid, 1e18);

- [LiquidityManager.sol#L214](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913//src/periphery/LiquidityManager.sol#L214)
    position.tokensOwed += FullMath.mulDiv(position.size, rewardPerPositionPaid - position.rewardPerPositionPaid, 1e18);

- [LiquidityManager.sol#L238](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913//src/periphery/LiquidityManager.sol#L238)
    position.tokensOwed += FullMath.mulDiv(position.size, rewardPerPositionPaid - position.rewardPerPositionPaid, 1e18);

- [JumpRate.sol#L17](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913//src/core/JumpRate.sol#L17)
      return (util * multiplier) / 1e18;

- [JumpRate.sol#L19](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913//src/core/JumpRate.sol#L19)
      uint256 normalRate = (kink * multiplier) / 1e18;

- [JumpRate.sol#L21](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913//src/core/JumpRate.sol#L21)
      return ((excessUtil * jumpMultiplier) / 1e18) + normalRate;

- [JumpRate.sol#L37](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913//src/core/JumpRate.sol#L37)
    return (borrowRate * util) / 1e18;

- [JumpRate.sol#L42](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913//src/core/JumpRate.sol#L42)
    return (borrowedLiquidity * 1e18) / totalLiquidity;

- [Pair.sol#L56](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913//src/core/Pair.sol#L56)
    uint256 scale0 = FullMath.mulDiv(amount0, 1e18, liquidity) * token0Scale;

- [Pair.sol#L57](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913//src/core/Pair.sol#L57)
    uint256 scale1 = FullMath.mulDiv(amount1, 1e18, liquidity) * token1Scale;

- [Pair.sol#L61](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913//src/core/Pair.sol#L61)
    uint256 a = scale0 * 1e18;

