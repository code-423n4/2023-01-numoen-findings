### Unspecific Compiler Version Pragma

#### Impact
Issue Information: 
Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

Example
🤦 Bad:

pragma solidity ^0.8.0;
🚀 Good:

pragma solidity 0.8.4;

#### Findings:
```
2023-01-numoen/lib/forge-std/src/Base.sol::2 => pragma solidity >=0.6.2 <0.9.0;
2023-01-numoen/lib/forge-std/src/Script.sol::2 => pragma solidity >=0.6.2 <0.9.0;
2023-01-numoen/lib/forge-std/src/StdAssertions.sol::2 => pragma solidity >=0.6.2 <0.9.0;
2023-01-numoen/lib/forge-std/src/StdChains.sol::2 => pragma solidity >=0.6.2 <0.9.0;
2023-01-numoen/lib/forge-std/src/StdCheats.sol::2 => pragma solidity >=0.6.2 <0.9.0;
2023-01-numoen/lib/forge-std/src/StdError.sol::3 => pragma solidity >=0.6.2 <0.9.0;
2023-01-numoen/lib/forge-std/src/StdJson.sol::2 => pragma solidity >=0.6.0 <0.9.0;
2023-01-numoen/lib/forge-std/src/StdMath.sol::2 => pragma solidity >=0.6.2 <0.9.0;
2023-01-numoen/lib/forge-std/src/StdStorage.sol::2 => pragma solidity >=0.6.2 <0.9.0;
2023-01-numoen/lib/forge-std/src/StdUtils.sol::2 => pragma solidity >=0.6.2 <0.9.0;
2023-01-numoen/lib/forge-std/src/Vm.sol::2 => pragma solidity >=0.6.2 <0.9.0;
2023-01-numoen/lib/forge-std/src/console.sol::2 => pragma solidity >=0.4.22 <0.9.0;
2023-01-numoen/lib/forge-std/src/console2.sol::2 => pragma solidity >=0.4.22 <0.9.0;
2023-01-numoen/lib/forge-std/src/interfaces/IERC1155.sol::2 => pragma solidity >=0.6.2;
2023-01-numoen/lib/forge-std/src/interfaces/IERC165.sol::2 => pragma solidity >=0.6.2;
2023-01-numoen/lib/forge-std/src/interfaces/IERC20.sol::2 => pragma solidity >=0.6.2;
2023-01-numoen/lib/forge-std/src/interfaces/IERC4626.sol::2 => pragma solidity >=0.6.2;
2023-01-numoen/lib/forge-std/src/interfaces/IERC721.sol::2 => pragma solidity >=0.6.2;
2023-01-numoen/src/core/Factory.sol::2 => pragma solidity ^0.8.4;
2023-01-numoen/src/core/ImmutableState.sol::2 => pragma solidity ^0.8.0;
2023-01-numoen/src/core/JumpRate.sol::2 => pragma solidity ^0.8.0;
2023-01-numoen/src/core/Lendgine.sol::2 => pragma solidity ^0.8.4;
2023-01-numoen/src/core/Pair.sol::2 => pragma solidity ^0.8.4;
2023-01-numoen/src/core/libraries/Position.sol::2 => pragma solidity ^0.8.4;
2023-01-numoen/src/libraries/Balance.sol::2 => pragma solidity ^0.8.4;
2023-01-numoen/src/libraries/SafeCast.sol::2 => pragma solidity ^0.8.0;
2023-01-numoen/src/periphery/LendgineRouter.sol::2 => pragma solidity ^0.8.4;
2023-01-numoen/src/periphery/LiquidityManager.sol::2 => pragma solidity ^0.8.4;
2023-01-numoen/src/periphery/Payment.sol::2 => pragma solidity ^0.8.4;
2023-01-numoen/src/periphery/SwapHelper.sol::2 => pragma solidity ^0.8.4;
```
#### Tools used
Manual VS Code audit