# [QA] - FIX FAILING TESTS
All tests should pass on the project. Currently there is a test that is failing.

# [QA] - INSUFFICIENT COVERAGE

## Description:
The test coverage rate of the project is 40%. Testing all functions is best practice in terms of security criteria.

Due to its capacity, test coverage is expected to be 100%.

# [QA] - LOCK PRAGMAS TO SPECIFIC COMPILER VERSION

## Description:
Pragma statements can be allowed to float when a contract is intended for consumption by other developers, as in the case with contracts in a library or EthPM package. Otherwise, the developer would need to manually update the pragma in order to compile locally.
https://swcregistry.io/docs/SWC-103

## Where
* [/src/core/Factory.sol/#L2](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Factory.sol/#L2)
* [/src/core/Lendgine.sol/#L2](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Lendgine.sol/#L2)
* [/src/periphery/LiquidityManager.sol/#L2](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/LiquidityManager.sol/#L2)
* [/src/periphery/LendgineRouter.sol/#L2](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/LendgineRouter.sol/#L2)
* [/src/core/ImmutableState.sol/#L2](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/ImmutableState.sol/#L2)
* [/src/core/JumpRate.sol/#L2](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/JumpRate.sol/#L2)
* [/src/periphery/Payment.sol/#L2](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/Payment.sol/#L2)
* [/src/periphery/SwapHelper.sol/#L2](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/SwapHelper.sol/#L2)
* [/src/core/Pair.sol/#L2](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Pair.sol/#L2)
* [/src/core/libraries/PositionMath.sol/#L2](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/libraries/PositionMath.sol/#L2)
* [/src/libraries/Balance.sol/#L2](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/libraries/Balance.sol/#L2)
* [/src/libraries/SafeCast.sol/#L2](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/libraries/SafeCast.sol/#L2)
* [/src/periphery/libraries/LendgineAddress.sol/#L2](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/libraries/LendgineAddress.sol/#L2)
* [/src/core/libraries/Position.sol/#L2](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/libraries/Position.sol/#L2)
* [/src/periphery/UniswapV2/libraries/UniswapV2Library.sol/#L1](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/UniswapV2/libraries/UniswapV2Library.sol/#L1)

## Recommendation:
Ethereum Smart Contract Best Practices - Lock pragmas to specific compiler version.
solidity-specific/locking-pragmas

# [QA] - USE A HASHED KEY FOR `getLedgine` in `Factory.sol` INSTEAD OF A COMPLEX NESTED MAPPING 

## Description
It would be more gas efficient and more performant to use a HASH dictionary instead of a nested mapping for Lendgines addresses tracking.

## Where
[/src/core/Factory.sol/#L38-L41](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Factory.sol/#L38-L41)

## Recommendation
Use an auxiliary function that will create an unique hash key for each Lendinge and use it to save the address to the new Market. 

```diff
    contract Factory is IFactory {
        /// @inheritdoc IFactory
-       mapping(address => mapping(address => mapping(uint256 => mapping(uint256 => mapping(uint256 => address)))))
+       mapping(bytes32 => address)
          public
          override getLendgine;

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

+         bytes32 lendgineHashKey = _getLendgineHashKey(
+           address token0,
+           address token1,
+           uint8 token0Exp,
+           uint8 token1Exp,
+           uint256 upperBound
+         );

-         if (getLendgine[token0][token1][token0Exp][token1Exp][upperBound] != address(0)) revert DeployedError();
+         if (getLendgine[lendgineHashKey] != address(0)) revert DeployedError();
          if (token0Exp > 18 || token0Exp < 6 || token1Exp > 18 || token1Exp < 6) revert ScaleError();

          parameters =
            Parameters({token0: token0, token1: token1, token0Exp: token0Exp, token1Exp: token1Exp, upperBound: upperBound});

          lendgine = address(new Lendgine{ salt: keccak256(abi.encode(token0, token1, token0Exp, token1Exp, upperBound)) }());

          delete parameters;

-         getLendgine[token0][token1][token0Exp][token1Exp][upperBound] = lendgine;
+         getLendgine[lendgineHashKey] = lendgine;
          emit LendgineCreated(token0, token1, token0Exp, token1Exp, upperBound, lendgine);
        }

+       function _getLendgineHashKey(
+         address token0,
+         address token1,
+         uint8 token0Exp,
+         uint8 token1Exp,
+         uint256 upperBound
+       ) internal pure returns (bytes32) {
+         return keccak256(abi.encode(token0, token1, token0Exp, token1Exp, upperBound));
+       }
    }
```

# [QA] - REMOVE `parameters` STORAGE PROPERTY `createLendgine()`

## Description
On `Factory.sol`, a `parameters` storage property is set and destroyed every time a new Lendgine is deployed. The Lendgine reads this property on construction time and it uses those properties. A better approach would be to create a temp memory variable Parameters and to pass this object to the Lendgine constructor. This will reduce the gas used and will remove an extra external call. Also, will simplify the factory implementation.

## Where
[/src/core/Factory.sol/#L56](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Factory.sol/#L56)

## Recommendation
Create a temp memory variable `parameters` in `createLendgine` and pass this object to the Lendgine constructor. After the execution is completed this variable will be removed.

[/src/core/Factory.sol](`https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Factory.sol`)

```diff
    contract Factory is IFactory {
-     Parameters public override parameters;      

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
-       parameters =
+       Parameters memory parameters =
          Parameters({token0: token0, token1: token1, token0Exp: token0Exp, token1Exp: token1Exp, upperBound: upperBound});
        ...
        
-       lendgine = address(new Lendgine{ salt: keccak256(abi.encode(token0, token1, token0Exp, token1Exp, upperBound)) }());
+       lendgine = address(new Lendgine{ salt: keccak256(abi.encode(token0, token1, token0Exp, token1Exp, upperBound)) }(parameters));      

-       delete parameters;        
      }
    }
```

[/src/core/ImmutableState.sol](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/ImmutableState.sol)
```diff
-   import { Factory } from "./Factory.sol";
+   import { Factory, Parameters } from "./Factory.sol";

    abstract contract ImmutableState is IImmutableState {
-     constructor() {
+     constructor(Parameters calldata parameters) 
        factory = msg.sender;

-       uint128 _token0Exp;
-       uint128 _token1Exp;

-       (token0, token1, _token0Exp, _token1Exp, upperBound) = Factory(msg.sender).parameters();
+       token0 = parameters.token0;
+       token1 = parameters.token1;
+       upperBound = parameters.upperBound;

-       token0Scale = 10 ** (18 - _token0Exp);
-       token1Scale = 10 ** (18 - _token1Exp);
+       token0Scale = 10 ** (18 - parameters.token0Exp);
+       token1Scale = 10 ** (18 - parameters.token1Exp);
      }
    }
```

# [QA] - MISSING INDEXED ARGUMENTS ON EVENTS

## Description
Events should have indexed arguments to facilitate off chain tracking and searching.

## Where
* [/src/core/Pair.sol/#L21](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/Pair.sol/#L21)

## Recommendation
Recommend adding an indexed argument for `Mint`.

# [QA] - ADD COMMENTS WITH NAMED NESTED MAPPING KEYS
## Description
When a nested mapping is used it's useful to add a comment naming all the nested keys.

## Where
[/src/periphery/LiquidityManager.sol/#L69](https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/LiquidityManager.sol/#L69)

## Recommendation
Add a comment with a detail of every nested key name

# [QA] - USE `Clones.sol` PATTERN ON FACTORY FOR LENDGINE CREATION - It's using create2

## Description
A better approach for the Factory functionality would be to use [`Clones.sol`](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/proxy/Clones.sol). These will reduce the gas consumption on every Lendgine deployment

## Recommendation
Implement Clones.sol or [clones-with-immutable-args](https://github.com/wighawag/clones-with-immutable-args) for Lendgine deployment.
