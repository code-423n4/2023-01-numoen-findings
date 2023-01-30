
## Summary

### Gas Optimizations
| |Issue|Instances|Total Gas Saved|
|-|:-|:-:|:-:|
| [G&#x2011;01] | Using `storage` instead of `memory` for structs/arrays saves gas | 2 |  4200 |
| [G&#x2011;02] | Structs can be packed into fewer storage slots | 4 |  - |
| [G&#x2011;03] | `keccak256()` should only need to be called on a specific string literal once | 1 |  42 |
| [G&#x2011;04] | Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if`-statement | 10 |  850 |
| [G&#x2011;05] | Multiple accesses of a mapping/array should use a local variable cache | 7 |  294 |
| [G&#x2011;06] | State variables should be cached in stack variables rather than re-reading them from storage | 10 |  1000 |
| [G&#x2011;07] | Avoid contract existence checks by using low level calls | 18 |  1800 |
| [G&#x2011;08] | `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables (`-=` too) | 4 |  452 |

Total: 56 instances over 8 issues with **8638 gas** saved.

Gas totals use lower bounds of ranges and count two iterations of each `for`-loop. All values above are runtime, not deployment, values; deployment values are listed in the individual issue descriptions.

## Gas Optimizations

### [G&#x2011;01]  Using `storage` instead of `memory` for structs/arrays saves gas
When fetching data from a storage location, assigning the data to a `memory` variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (**2100 gas**) for *each* field of the struct/array. If the fields are read from the new memory variable, they incur an additional `MLOAD` rather than a cheap stack read. Instead of declaring the variable with the `memory` keyword, declaring the variable with the `storage` keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a `memory` variable, is if the full struct/array is being returned by the function, is being passed to a function that requires `memory`, or if the array/struct is being read from another `memory` array/struct.

*There are 2 instances of this issue:*

```solidity
File: src\core\Lendgine.sol

/// @audit `rewardPerPositionPaid` isn't used
167     Position.Info memory positionInfo = positions[msg.sender]; // SLOAD

/// @audit `tokensOwed` isn't used
167     Position.Info memory positionInfo = positions[msg.sender]; // SLOAD
```

### [G&#x2011;02]  Structs can be packed into fewer storage slots
Each slot saved can avoid an extra Gsset (**20000 gas**) for the first setting of the struct. Subsequent reads as well as writes have smaller gas savings.

*There are 4 instances of this issue:*

```solidity
File: src\periphery\LendgineRouter.sol

/// @audit `swapType` can be after `payer`
74    struct MintCallbackData {
75      address token0;
76      address token1;
77      uint256 token0Exp;
78      uint256 token1Exp;
79      uint256 upperBound;
80      uint256 collateralMax;
81      SwapType swapType;
82      bytes swapExtraData;
83      address payer;
84    }

/// @audit `swapType` can be after `recipient`
126   struct MintParams {
127     address token0;
128     address token1;
129     uint256 token0Exp;
130     uint256 token1Exp;
131     uint256 upperBound;
132     uint256 amountIn;
133     uint256 amountBorrow;
134     uint256 sharesMin;
135     SwapType swapType;
136     bytes swapExtraData;
137     address recipient;
138     uint256 deadline;
139   }

/// @audit `swapType` can be after `recipient`
175   struct PairMintCallbackData {
176     address token0;
177     address token1;
178     uint256 token0Exp;
179     uint256 token1Exp;
180     uint256 upperBound;
181     uint256 collateralMin;
182     uint256 amount0Min;
183     uint256 amount1Min;
184     SwapType swapType;
185     bytes swapExtraData;
186     address recipient;
187   }

/// @audit `swapType` can be after `recipient`
240   struct BurnParams {
241     address token0;
242     address token1;
243     uint256 token0Exp;
244     uint256 token1Exp;
245     uint256 upperBound;
246     uint256 shares;
247     uint256 collateralMin;
248     uint256 amount0Min;
249     uint256 amount1Min;
250     SwapType swapType;
251     bytes swapExtraData;
252     address recipient;
253     uint256 deadline;
254   }
```


### [G&#x2011;03]  `keccak256()` should only need to be called on a specific string literal once
It should be saved to an immutable variable, and the variable used instead. If the hash is being used as a part of a function selector, the cast to `bytes4` should also only be done once.

*There is 1 instance of this issue:*

```solidity
File: src\libraries\Balance.sol

13      (bool success, bytes memory data) =
14        token.staticcall(abi.encodeWithSelector(bytes4(keccak256(bytes("balanceOf(address)"))), address(this)));
```

### [G&#x2011;04]  Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if`-statement
`require(a <= b); x = b - a` => `require(a <= b); unchecked { x = b - a }`.

*There are 10 instances of this issue:*

```solidity
File: src\core\JumpRate.sol

/// @audit `if`-condition on line 16
20        uint256 excessUtil = util - kink;
```

```solidity
File: src\core\Lendgine.sol

/// @audit ternary expression on line 198
201       position.tokensOwed = tokensOwed - collateral; // SSTORE

244     uint256 timeElapsed = block.timestamp - lastUpdate;

/// @audit ternary expression on line 253
256     totalLiquidityBorrowed = _totalLiquidityBorrowed - dilutionLP;
```

```solidity
File: src\core\Pair.sol

/// @audit checked arithmetic on line 106
108     reserve0 = _reserve0 - SafeCast.toUint120(amount0); // SSTORE

/// @audit checked arithmetic on line 106
109     reserve1 = _reserve1 - SafeCast.toUint120(amount1); // SSTORE

/// @audit checked arithmetic on line 106
110     totalLiquidity = _totalLiquidity - liquidity; // SSTORE

/// @audit checked arithmetic on line 131
135     reserve0 = _reserve0 + SafeCast.toUint120(amount0In) - SafeCast.toUint120(amount0Out); // SSTORE

/// @audit checked arithmetic on line 131
136     reserve1 = _reserve1 + SafeCast.toUint120(amount1In) - SafeCast.toUint120(amount1Out); // SSTORE
```

```solidity
File: src\periphery\SwapHelper.sol

107         zeroForOne ? TickMath.MIN_SQRT_RATIO + 1 : TickMath.MAX_SQRT_RATIO - 1,
```

### [G&#x2011;05]  Multiple accesses of a mapping/array should use a local variable cache
The instances below point to the second+ access of a value inside a mapping/array, within a function. Caching a mapping's value in a local `storage` or `calldata` variable when the value is accessed [multiple times](https://gist.github.com/IllIllI000/ec23a57daa30a8f8ca8b9681c8ccefb0), saves **~42 gas per access** due to not having to recalculate the key's keccak256 hash (Gkeccak256 - **30 gas**) and that calculation's associated stack operations. Caching an array's struct avoids recalculating the array offsets into memory/calldata.

*There are 7 instances of this issue:*

```solidity
File: src\core\Factory.sol

/// @audit `getLendgine[token0]` on line 76
86      getLendgine[token0][token1][token0Exp][token1Exp][upperBound] = lendgine;

/// @audit `getLendgine[token0][token1]` on line 76
86      getLendgine[token0][token1][token0Exp][token1Exp][upperBound] = lendgine;

/// @audit `getLendgine[token0][token1][token0Exp]` on line 76
86      getLendgine[token0][token1][token0Exp][token1Exp][upperBound] = lendgine;

/// @audit `getLendgine[token0][token1][token0Exp][token1Exp]` on line 76
86      getLendgine[token0][token1][token0Exp][token1Exp][upperBound] = lendgine;
```

```solidity
File: src\periphery\LiquidityManager.sol

/// @audit `positions[params.recipient]` on line 175
182     positions[params.recipient][lendgine] = position; // SSTORE

/// @audit `positions[msg.sender]` on line 211
218     positions[msg.sender][lendgine] = position; // SSTORE

/// @audit `positions[msg.sender]` on line 235
244     positions[msg.sender][params.lendgine] = position; // SSTORE
```

### [G&#x2011;06]  State variables should be cached in stack variables rather than re-reading them from storage
The instances below point to the second+ access of a state variable within a function. Caching of a state variable replaces each Gwarmaccess (**100 gas**) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.

*There are 10 instances of this issue:*

```solidity
File: src\core\Factory.sol

/// @audit `getLendgine[token0]` on line 76
86      getLendgine[token0][token1][token0Exp][token1Exp][upperBound] = lendgine;

/// @audit `getLendgine[token0][token1]` on line 76
86      getLendgine[token0][token1][token0Exp][token1Exp][upperBound] = lendgine;

/// @audit `getLendgine[token0][token1][token0Exp]` on line 76
86      getLendgine[token0][token1][token0Exp][token1Exp][upperBound] = lendgine;

/// @audit `getLendgine[token0][token1][token0Exp][token1Exp]` on line 76
86      getLendgine[token0][token1][token0Exp][token1Exp][upperBound] = lendgine;
```

```solidity
File: src\core\Lendgine.sol

/// @audit `totalPositionSize` on line 135
142     if (totalLiquiditySupplied == 0 && totalPositionSize > 0) revert CompleteUtilizationError();

/// @audit `totalPositionSize` on line 163
176     totalPositionSize -= size;

/// @audit `totalLiquidityBorrowed` on line 239
247     uint256 _totalLiquidityBorrowed = totalLiquidityBorrowed; // SLOAD
```

```solidity
File: src\periphery\LiquidityManager.sol

/// @audit `positions[params.recipient]` on line 175
182     positions[params.recipient][lendgine] = position; // SSTORE

/// @audit `positions[msg.sender]` on line 211
218     positions[msg.sender][lendgine] = position; // SSTORE

/// @audit `positions[msg.sender]` on line 235
244     positions[msg.sender][params.lendgine] = position; // SSTORE
```

### [G&#x2011;07]  Avoid contract existence checks by using low level calls
Prior to 0.8.10 the compiler inserted extra code, including `EXTCODESIZE` (**100 gas**), to check for contract existence for external function calls. In more recent solidity versions, the compiler will not insert these checks if the external call has a return value. Similar behavior can be achieved in earlier versions by using low-level calls, since low level calls never check for contract existence.

*There are 18 instances of this issue:*

```solidity
File: src\core\ImmutableState.sol

/// @audit parameters()
33      (token0, token1, _token0Exp, _token1Exp, upperBound) = Factory(msg.sender).parameters();
```

```solidity
File: src\periphery\LendgineRouter.sol

/// @audit mint()
147     shares = ILendgine(lendgine).mint(
148       address(this),
149       params.amountIn + params.amountBorrow,
150       abi.encode(
151         MintCallbackData({
152           token0: params.token0,
153           token1: params.token1,
154           token0Exp: params.token0Exp,
155           token1Exp: params.token1Exp,
156           upperBound: params.upperBound,
157           collateralMax: params.amountIn,
158           swapType: params.swapType,
159           swapExtraData: params.swapExtraData,
160           payer: msg.sender
161         })
162       )
163     );

/// @audit reserve0()
198     uint256 r0 = ILendgine(msg.sender).reserve0();

/// @audit reserve1()
199     uint256 r1 = ILendgine(msg.sender).reserve1();

/// @audit totalLiquidity()
200     uint256 totalLiquidity = ILendgine(msg.sender).totalLiquidity();

/// @audit convertLiquidityToCollateral()
231     uint256 collateralTotal = ILendgine(msg.sender).convertLiquidityToCollateral(liquidity);

/// @audit burn()
266     amount = ILendgine(lendgine).burn(
267       address(this),
268       abi.encode(
269         PairMintCallbackData({
270           token0: params.token0,
271           token1: params.token1,
272           token0Exp: params.token0Exp,
273           token1Exp: params.token1Exp,
274           upperBound: params.upperBound,
275           collateralMin: params.collateralMin,
276           amount0Min: params.amount0Min,
277           amount1Min: params.amount1Min,
278           swapType: params.swapType,
279           swapExtraData: params.swapExtraData,
280           recipient: recipient
281         })
282       )
283     );
```

```solidity
File: src\periphery\LiquidityManager.sol

/// @audit reserve0()
140     uint256 r0 = ILendgine(lendgine).reserve0();

/// @audit reserve1()
141     uint256 r1 = ILendgine(lendgine).reserve1();

/// @audit totalLiquidity()
142     uint256 totalLiquidity = ILendgine(lendgine).totalLiquidity();

/// @audit deposit()
157     uint256 size = ILendgine(lendgine).deposit(
158       address(this),
159       params.liquidity,
160       abi.encode(
161         PairMintCallbackData({
162           token0: params.token0,
163           token1: params.token1,
164           token0Exp: params.token0Exp,
165           token1Exp: params.token1Exp,
166           upperBound: params.upperBound,
167           amount0: amount0,
168           amount1: amount1,
169           payer: msg.sender
170         })
171       )
172     );

/// @audit positions()
177     (, uint256 rewardPerPositionPaid,) = ILendgine(lendgine).positions(address(this));

/// @audit withdraw()
208     (uint256 amount0, uint256 amount1, uint256 liquidity) = ILendgine(lendgine).withdraw(recipient, params.size);

/// @audit positions()
213     (, uint256 rewardPerPositionPaid,) = ILendgine(lendgine).positions(address(this));

/// @audit positions()
237     (, uint256 rewardPerPositionPaid,) = ILendgine(params.lendgine).positions(address(this));

/// @audit collect()
246     uint256 collectAmount = ILendgine(params.lendgine).collect(recipient, amount);
```

```solidity
File: src\periphery\SwapHelper.sol

/// @audit swap()
103       (int256 amount0, int256 amount1) = pool.swap(
104         params.recipient,
105         zeroForOne,
106         params.amount,
107         zeroForOne ? TickMath.MIN_SQRT_RATIO + 1 : TickMath.MAX_SQRT_RATIO - 1,
108         abi.encode(params.tokenIn)
109       );
```

```solidity
File: src\periphery\UniswapV2\libraries\UniswapV2Library.sol

/// @audit getReserves()
46      (uint256 reserve0, uint256 reserve1,) = IUniswapV2Pair(pairFor(factory, tokenA, tokenB)).getReserves();
```

### [G&#x2011;08]  `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables (`-=` too)
Using the addition operator instead of plus-equals saves **[113 gas](https://gist.github.com/IllIllI000/cbbfb267425b898e5be734d4008d4fe8)**. Subtructions act the same way.

*There are 4 instances of this issue:*

```solidity
File: src\core\Lendgine.sol

91      totalLiquidityBorrowed += liquidity;

114     totalLiquidityBorrowed -= liquidity;

176     totalPositionSize -= size;

257     rewardPerPositionStored += FullMath.mulDiv(dilutionSpeculative, 1e18, totalPositionSize);
```
