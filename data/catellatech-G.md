# c4udit Numoen Gas Report

## Files analyzed
- 2023-01-numoen\src\core\ERC20.sol
- 2023-01-numoen\src\core\Factory.sol
- 2023-01-numoen\src\core\ImmutableState.sol
- 2023-01-numoen\src\core\JumpRate.sol
- 2023-01-numoen\src\core\Lendgine.sol
- 2023-01-numoen\src\core\Pair.sol
- 2023-01-numoen\src\core\ReentrancyGuard.sol
- 2023-01-numoen\src\core\libraries\Position.sol
- 2023-01-numoen\src\core\libraries\PositionMath.sol
- 2023-01-numoen\src\periphery\LendgineRouter.sol
- 2023-01-numoen\src\periphery\LiquidityManager.sol
- 2023-01-numoen\src\periphery\Multicall.sol
- 2023-01-numoen\src\periphery\Payment.sol
- 2023-01-numoen\src\periphery\SelfPermit.sol
- 2023-01-numoen\src\periphery\SwapHelper.sol
- 2023-01-numoen\src\periphery\UniswapV2\libraries\UniswapV2Library.sol
- 2023-01-numoen\src\periphery\libraries\LendgineAddress.sol


# Issues found


## Use != 0 instead of > 0 for Unsigned Integer Comparison

#### Impact
Issue Information: [G001]

#### Findings:
```
2023-01-numoen\src\core\Lendgine.sol::89 => if (totalSupply > 0 && totalLiquidityBorrowed == 0) revert CompleteUtilizationError();
2023-01-numoen\src\core\Lendgine.sol::142 => if (totalLiquiditySupplied == 0 && totalPositionSize > 0) revert CompleteUtilizationError();
2023-01-numoen\src\core\Lendgine.sol::200 => if (collateral > 0) {
2023-01-numoen\src\core\Pair.sol::102 => if (amount0 > 0) SafeTransferLib.safeTransfer(token0, to, amount0);
2023-01-numoen\src\core\Pair.sol::103 => if (amount1 > 0) SafeTransferLib.safeTransfer(token1, to, amount1);
2023-01-numoen\src\core\Pair.sol::122 => if (amount0Out > 0) SafeTransferLib.safeTransfer(token0, to, amount0Out);
2023-01-numoen\src\core\Pair.sol::123 => if (amount1Out > 0) SafeTransferLib.safeTransfer(token1, to, amount1Out);
2023-01-numoen\src\core\libraries\Position.sol::50 => if (_positionInfo.size > 0) {
2023-01-numoen\src\core\libraries\Position.sol::64 => if (tokensOwed > 0) positionInfo.tokensOwed = _positionInfo.tokensOwed + tokensOwed;

2023-01-numoen\src\periphery\LiquidityManager.sol::112 => if (decoded.amount0 > 0) pay(decoded.token0, decoded.payer, msg.sender, decoded.amount0);
2023-01-numoen\src\periphery\LiquidityManager.sol::113 => if (decoded.amount1 > 0) pay(decoded.token1, decoded.payer, msg.sender, decoded.amount1);
2023-01-numoen\src\periphery\Payment.sol::29 => if (balanceWETH > 0) {
2023-01-numoen\src\periphery\Payment.sol::39 => if (balanceToken > 0) {
2023-01-numoen\src\periphery\Payment.sol::45 => if (address(this).balance > 0) SafeTransferLib.safeTransferETH(msg.sender, address(this).balance);
2023-01-numoen\src\periphery\SwapHelper.sol::42 => SafeTransferLib.safeTransfer(tokenIn, msg.sender, amount0Delta > 0 ? uint256(amount0Delta) : uint256(amount1Delta));
2023-01-numoen\src\periphery\SwapHelper.sol::76 => amount = params.amount > 0
2023-01-numoen\src\periphery\SwapHelper.sol::81 => params.amount > 0 ? (uint256(params.amount), amount) : (amount, uint256(-params.amount));
2023-01-numoen\src\periphery\SwapHelper.sol::111 => if (params.amount > 0) {

```

## Use immutable for OpenZeppelin AccessControl's Roles Declarations

#### Impact
Issue Information: [G002]

#### Findings:
```
2023-01-numoen\src\core\ERC20.sol::44 => keccak256("Permit(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)");
2023-01-numoen\src\core\ERC20.sol::129 => bytes32 digest = keccak256(
2023-01-numoen\src\core\ERC20.sol::133 => keccak256(abi.encode(PERMIT_TYPEHASH, owner, spender, value, nonces[owner]++, deadline)),
```


## Use Shift Right/Left instead of Division/Multiplication if possible

#### Impact
Issue Information: [G003]

#### Findings:
```
2023-01-numoen\src\core\Pair.sol::63 => uint256 c = (scale1 * scale1) / 4;
```

## Splitting require() statements that use && saves gas
instead of using operator && on single require check (ERC20.sol line 139). using double require check can save more gas:

#### Impact
Issue Information: [G004] more expensive gas usage

#### Findings:
```
2023-01-numoen\src\core\ERC20.sol::139 => require(recoveredAddress != address(0) && recoveredAddress == owner, "INVALID_SIGNER");

```

Recommended Mitigation Steps:

```
require(recoveredAddress != address(0));
require(recoveredAddress == owner);

```

##  KECCAK256() SHOULD ONLY NEED TO BE CALLED ON A SPECIFIC STRING LITERAL ONCE
It should be saved to an immutable variable, and the variable used instead. If the hash is being used as a part of a function selector, the cast to bytes4 should also only be done once

#### Impact
Issue Information: [G005] more expensive gas usage

#### Findings:
```
2023-01-numoen\src\core\ERC20.sol::154 => keccak256("EIP712Domain(string name,string version,uint256 chainId,address verifyingContract)"),);

```

# <X> += <Y> COSTS MORE GAS THAN <X> = <X> + <Y> FOR STATE VARIABLES

#### Impact
Issue Information: [G006] more expensive gas usage

#### Findings:
```
2023-01-numoen\src\core\ERC20.sol::168 => totalSupply += amount;;
2023-01-numoen\src\core\ERC20.sol::173 => balanceOf[to] += amount; 
2023-01-numoen\src\core\Lendgine.sol::91  =>  totalLiquidityBorrowed += liquidity;
2023-01-numoen\src\core\LiquidityManager.sol::180  => position.size += size;
```