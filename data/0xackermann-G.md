### Gas Optimizations Report
| No. | Issue |
| --- | --- |
| 1 | [Splitting `require` which contains `&&` saves gas](#G-01-splitting-require-which-contains--saves-gas)|
| 2 | [Reduce the size of error messages (Long revert Strings)](#G-02-reduce-the-size-of-error-messages-long-revert-strings)|
| 3 | [Use Custom Errors rather than revert() / require() strings to save deployment gas [68 gas per instance]](#G-03-use-custom-errors-rather-than-revert--require-strings-to-save-deployment-gas-68-gas-per-instance)|
| 4 | [Usage of uint/int smaller than 32 bytes (256 bits) incurs overhead](#G-04-usage-of-uintint-smaller-than-32-bytes-256-bits-incurs-overhead)|
| 5 | [Optimize names to save gas [22 gas per instance]](#G-05-optimize-names-to-save-gas-22-gas-per-instance)|
| 6 | [Setting the `constructor` to `payable` [~13 gas per instance]](#G-06-setting-the-constructor-to-payable-13-gas-per-instance)|
| 7 | [Superfluous event fields](#G-07-superfluous-event-fields)|
| 8 | [Comparison operators](#G-08-comparison-operators)|
| 9 | [Use a more recent version of solidity](#G-09-use-a-more-recent-version-of-solidity)|
| 10 | [`x += y` costs more gas than `x = x + y` for state variables](#G-10-x--y-costs-more-gas-than-x--x--y-for-state-variables)|
---
### [G-01] Splitting `require` which contains `&&` saves gas

---
**Description:**

Using multiple require instead of operator && can save more gas. When a require statement has 2 or more expressions with &&, separate them into individual expression results in lesser gas.

**Lines of Code:** 

- [UniswapV2Library.sol#L61](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L61)
- [UniswapV2Library.sol#L79](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L79)

---
### [G-02] Reduce the size of error messages (Long revert Strings)

---
**Description:**

Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition has been met. 

Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for computing memory offset, etc.

**Recommendation:**

Shorten the revert strings to fit in 32 bytes. 

Or in contracts using solc version 0.8.4 or greater use the Custom Errors feature.

**Lines of Code:** 

- [UniswapV2Library.sol#L11](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L11)
- [UniswapV2Library.sol#L60](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L60)
- [UniswapV2Library.sol#L61](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L61)
- [UniswapV2Library.sol#L78](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L78)
- [UniswapV2Library.sol#L79](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L79)

---
### [G-03] Use Custom Errors rather than revert() / require() strings to save deployment gas [68 gas per instance]

---
**Description:**

Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they’re hitby avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas.

<https://blog.soliditylang.org/2021/04/21/custom-errors/>

**Recommendation:**

Use the Custom Errors feature.

**Lines of Code:** 

- [PositionMath.sol#L14](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/libraries/PositionMath.sol#L14)
- [PositionMath.sol#L16](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/libraries/PositionMath.sol#L16)
- [Payment.sol#L22](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/Payment.sol#L22)
- [UniswapV2Library.sol#L11](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L11)
- [UniswapV2Library.sol#L13](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L13)
- [UniswapV2Library.sol#L60](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L60)
- [UniswapV2Library.sol#L61](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L61)
- [UniswapV2Library.sol#L78](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L78)
- [UniswapV2Library.sol#L79](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L79)

---
### [G-04] Usage of uint/int smaller than 32 bytes (256 bits) incurs overhead

---
**Description:**

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size. Each operation involving a uint8 costs an extra 22-28 gas (depending on whether the other operand is also a variable of type uint8) as compared to ones involving uint256, due to the compiler having to clear the higher bits of the memory word before operating on the uint8, as well as the associated stack operations of doing so.

**Recommendation:**

Use a larger size then downcast where needed

**Lines of Code:** 

- [Factory.sol#L50](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Factory.sol#L50)
- [Factory.sol#L51](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Factory.sol#L51)
- [Factory.sol#L66](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Factory.sol#L66)
- [Factory.sol#L67](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Factory.sol#L67)
- [ImmutableState.sol#L30](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/ImmutableState.sol#L30)
- [ImmutableState.sol#L31](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/ImmutableState.sol#L31)
- [Pair.sol#L40](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Pair.sol#L40)
- [Pair.sol#L43](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Pair.sol#L43)
- [SafeCast.sol#L8](https://github.com/code-423n4/2023-01-numoen/tree/main/src/libraries/SafeCast.sol#L8)
- [SwapHelper.sol#L62](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/SwapHelper.sol#L62)

---
### [G-05] Optimize names to save gas [22 gas per instance]

---
**Description:**

Contracts most called functions could simply save gas by function ordering via Method ID. Calling a function at runtime will be cheaper if the function is positioned earlier in the order (has a relatively lower Method ID) because 22 gas are added to the cost of a function for every position that came before it. The caller can save on gas if you prioritize most called functions.

**Recommendation:**

Find a lower `method ID` name for the most called functions for example Call() vs. Call1() is cheaper by 22 gas. For example, the function IDs in the L1GraphTokenGateway.sol contract will be the most used; A lower method ID may be given.

**Proof Of Concept:**

https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92

**Lines of Code:** 

- [Factory.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Factory.sol)
- [ImmutableState.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/ImmutableState.sol)
- [JumpRate.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/JumpRate.sol)
- [Lendgine.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Lendgine.sol)
- [Pair.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Pair.sol)
- [Position.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/libraries/Position.sol)
- [PositionMath.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/libraries/PositionMath.sol)
- [Balance.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/libraries/Balance.sol)
- [SafeCast.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/libraries/SafeCast.sol)
- [LendgineRouter.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/LendgineRouter.sol)
- [LiquidityManager.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/LiquidityManager.sol)
- [Payment.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/Payment.sol)
- [SwapHelper.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/SwapHelper.sol)
- [UniswapV2Library.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol)
- [LendgineAddress.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/libraries/LendgineAddress.sol)

---
### [G-06] Setting the `constructor` to `payable` [~13 gas per instance]

---
**Lines of Code:** 

- [ImmutableState.sol#L27](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/ImmutableState.sol#L27)
- [LendgineRouter.sol#L49](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/LendgineRouter.sol#L49)
- [LiquidityManager.sol#L75](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/LiquidityManager.sol#L75)
- [Payment.sol#L17](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/Payment.sol#L17)
- [SwapHelper.sol#L29](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/SwapHelper.sol#L29)

---
### [G-07] Superfluous event fields

---
**Description:**

`block.number` and `block.timestamp` are added to the event information by default, so adding them manually will waste additional gas.

**Lines of Code:** 

- [Lendgine.sol#L33](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Lendgine.sol#L33)

---
### [G-08] Comparison operators

---
**Description:**

In the EVM, there is no opcode for `>=` or `<=`. When using greater than or equal, two operations are performed: `>` and `=`. Using strict comparison operators hence saves gas.

**Recommendation:**

 Replace `<=` with `<`, and `>=` with `>`. Do not forget to increment/decrement the compared variable.

**Lines of Code:** 

- [JumpRate.sol#L16](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/JumpRate.sol#L16)
- [Pair.sol#L66](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Pair.sol#L66)
- [PositionMath.sol#L16](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/libraries/PositionMath.sol#L16)
- [Payment.sol#L53](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/Payment.sol#L53)

---
### [G-09] Use a more recent version of solidity

---
**Description:**

Solidity 0.8.10 has a useful change that reduced gas costs of external calls which expect a return value.

In 0.8.15 the conditions necessary for inlining are relaxed. Benchmarks show that the change significantly decreases the bytecode size (which impacts the deployment cost) while the effect on the runtime gas usage is smaller.

In 0.8.17 prevent the incorrect removal of storage writes before calls to Yul functions that conditionally terminate the external EVM call; Simplify the starting offset of zero-length operations to zero. More efficient overflow checks for multiplication.

**Lines of Code:** 

- [Factory.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Factory.sol)
- [ImmutableState.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/ImmutableState.sol)
- [JumpRate.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/JumpRate.sol)
- [Lendgine.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Lendgine.sol)
- [Pair.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Pair.sol)
- [Position.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/libraries/Position.sol)
- [PositionMath.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/libraries/PositionMath.sol)
- [Balance.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/libraries/Balance.sol)
- [SafeCast.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/libraries/SafeCast.sol)
- [LendgineRouter.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/LendgineRouter.sol)
- [LiquidityManager.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/LiquidityManager.sol)
- [Payment.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/Payment.sol)
- [SwapHelper.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/SwapHelper.sol)
- [UniswapV2Library.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol)
- [LendgineAddress.sol](https://github.com/code-423n4/2023-01-numoen/tree/main/src/periphery/libraries/LendgineAddress.sol)

---
### [G-10] `x += y` costs more gas than `x = x + y` for state variables

---
**Lines of Code:** 

- [Lendgine.sol#L91](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Lendgine.sol#L91)
- [Lendgine.sol#L114](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Lendgine.sol#L114)
- [Lendgine.sol#L176](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Lendgine.sol#L176)
- [Lendgine.sol#L257](https://github.com/code-423n4/2023-01-numoen/tree/main/src/core/Lendgine.sol#L257)