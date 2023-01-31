## [G-01] Using bools for storage incurs overhead

### Description

Use uint256 for true/false to avoid a Gwarmaccess (100 gas), and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past.

### Findings

- [src/periphery/SwapHelper.sol#L95](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/SwapHelper.sol#L95)
  ```Solidity
  bool zeroForOne = params.tokenIn < params.tokenOut;
  ```

### References

- [[GAS-1] Using bools for storage incurs overhead](https://gist.github.com/Picodes/ab2df52379e4b4993709be1b91aab651#gas-1-using-bools-for-storage-incurs-overhead)
- [OpenZeppelin Contracts](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27)

## [G-02] Split `require()` statements that use `&&` to save gas

### Description

It is advised to use two `require` instead of using one `require` with the operator `&&` to save gas.

### Findings

- [src/periphery/UniswapV2/libraries/UniswapV2Library.sol](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol)
```Solidity
::61 =>     require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");
::79 =>     require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");
```

### Resources
- [[G-04] SPLITTING REQUIRE() STATEMENTS THAT USE && SAVES GAS](https://code4rena.com/reports/2022-12-caviar/#g-04-splitting-require-statements-that-use--saves-gas)

## [G-03] Use `selfbalance()` instead of `address(this).balance`

The `BALANCE` opcode costs 100 GAS minimum, `SELFBALANCE` costs 5 GAS minimum.

### Description

### Findings

- [src/periphery/Payment.sol](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/Payment.sol)
```Solidity
::45 =>     if (address(this).balance > 0) SafeTransferLib.safeTransferETH(msg.sender, address(this).balance);
::53 =>     if (token == weth && address(this).balance >= value) {
```

### Resources

- [BALANCE | EVM Codes](https://www.evm.codes/#31?fork=merge)
- [SELFBALANCE | EVM Codes](https://www.evm.codes/#47?fork=merge)