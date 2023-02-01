# 1. Old solidity version

Almost all contracts use 0.8.4 Solidity version. Please update this contracts latest Solidity version.

# 2. Improper Use of Temporary Deployment Storage

Link : https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Factory.sol#L63

Summary: 

The contract `Factory` uses a temporary deployment storage called `parameters` to store important data before deployment. This storage is deleted right after deployment, making it impossible to retrieve or reference this data in the future.

Impact: 

This design choice has the potential to cause serious issues. For example, if an attacker were to call the `createLendgine` function after the parameters have been deleted, the function would revert and fail to execute. This could result in lost funds or other unintended consequences.

Recommendation: 

It is recommended to store important data such as the parameters in a persistent mapping or data structure to ensure that it can be accessed and referenced in the future.

Example: 

Instead of using a struct, the `parameters` could be stored in a `mapping`, like this:

```
mapping (bytes32 => Parameters) public parametersMap;

function createLendgine(
    address token0,
    address token1,
    uint8 token0Exp,
    uint8 token1Exp,
    uint256 upperBound
  )
    external
    override
    returns (address lendgine)
  {
    if (token0 == token1) revert SameTokenError();
    if (token0 == address(0) || token1 == address(0)) revert ZeroAddressError();
    if (getLendgine[token0][token1][token0Exp][token1Exp][upperBound] != address(0)) revert DeployedError();
    if (token0Exp > 18 || token0Exp < 6 || token1Exp > 18 || token1Exp < 6) revert ScaleError();

    parametersMap[keccak256(abi.encodePacked(token0, token1, token0Exp, token1Exp, upperBound))] = 
      Parameters({token0: token0, token1: token1, token0Exp: token0Exp, token1Exp: token1Exp, upperBound: upperBound});

    lendgine = address(new Lendgine{ salt: keccak256(abi.encode(token0, token1, token0Exp, token1Exp, upperBound)) }());

    getLendgine[token0][token1][token0Exp][token1Exp][upperBound] = lendgine;
    emit LendgineCreated(token0, token1, token0Exp, token1Exp, upperBound, lendgine);
  }
```
# 3. Incorrect `Burn` Function Reverts on Valid Inputs

Link: https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L105

Summary: 

The `burn` function in the `Lendgine` contract is not handling cases of 0 collateral, liquidity, or shares correctly, causing it to revert even when the input is valid.

Impact: 

The `burn` function is used to burn shares and get the collateral in return, but the incorrect handling of 0 inputs causes it to revert even when the input is valid, leading to user funds being stuck and the overall loss of functionality of the `Lendgine` contract.

Recommendation: 

The conditions in the `if` statement in the `burn` function should be changed to correctly handle the 0 inputs. For example, the check for `collateral == 0` should be changed to `collateral <= 0`. This will ensure that the function behaves correctly even when the input is 0 and prevent the issue of user funds being stuck.

Example:

```
  /// @inheritdoc ILendgine
  function burn(address to, bytes calldata data) external override nonReentrant returns (uint256 collateral) {
    _accrueInterest();

    uint256 shares = balanceOf[address(this)];
    uint256 liquidity = convertShareToLiquidity(shares);
    collateral = convertLiquidityToCollateral(liquidity);

    if (collateral <= 0 || liquidity <= 0 || shares <= 0) revert InputError();

    totalLiquidityBorrowed -= liquidity;
    _burn(address(this), shares);
    SafeTransferLib.safeTransfer(token1, to, collateral); // optimistically transfer
    mint(liquidity, data);

    emit Burn(msg.sender, collateral, shares, liquidity, to);
  }
```

# 4. Vulnerability in the Lendgine Contract

Link: https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L71

Summary: 

The Lendgine contract has a potential reentrancy vulnerability when using the `mint()` and `burn()` functions. The contract makes a call to `IMintCallback` and transfers the token balance before the `callback` function has been executed, which could result in an attacker being able to call `mint()` or `burn()` multiple times within the same transaction.

Impact: 

An attacker could exploit this vulnerability to repeatedly call `mint()` or `burn()` and drain the contract's balance. This would result in a significant loss of funds for the contract and its users.

Recommendation:

The recommended solution is to first transfer the balance and then call the `IMintCallback` function to mitigate the reentrancy vulnerability.

Example:

```
function mint(
  address to,
  uint256 collateral,
  bytes calldata data
)
  external
  override
  nonReentrant
  returns (uint256 shares)
{
  _accrueInterest();

  uint256 liquidity = convertCollateralToLiquidity(collateral);
  shares = convertLiquidityToShare(liquidity);

  if (collateral == 0 || liquidity == 0 || shares == 0) revert InputError();
  if (liquidity > totalLiquidity) revert CompleteUtilizationError();
  // next check is for the case when liquidity is borrowed but then was completely accrued
  if (totalSupply > 0 && totalLiquidityBorrowed == 0) revert CompleteUtilizationError();

  totalLiquidityBorrowed += liquidity;
  (uint256 amount0, uint256 amount1) = burn(to, liquidity);
  _mint(to, shares);

  uint256 balanceBefore = Balance.balance(token1);
  SafeTransferLib.safeTransfer(token1, to, collateral);
  IMintCallback(msg.sender).mintCallback(collateral, amount0, amount1, liquidity, data);
  uint256 balanceAfter = Balance.balance(token1);

  if (balanceAfter < balanceBefore + collateral) revert InsufficientInputError();

  emit Mint(msg.sender, collateral, shares, liquidity, to);
}
```

# 5. Reentrancy Vulnerability in LiquidityManager Contract

Link : https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L135

Summary: 

The LiquidityManager contract has a potential reentrancy vulnerability in its `addLiquidity` function. When adding liquidity, the contract makes a call to the `pay` function and passes `params.recipient` as the recipient address. If an attacker is able to control `params.recipient`, they can perform a reentrant attack by calling the `addLiquidity` function in a malicious contract.

Impact: 

An attacker could potentially steal funds from the contract by executing a reentrancy attack.

Recommendation: 

To prevent the reentrancy attack, the contract should check if `params.recipient` is a trusted address or not before executing the call to the pay function.

Example: 

Here is an example of how the vulnerability could be exploited:

```
contract Attacker {
  function attack(address liquidityManager) public {
    liquidityManager.call.value(10 ether)("0x00", "0x00", 0, 0, 0, 0, 10, 10, 0, address(this), 0);
  }
}

contract Main {
  function addLiquidity(address token0, address token1, uint256 token0Exp, uint256 token1Exp, uint256 upperBound, uint256 liquidity, uint256 amount0Min, uint256 amount1Min, uint256 sizeMin, address recipient, uint256 deadline) public payable {
    require(msg.value == 10 ether, "incorrect value");
    Attacker attacker = new Attacker();
    attacker.attack(address(this));
  }
}
```

In this example, Main contract is vulnerable to reentrancy attacks as it directly passes recipient address to `pay` function. An attacker can create a malicious contract and call the `addLiquidity` function with `address(this)` as the recipient address. The call will cause the funds in the contract to be transferred to the attacker's contract, and the attack function in the attacker's contract will be executed again, stealing more funds.

# 6. Contract Initialization Vulnerability in ImmutableState Contract

Link: https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/ImmutableState.sol#L27

Summary: 

The `ImmutableState` contract is vulnerable to a contract initialization attack. The code sets the factory address equal to the address of the contract creator, which can be a malicious actor. As a result, the malicious actor could provide incorrect parameter values for `token0, token1, token0Scale, token1Scale, and upperBound` through their own malicious implementation of the `Factory` contract.

Impact: 

The incorrect values provided by a malicious actor can lead to incorrect functionality of the contract, potentially leading to financial loss for users of the contract.

Recommendation: 

The contract should verify that the factory address is a trusted source before initializing the contract parameters. One way to do this would be to compare the factory address against a predefined list of trusted addresses.

Example:
```
pragma solidity ^0.8.0;

import { Factory } from "./Factory.sol";
import { IImmutableState } from "./interfaces/IImmutableState.sol";

abstract contract ImmutableState is IImmutableState {
  /// @inheritdoc IImmutableState
  address public immutable override factory;
  address constant trustedFactory = 0x...;  // Define a trusted factory address

  /// @inheritdoc IImmutableState
  address public immutable override token0;

  /// @inheritdoc IImmutableState
  address public immutable override token1;

  /// @inheritdoc IImmutableState
  uint256 public immutable override token0Scale;

  /// @inheritdoc IImmutableState
  uint256 public immutable override token1Scale;

  /// @inheritdoc IImmutableState
  uint256 public immutable override upperBound;

  constructor() {
    require(msg.sender == trustedFactory, "Unauthorized contract deployment");
    factory = msg.sender;

    uint128 _token0Exp;
    uint128 _token1Exp;

    (token0, token1, _token0Exp, _token1Exp, upperBound) = Factory(msg.sender).parameters();

    token0Scale = 10 ** (18 - _token0Exp);
    token1Scale = 10 ** (18 - _token1Exp);
  }
}
```

# 7. Overflow vulnerability in `getBorrowRate()` function

Link: https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol#L13

Summary: 

The `getBorrowRate()` function in the `JumpRate` contract is vulnerable to an overflow issue when the utilization rate is larger than the kink constant. This results in a incorrect return value, causing incorrect calculations of interest rates.

Impact: 

This vulnerability can result in incorrect calculation of interest rates for borrowers, leading to unintended financial losses for users of the contract.

Recommendation: 

To mitigate the overflow vulnerability, the implementation of the `getBorrowRate()` function should be revised to handle large utilization rates in a safer manner. An example of a safer implementation would be to use the `SafeMath` library to perform arithmetic operations and prevent overflow issues.

Example:
```
pragma solidity ^0.8.0;
import "https://github.com/OpenZeppelin/openzeppelin-solidity/contracts/math/SafeMath.sol";

import { IJumpRate } from "./interfaces/IJumpRate.sol";

abstract contract JumpRate is IJumpRate {
  using SafeMath for uint256;
  
  uint256 public constant override kink = 0.8 ether;

  uint256 public constant override multiplier = 1.375 ether;

  uint256 public constant override jumpMultiplier = 44.5 ether;

  function getBorrowRate(uint256 borrowedLiquidity, uint256 totalLiquidity) public pure override returns (uint256 rate) {
    uint256 util = utilizationRate(borrowedLiquidity, totalLiquidity);

    if (util <= kink) {
      return (util.mul(multiplier)) / 1e18;
    } else {
      uint256 normalRate = (kink.mul(multiplier)) / 1e18;
      uint256 excessUtil = util.sub(kink);
      return (excessUtil.mul(jumpMultiplier).div(1e18)).add(normalRate);
    }
  }
  // ...
}
```