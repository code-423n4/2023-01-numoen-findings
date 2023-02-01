## Summary
_This report does not include publicly known issues_
### Gas Optimizations
| |Issue|Instances|
|-|:-|:-:|
| [GAS-01] | `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables | 4 | 
| [GAS-02] | Splitting `require()` statements that use `&&` saves gas | 2 | 
| [GAS-03] | Setting the `constructor` to `payable` | 4 |
| [GAS-04] | Unused named return variables | 3 |


Total: 13 instances over 4 issues


## Gas Optimizations
### [GAS-01] `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables
Using the addition operator instead of plus-equals saves **[113 gas](https://gist.github.com/MiniGlome/f462d69a30f68c89175b0ce24ce37cae)**
Same for `-=`, `*=` and `/=`.
```solidity
File: Lendgine.sol
L91:            totalLiquidityBorrowed += liquidity;
L114:           totalLiquidityBorrowed -= liquidity;
L176:           totalPositionSize -= size;
L257:           rewardPerPositionStored += FullMath.mulDiv(dilutionSpeculative, 1e18, totalPositionSize);
```
[Link to code](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol)

### [GAS-02]  Splitting `require()` statements that use `&&` saves gas
Instead of using operator `&&` on a single `require`. Using a two `require` can save more gas.
i.e. for `require(version == 1 && _tokenAmount > 0, "nope");` use:
```solidity
require(version == 1);
require(_tokenAmount > 0);
```
```solidity
File: UniswapV2Library.sol

L61:        require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");
L79:        require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");
```
[Link to code](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol)

### [GAS-03]  Setting the `constructor` to `payable`
Saves ~13 gas per instance
```solidity
File: LiquidityManager.sol

L75:           constructor(address _factory, address _weth) Payment(_weth) {
```
[Link to code](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L75)
```solidity
File: LendingRouter.sol

L49:           constructor(
        address _factory,
        address _uniswapV2Factory,
        address _uniswapV3Factory,
        address _weth
        )
        SwapHelper(_uniswapV2Factory, _uniswapV3Factory)
        Payment(_weth)
        {
```
[Link to code](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LendingRouter.sol#L49)
```solidity
File: Payment.sol

L17:           constructor(address _weth) {
```
[Link to code](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/Payment.sol#L17)
```solidity
File: SwapHelper.sol

L29:           constructor(address _uniswapV2Factory, address _uniswapV3Factory) {
```
[Link to code](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/SwapHelper.sol#L29)


### [GAS-04]  Unused named return variables
Not using the named return variables when function returns, wastes deployment gas
```
File: JumpRate.sol

L13:        function getBorrowRate(uint256 borrowedLiquidity, uint256 totalLiquidity) public pure override returns (uint256 rate) {
        //...
L17:        return (util * multiplier) / 1e18;
        //...
L21:        return ((excessUtil * jumpMultiplier) / 1e18) + normalRate;

L25:        function getSupplyRate(
    uint256 borrowedLiquidity,
    uint256 totalLiquidity
    )
    external
    pure
    override
    returns (uint256 rate)
    {
        //...
L37:        return (borrowRate * util) / 1e18;

L40:        function utilizationRate(uint256 borrowedLiquidity, uint256 totalLiquidity) private pure returns (uint256 rate) {
        //...
L42:        return (borrowedLiquidity * 1e18) / totalLiquidity;
```
[Link to code](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol)
