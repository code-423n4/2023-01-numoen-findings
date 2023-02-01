## [N-1] LOCK PRAGMAS TO SPECIFIC COMPILER VERSION
Description:
Pragma statements can be allowed to float when a contract is intended for consumption by other developers, as in the case with contracts in a library or EthPM package. Otherwise, the developer would need to manually update the pragma in order to compile locally.
https://swcregistry.io/docs/SWC-103
\
Recommendation:
Ethereum Smart Contract Best Practices - Lock pragmas to specific compiler version.
[solidity-specific/locking-pragmas](https://consensys.github.io/smart-contract-best-practices/development-recommendations/solidity-specific/locking-pragmas/)
Affected : 15 Files
```
pragma solidity ^0.8.4;
src/core/Factory.sol 
src/core/Lendgine.sol
src/core/Pair.sol
src/core/libraries/Position.sol
src/periphery/LendgineRouter.sol
src/periphery/LiquidityManager.sol 
src/periphery/Payment.sol
src/periphery/SwapHelper.sol
src/libraries/Balance.sol

pragma solidity ^0.8.0;
src/core/ImmutableState.sol
src/core/JumpRate.sol
src/libraries/SafeCast.sol

pragma solidity >=0.8.0;
src/periphery/UniswapV2/libraries/UniswapV2Library.sol

pragma solidity >=0.5.0;
src/core/libraries/PositionMath.sol
src/periphery/libraries/LendgineAddress.sol


```
## [N-2] USE A MORE RECENT VERSION OF SOLIDITY
 Description:
    For security, it is best practice to use the latest Solidity version.
    For the security fix list in the versions;
    https://github.com/ethereum/solidity/blob/develop/Changelog.md
    Recommendation:
    Old version of Solidity is used (>=0.5.10,^0.8.0,^0.8.4), newer version can be used (0.8.17)
## [N-3] LINES ARE TOO LONG
    Description:
    Usually lines in source code are limited to 80 characters. Today’s screens are much larger so it’s reasonable to stretch this in some cases. 
   Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over 164 characters, the 
   lines below should be split when they reach that length.
   Reference: https://docs.soliditylang.org/en/v0.8.10/style-guide.html#maximum-line-length
 
```
src/periphery/LendgineRouter.sol
194: factory, decoded.token0, decoded.token1, decoded.token0Exp, decoded.token1Exp, decoded.upperBound
257: function burn(BurnParams calldata params) external payable checkDeadline(params.deadline) returns (uint256 amount) {   
142:  function mint(MintParams calldata params) external payable checkDeadline(params.deadline) returns (uint256 shares) {

src/periphery/LiquidityManager.sol
135: function addLiquidity(AddLiquidityParams calldata params) external payable checkDeadline(params.deadline) {
178: position.tokensOwed += FullMath.mulDiv(position.size, rewardPerPositionPaid - position.rewardPerPositionPaid, 1e18);
201:  function removeLiquidity(RemoveLiquidityParams calldata params) external payable checkDeadline(params.deadline) {
214:   position.tokensOwed += FullMath.mulDiv(position.size, rewardPerPositionPaid - position.rewardPerPositionPaid, 1e18);
238: position.tokensOwed += FullMath.mulDiv(position.size, rewardPerPositionPaid - position.rewardPerPositionPaid, 1e18);

src/core/Factory.sol
80:  Parameters({token0: token0, token1: token1, token0Exp: token0Exp, token1Exp: token1Exp, upperBound: upperBound});
82:  lendgine = address(new Lendgine{ salt: keccak256(abi.encode(token0, token1, token0Exp, token1Exp, upperBound)) }());

src/core/Lendgine.sol
105: function burn(address to, bytes calldata data) external override nonReentrant returns (uint256 collateral) {
194: function collect(address to, uint256 collateralRequested) external override nonReentrant returns (uint256 collateral) {
252: uint256 dilutionLPRequested = (FullMath.mulDiv(borrowRate, _totalLiquidityBorrowed, 1e18) * timeElapsed) / 365 days;

src/core/JumpRate.sol
13: function getBorrowRate(uint256 borrowedLiquidity, uint256 totalLiquidity) public pure override returns (uint256 rate) {
40: function utilizationRate(uint256 borrowedLiquidity, uint256 totalLiquidity) private pure returns (uint256 rate) {

src/core/Pair.sol
53:  function invariant(uint256 amount0, uint256 amount1, uint256 liquidity) public view override returns (bool) {
116: function swap(address to, uint256 amount0Out, uint256 amount1Out, bytes calldata data) external override nonReentrant {
  ```
## [N-3] 0 ADDRESS CHECK
Description: check of the address to protect the code from 0x0 address problem just in case. This is best practice or instead of suggesting that they verify address != 0x0, NatSpec comments could be  added explaining what is valid and what is invalid and what are the implications of accidentally using an invalid address.
Context:
[LendgineRouter.sol#L49-L59](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LendgineRouter.sol#L49-L59)
[LiquidityManager.sol#L75-L77](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L75-L77)
[Payment.sol#L17-L19](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/Payment.sol#L17-L19)
Recommendation:
Add code like this;
if (_factory== address(0)) revert ADDRESS_ZERO();

## [N-4]Function is marked with `returns (uint256 shares)` but it returns nothing
Context: 
[Lendgine.sol#L79-L102](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L79-L102)
                [Lendgine.sol#L123-L149](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L123-L149)
                [Lendgine.sol#L152-L180](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L152-L180)


## [S-1] GENERATE PERFECT CODE HEADERS EVERY TIME
Description:
I recommend using header for Solidity code layout and readability

https://github.com/transmissions11/headers
```
/*//////////////////////////////////////////////////////////////
                           TESTING 123
//////////////////////////////////////////////////////////////*/
```
## [S-2]USE A SINGLE FILE FOR ALL SYSTEM-WIDE CONSTANTS
Description:
 There are many addresses and constants used in the system. It is recommended to put the most used ones in one file (for example constants.sol, use inheritance to access these values)
 This will help with readability and easier maintenance for future changes. This also helps with any issues, as some of these hard-coded values are admin addresses.

Use and import this file in contracts that require access to these values. This is just a suggestion, in some use cases this may result in higher gas usage in the distribution.
 
Context:
```
src/periphery/libraries/LendgineAddress.sol
6:  uint256 internal constant INIT_CODE_HASH =
7:    71_695_300_681_742_793_458_567_320_549_603_773_755_938_496_017_772_337_363_704_152_556_600_186_974;

src/core/JumpRate.sol
7:  uint256 public constant override kink = 0.8 ether;

9: uint256 public constant override multiplier = 1.375 ether;

11: uint256 public constant override jumpMultiplier = 44.5 ether;

```
