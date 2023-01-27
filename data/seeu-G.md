## Using bools for storage incurs overhead

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