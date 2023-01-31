## [L-01] Outdated Compiler Version

### Description

Using an older compiler version might be risky, especially if the version in question has faults and problems that have been made public.

### Findings

- https://github.com/code-423n4/2023-01-numoen/blob/main/src/libraries/Balance.sol => `^0.8.4`
- https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Factory.sol => `^0.8.4`
- https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/ImmutableState.sol => `^0.8.0`
- https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol => `^0.8.0`
- https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol => `^0.8.4`
- https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/libraries/LendgineAddress.sol => `>=0.5.0`
- https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LendgineRouter.sol => `^0.8.4`
- https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol => `^0.8.4`
- https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Pair.sol => `^0.8.4`
- https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/Payment.sol => `^0.8.4`
- https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/libraries/Position.sol => `^0.8.4`
- https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/libraries/PositionMath.sol => `>=0.5.0`
- https://github.com/code-423n4/2023-01-numoen/blob/main/src/libraries/SafeCast.sol => `^0.8.0`
- https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/SwapHelper.sol => `^0.8.4`
- https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol => `>=0.8.0`

### Resources

- [SWC-102](https://swcregistry.io/docs/SWC-102)
- [Etherscan Solidity Bug Info](https://etherscan.io/solcbuginfo)

## [L-02] SPDX-License-Identifier missing

### Description

Missing license agreements (SPDX-License-Identifier) may result in lawsuits and improper forms of use of code.

### Findings

- https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol => `The Solidity file is missing the SPDX-License-Identifier`

### References

- [Missing license identifier | UMA DVM 2.0 Audit](https://blog.openzeppelin.com/uma-dvm-2-0-audit/#missing-license-identifier)


## [L-03] Unnamed return parameters

### Description

To increase explicitness and readability, take into account introducing and utilizing named return parameters.

### Findings

- https://github.com/code-423n4/2023-01-numoen/blob/main/src/libraries/Balance.sol
  ```Solidity
  ::12 =>   function balance(address token) internal view returns (uint256) {
  ```
- https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol
  ```Solidity
  ::213 =>   function convertLiquidityToShare(uint256 liquidity) public view override returns (uint256) {
  ::219 =>   function convertShareToLiquidity(uint256 shares) public view override returns (uint256) {
  ::224 =>   function convertCollateralToLiquidity(uint256 collateral) public view override returns (uint256) {
  ::229 =>   function convertLiquidityToCollateral(uint256 liquidity) public view override returns (uint256) {
  ```
- https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Pair.sol
  ```Solidity
  ::53 =>   function invariant(uint256 amount0, uint256 amount1, uint256 liquidity) public view override returns (bool) {
  ```
- https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/libraries/Position.sol
  ```Solidity
  ::69 =>   function newTokensOwed(Position.Info memory position, uint256 rewardPerPosition) internal pure returns (uint256) {
  ```

### References

- [Unnamed return parameters | Opyn Bull Strategy Contracts Audit](https://blog.openzeppelin.com/opyn-bull-strategy-contracts-audit/#unnamed-return-parameters)


## [L-04] Avoid using abi.encodePacked() with dynamic types when passing the result to a hash function

### Description

Instead of using `abi.encodePacked()` use `abi.encode()`. It will pad items to 32 bytes, which will prevent [hash collisions](https://docs.soliditylang.org/en/v0.8.13/abi-spec.html#non-standard-packed-mode).

It is possible to cast to `bytes()` or `bytes32()` in place of `abi.encodePacked()` when there is just one parameter, see "[how to compare strings in solidity?](https://ethereum.stackexchange.com/questions/30912/how-to-compare-strings-in-solidity#answer-82739)". `bytes.concat()` should be used if all parameters are strings or bytes.

### Findings

- [src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L26](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L26)
```Solidity
keccak256(abi.encodePacked(token0, token1)),
```

### Resources

- [[L-1] abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as keccak256()](https://gist.github.com/GalloDaSballo/39b929e8bd48704b9d35b448aaa29480#l-1--abiencodepacked-should-not-be-used-with-dynamic-types-when-passing-the-result-to-a-hash-function-such-as-keccak256)