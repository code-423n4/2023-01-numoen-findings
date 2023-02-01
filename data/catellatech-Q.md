# c4udit Numoen QA Report

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

# FILE IS MISSING NATSPEC

#### Impact
Issue Information: [N001]
#### Findings:
```
2023-01-numoen\src\core\Factory.sol
2023-01-numoen\src\core\JumpRate.sol
2023-01-numoen\src\core\Lendgine.sol
2023-01-numoen\src\core\Pair.sol
2023-01-numoen\src\periphery\LendgineRouter.sol
2023-01-numoen\src\periphery\LiquidityManager.sol
```

## Unspecific Compiler Version Pragma

#### Impact
Issue Information: [L001]
#### Findings:
```
2023-01-numoen\src\core\ERC20.sol::2 => pragma solidity >=0.8.0;
2023-01-numoen\src\core\Factory.sol::2 => pragma solidity ^0.8.4;
2023-01-numoen\src\core\ImmutableState.sol::2 => pragma solidity ^0.8.0;
2023-01-numoen\src\core\JumpRate.sol::2 => pragma solidity ^0.8.0;
2023-01-numoen\src\core\Lendgine.sol::2 => pragma solidity ^0.8.4;
2023-01-numoen\src\core\Pair.sol::2 => pragma solidity ^0.8.4;
2023-01-numoen\src\core\ReentrancyGuard.sol::2 => pragma solidity >=0.8.0;
2023-01-numoen\src\core\libraries\Position.sol::2 => pragma solidity ^0.8.4;
2023-01-numoen\src\core\libraries\PositionMath.sol::2 => pragma solidity >=0.5.0;

2023-01-numoen\src\periphery\LendgineRouter.sol::2 => pragma solidity ^0.8.4;
2023-01-numoen\src\periphery\LiquidityManager.sol::2 => pragma solidity ^0.8.4;
2023-01-numoen\src\periphery\Multicall.sol::2 => pragma solidity ^0.8.0;
2023-01-numoen\src\periphery\Payment.sol::2 => pragma solidity ^0.8.4;
2023-01-numoen\src\periphery\SelfPermit.sol::2 => pragma solidity ^0.8.0;
2023-01-numoen\src\periphery\SwapHelper.sol::2 => pragma solidity ^0.8.4;
2023-01-numoen\src\periphery\UniswapV2\libraries\UniswapV2Library.sol::1 => pragma solidity >=0.8.0;
2023-01-numoen\src\periphery\libraries\LendgineAddress.sol::2 => pragma solidity >=0.5.0;
```

