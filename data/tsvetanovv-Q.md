### QA Issues List
| Number |Issues Details
|:--:|:-------|:--:|
|[QA-01]| Use latest Solidity version
|[QA-02]| Use stable pragma statement
|[QA-03]| Different pragma directives are used
|[QA-04]| Constant variables should be written in UPPERCASE
|[QA-05]| Missing zero address validation
|[QA-06]| Loss of precision due to rounding
|[QA-07]| Missing uint256 `0` check in `JumpRate.sol` contract
|[QA-08]| Unsafe casting from uint160 to uint256
***

## [QA-01] Use latest Solidity version

Solidity pragma versioning should be upgraded to latest available version.
***

## [QA-02] Use stable pragma statement

Using a floating pragma statement (`^0.8.4`, `^0.8.0`) is discouraged as code can compile to different bytecodes with different compiler versions. Use a stable pragma statement to get a deterministic bytecode.
***

## [QA-03] Different pragma directives are used

Use one Solidity version on each contract.
***

## [QA-04] Constant variables should be written in UPPERCASE

For better readability of the code constant variables should be written in UPPERCASE
```
JumpRate.sol:
7: uint256 public constant override kink = 0.8 ether;
9: uint256 public constant override multiplier = 1.375 ether;
11: uint256 public constant override jumpMultiplier = 44.5 ether;
```
***

## [QA-05] Missing zero address validation

Setters of address type parameters should include a zero-address check otherwise contract functionality may become inaccessible or tokens burnt forever.

```
Lendgine.sol
71: function mint(
72:    	address to,
105: function burn(address to, bytes calldata data) external override nonReentrant returns (uint256 collateral) {
123: function deposit(
124:     address to,

Payment.sol:
25: function unwrapWETH(uint256 amountMinimum, address recipient) public payable {
35: function sweepToken(address token, uint256 amountMinimum, address recipient) public payable {
44: function refundETH() external payable {
52: function pay(address token, address payer, address recipient, uint256 value) internal {
```

#### Recommendation

Check that the address is not zero.
***

## [QA-06] LOSS OF PRECISION DUE TO ROUNDING
```
JumpRate.sol

16: if (util <= kink) {  // @audit-ok always be 18 decimals
17:      return (util * multiplier) / 1e18;
18:    } else {
19:      uint256 normalRate = (kink * multiplier) / 1e18;
20:      uint256 excessUtil = util - kink;
21:      return ((excessUtil * jumpMultiplier) / 1e18) + normalRate;
...
37: return (borrowRate * util) / 1e18;
42: return (borrowedLiquidity * 1e18) / totalLiquidity;
```
***

## [QA-07] Missing uint256 `0` check in `JumpRate.sol` contract

```
23: function getBorrowRate(uint256 borrowedLiquidity, uint256 totalLiquidity) public pure override returns (uint256 rate) {
25: function getSupplyRate(
    uint256 borrowedLiquidity,
    uint256 totalLiquidity
  )
40: function utilizationRate(uint256 borrowedLiquidity, uint256 totalLiquidity) private pure returns (uint256 rate) {
```

Make sure the entered number is not 0, because this may cause a miscalculation or zero devision error.
***

# [QA-08] Unsafe casting from uint160 to uint256
## Vulnerability Detail
In `LendgineAddress.sol`  and `UniswapV2Library.sol` we have unsafe casting from uint160 to uint256, which can lead to serious problems

## Code Snippet

```
LendgineAddress.sol
21:  lendgine = address(
22:      uint160( 
23:        uint256(
24:          keccak256(
25:            abi.encodePacked(
26:              hex"ff",
27:              factory,
28:              keccak256(abi.encode(token0, token1, token0Exp, token1Exp, upperBound)),
29:              bytes32(INIT_CODE_HASH)
30:            )
31:          )
32:        )
33:      )
34:    );
```

```
UniswapV2Library.sol
17: function pairFor(address factory, address tokenA, address tokenB) internal pure returns (address pair) {
18:   (address token0, address token1) = sortTokens(tokenA, tokenB);
19:       pair = address(
20:	      uint160( // extra cast for newer solidity
21:	        uint256(
```
## Recommendation

SafeCast library must be used in every type cast.
***