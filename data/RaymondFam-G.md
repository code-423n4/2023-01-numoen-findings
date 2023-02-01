## `||` costs less gas than its equivalent `&&`
Rule of thumb: `(x && y)` is `(!(!x || !y))`

Even with the 10k Optimizer enabled: `||`, OR conditions cost less than their equivalent `&&`, AND conditions.

As an example, the instance below may be refactored as follows:

[File: Lendgine.sol#L89](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L89)

```diff
-    if (totalSupply > 0 && totalLiquidityBorrowed == 0) revert CompleteUtilizationError();
+    if (!(totalSupply == 0 || totalLiquidityBorrowed != 0)) revert CompleteUtilizationError();
```
## Use storage instead of memory for structs/arrays
A storage pointer is cheaper since copying a state struct in memory would incur as many SLOADs and MSTOREs as there are slots. In another words, this causes all fields of the struct/array to be read from storage, incurring a Gcoldsload (2100 gas) for each field of the struct/array, and then further incurring an additional MLOAD rather than a cheap stack read. As such, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables will be much cheaper, involving only Gcoldsload for all associated field reads. Read the whole struct/array into a memory variable only when it is being returned by the function, passed into a function that requires memory, or if the array/struct is being read from another memory array/struct.

For instance, the specific instance below may be refactored as follows:

[File: Lendgine.sol#L167](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L167)

```diff
-    Position.Info memory positionInfo = positions[msg.sender]; // SLOAD
+    Position.Info storage positionInfo = positions[msg.sender]; // SLOAD
```
## += and -= cost more gas
`+=` and `-=` generally cost 22 more gas than writing out the assigned equation explicitly. The amount of gas wasted can be quite sizable when repeatedly operated in a loop.

For instance, the `+=` instance below may be refactored as follows:

[File: Lendgine.sol#L257](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L257)

```diff
-    rewardPerPositionStored += FullMath.mulDiv(dilutionSpeculative, 1e18, totalPositionSize);
+    rewardPerPositionStored = rewardPerPositionStored + FullMath.mulDiv(dilutionSpeculative, 1e18, totalPositionSize);
```
## Ternary over `if ... else`
Using ternary operator instead of the if else statement saves gas.

Here is a specific instance entailed:

[File: LiquidityManager.sol#L147-L153](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L147-L153)

```solidity
    if (totalLiquidity == 0) {
      amount0 = params.amount0Min;
      amount1 = params.amount1Min;
    } else {
      amount0 = FullMath.mulDivRoundingUp(params.liquidity, r0, totalLiquidity);
      amount1 = FullMath.mulDivRoundingUp(params.liquidity, r1, totalLiquidity);
    }
```
## Private function with embedded modifier reduces contract size
Consider having the logic of a modifier embedded through a private function to reduce contract size if need be. A private visibility that saves more gas on function calls than the internal visibility is adopted because the modifier will only be making this call inside the contract.

For instance, the modifier instance below may be refactored as follows:

[File: LendgineRouter.sol#L65-L68](https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LendgineRouter.sol#L65-L68)

```diff
+    function _checkDeadline() private view {
+        if (deadline < block.timestamp) revert LivelinessError();
+    }

    modifier checkDeadline() {
-        if (deadline < block.timestamp) revert LivelinessError();
+        _checkDeadline();
        _;
    }
```
## Function order affects gas consumption
The order of function will also have an impact on gas consumption. Because in smart contracts, there is a difference in the order of the functions. Each position will have an extra 22 gas. The order is dependent on method ID. So, if you rename the frequently accessed function to more early method ID, you can save gas cost. Please visit the following site for further information:

https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92

## Activate the optimizer
Before deploying your contract, activate the optimizer when compiling using “solc --optimize --bin sourceFile.sol”. By default, the optimizer will optimize the contract assuming it is called 200 times across its lifetime. If you want the initial contract deployment to be cheaper and the later function executions to be more expensive, set it to “ --optimize-runs=1”. Conversely, if you expect many transactions and do not care for higher deployment cost and output size, set “--optimize-runs” to a high number.

```
module.exports = {
solidity: {
version: "0.8.15",
settings: {
  optimizer: {
    enabled: true,
    runs: 1000,
  },
},
},
};
```
Please visit the following site for further information:

https://docs.soliditylang.org/en/v0.5.4/using-the-compiler.html#using-the-commandline-compiler

Here's one example of instance on opcode comparison that delineates the gas saving mechanism:

```
for !=0 before optimization
PUSH1 0x00
DUP2
EQ
ISZERO
PUSH1 [cont offset]
JUMPI

after optimization
DUP1
PUSH1 [revert offset]
JUMPI
```
Disclaimer: There have been several bugs with security implications related to optimizations. For this reason, Solidity compiler optimizations are disabled by default, and it is unclear how many contracts in the wild actually use them. Therefore, it is unclear how well they are being tested and exercised. High-severity security issues due to optimization bugs have occurred in the past . A high-severity bug in the emscripten -generated solc-js compiler used by Truffle and Remix persisted until late 2018. The fix for this bug was not reported in the Solidity CHANGELOG. Another high-severity optimization bug resulting in incorrect bit shift results was patched in Solidity 0.5.6. Please measure the gas savings from optimizations, and carefully weigh them against the possibility of an optimization-related bug. Also, monitor the development and adoption of Solidity compiler optimizations to assess their maturity.

## Non-strict inequalities are cheaper than strict ones
In the EVM, there is no opcode for non-strict inequalities (>=, <=) and two operations are performed (> + = or < + =).

As an example, the following `<=` inequality instance may be refactored as follows:

[File: JumpRate.sol#L16](https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol#L16)

```diff
-    if (util <= kink) {
// Rationale for adding 1 on the right side of the inequality:
// x <= 10; [10, 9, 8, ...]
// x < 10 + 1 is the same as x < 11; [10, 9, 8 ...]
+    if (util < kink + 1) {
```
