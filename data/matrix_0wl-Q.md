# Summary

## Low Risk and Non-Critical Issues

## Non-Critical Issues

|      | Issue                                                                      |
| ---- | :------------------------------------------------------------------------- |
| NC-1 | USE OF BYTES.CONCAT() INSTEAD OF ABI.ENCODEPACKED(,)                       |
| NC-2 | SOLIDITY COMPILER VERSIONS MISMATCH                                        |
| NC-3 | Use a more recent version of solidity                                      |
| NC-4 | `require()` / `revert()` statements should have descriptive reason strings |
| NC-5 | For functions, follow solidity standard naming conventions                 |
| NC-6 | Lines are too long                                                         |

| NC-6 | NOT VERIFIED INPUT |

### [NC-1] USE OF BYTES.CONCAT() INSTEAD OF ABI.ENCODEPACKED(,)

Rather than using `abi.encodePacked` for appending bytes, since version 0.8.4, `bytes.concat()` is enabled

Since version 0.8.4 for appending bytes, `bytes.concat()` can be used instead of `abi.encodePacked(,)`.

#### **Proof Of Concept**

```solidity
File: src/periphery/UniswapV2/libraries/UniswapV2Library.sol

26:               keccak256(abi.encodePacked(token0, token1)),

```

### [NC-2] SOLIDITY COMPILER VERSIONS MISMATCH

The project is compiled with different versions of Solidity, which is not recommended because it can lead to undefined behaviors.

It is better to use one Solidity compiler version across all contracts instead of different versions with different bugs and security checks.

#### **Proof Of Concept**

```solidity
File: src/core/Factory.sol

2: pragma solidity ^0.8.4;

```

```solidity
File: src/core/ImmutableState.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/core/JumpRate.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/core/Lendgine.sol

2: pragma solidity ^0.8.4;

```

```solidity
File: src/core/Pair.sol

2: pragma solidity ^0.8.4;

```

```solidity
File: src/core/libraries/Position.sol

2: pragma solidity ^0.8.4;

```

```solidity
File: src/core/libraries/PositionMath.sol

2: pragma solidity >=0.5.0;

```

```solidity
File: src/libraries/Balance.sol

2: pragma solidity ^0.8.4;

```

```solidity
File: src/libraries/SafeCast.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/periphery/LendgineRouter.sol

2: pragma solidity ^0.8.4;

```

```solidity
File: src/periphery/LiquidityManager.sol

2: pragma solidity ^0.8.4;

```

```solidity
File: src/periphery/Payment.sol

2: pragma solidity ^0.8.4;

```

```solidity
File: src/periphery/SwapHelper.sol

2: pragma solidity ^0.8.4;

```

```solidity
File: src/periphery/UniswapV2/libraries/UniswapV2Library.sol

1: pragma solidity >=0.8.0;

```

```solidity
File: src/periphery/libraries/LendgineAddress.sol

2: pragma solidity >=0.5.0;

```

### [NC-3] Use a more recent version of solidity

#### **Context:**

All Contracts

#### **Description:**

For security, it is best practice to use the latest Solidity version.

For the security fix list in the versions.

https://github.com/ethereum/solidity/blob/develop/Changelog.md

#### **Recommendation**

Old version of Solidity is used, newer version can be used `(0.8.17)`

### [NC-4] `require()` / `revert()` statements should have descriptive reason strings

#### **Proof Of Concept**

```solidity
File: src/libraries/SafeCast.sol

9:     require((z = uint120(y)) == y);

16:     require(y < 2 ** 255);

```

```solidity
File: src/periphery/SwapHelper.sol

116:         require(amountOutReceived == params.amount);

```

### [NC-5] For functions, follow solidity standard naming conventions

Solidity standard naming convention for internal and private functions: the mixedCase format starting with an underscore (\_mixedCase starting with an underscore)

#### **Proof Of Concept**

```solidity
File: src/core/JumpRate.sol

40:   function utilizationRate(uint256 borrowedLiquidity, uint256 totalLiquidity) private pure returns (uint256 rate) {

```

```solidity
File: src/core/Pair.sol

70:   function mint(uint256 liquidity, bytes calldata data) internal {

93:   function burn(address to, uint256 liquidity) internal returns (uint256 amount0, uint256 amount1) {

```

```solidity
File: src/core/libraries/Position.sol

38:       function update(

69:   function newTokensOwed(Position.Info memory position, uint256 rewardPerPosition) internal pure returns (uint256) {

73:       function convertLiquidityToPosition(

86:     function convertPositionToLiquidity(

```

```solidity
File: src/core/libraries/PositionMath.sol

12:   function addDelta(uint256 x, int256 y) internal pure returns (uint256 z) {

```

```solidity
File: src/libraries/Balance.sol

12:   function balance(address token) internal view returns (uint256) {

```

```solidity
File: src/libraries/SafeCast.sol

8:   function toUint120(uint256 y) internal pure returns (uint120 z) {

15:   function toInt256(uint256 y) internal pure returns (int256 z) {

```

```solidity
File: src/periphery/Payment.sol

52:   function pay(address token, address payer, address recipient, uint256 value) internal {

```

```solidity
File: src/periphery/SwapHelper.sol

69:   function swap(SwapType swapType, SwapParams memory params, bytes memory data) internal returns (uint256 amount) {

```

```solidity
File: src/periphery/UniswapV2/libraries/UniswapV2Library.sol

10:   function sortTokens(address tokenA, address tokenB) internal pure returns (address token0, address token1) {

17:   function pairFor(address factory, address tokenA, address tokenB) internal pure returns (address pair) {

36:       function getReserves(

51:     function getAmountOut(

69:     function getAmountIn(

```

```solidity
File: src/periphery/libraries/LendgineAddress.sol

9:       function computeAddress(

```

#### **Recommendation**

Use solidity standard naming convention for internal and private functions

### [NC-6] Lines are too long

#### **Context:**

Almost all Contracts

#### **Description:**

Usually lines in source code are limited to 80 characters.

[Reference](https://docs.soliditylang.org/en/v0.8.10/style-guide.html#maximum-line-length)

#### **Proof Of Concept**

```solidity
File: src/core/Factory.sol

39:   mapping(address => mapping(address => mapping(uint256 => mapping(uint256 => mapping(uint256 => address)))))

75:     if (token0 == address(0) || token1 == address(0)) revert ZeroAddressError();

76:     if (getLendgine[token0][token1][token0Exp][token1Exp][upperBound] != address(0)) revert DeployedError();

77:     if (token0Exp > 18 || token0Exp < 6 || token1Exp > 18 || token1Exp < 6) revert ScaleError();

80:       Parameters({token0: token0, token1: token1, token0Exp: token0Exp, token1Exp: token1Exp, upperBound: upperBound});

82:     lendgine = address(new Lendgine{ salt: keccak256(abi.encode(token0, token1, token0Exp, token1Exp, upperBound)) }());

87:     emit LendgineCreated(token0, token1, token0Exp, token1Exp, upperBound, lendgine);

```

```solidity
File: src/core/ImmutableState.sol

33:     (token0, token1, _token0Exp, _token1Exp, upperBound) = Factory(msg.sender).parameters();

```

```solidity
File: src/core/JumpRate.sol

13:   function getBorrowRate(uint256 borrowedLiquidity, uint256 totalLiquidity) public pure override returns (uint256 rate) {

40:   function utilizationRate(uint256 borrowedLiquidity, uint256 totalLiquidity) private pure returns (uint256 rate) {

```

```solidity
File: src/core/Lendgine.sol

25:   event Mint(address indexed sender, uint256 collateral, uint256 shares, uint256 liquidity, address indexed to);

27:   event Burn(address indexed sender, uint256 collateral, uint256 shares, uint256 liquidity, address indexed to);

29:   event Deposit(address indexed sender, uint256 size, uint256 liquidity, address indexed to);

31:   event Withdraw(address indexed sender, uint256 size, uint256 liquidity, address indexed to);

33:   event AccrueInterest(uint256 timeElapsed, uint256 collateral, uint256 liquidity);

35:   event AccruePositionInterest(address indexed owner, uint256 rewardPerPosition);

89:     if (totalSupply > 0 && totalLiquidityBorrowed == 0) revert CompleteUtilizationError();

96:     IMintCallback(msg.sender).mintCallback(collateral, amount0, amount1, liquidity, data);

99:     if (balanceAfter < balanceBefore + collateral) revert InsufficientInputError();

105:   function burn(address to, bytes calldata data) external override nonReentrant returns (uint256 collateral) {

116:     SafeTransferLib.safeTransfer(token1, to, collateral); // optimistically transfer

138:     size = Position.convertLiquidityToPosition(liquidity, totalLiquiditySupplied, _totalPositionSize);

142:     if (totalLiquiditySupplied == 0 && totalPositionSize > 0) revert CompleteUtilizationError();

168:     liquidity = Position.convertPositionToLiquidity(size, totalLiquiditySupplied, _totalPositionSize);

175:     positions.update(msg.sender, -SafeCast.toInt256(size), rewardPerPositionStored);

194:   function collect(address to, uint256 collateralRequested) external override nonReentrant returns (uint256 collateral) {

198:     collateral = collateralRequested > tokensOwed ? tokensOwed : collateralRequested;

213:   function convertLiquidityToShare(uint256 liquidity) public view override returns (uint256) {

215:     return _totalLiquidityBorrowed == 0 ? liquidity : FullMath.mulDiv(liquidity, totalSupply, _totalLiquidityBorrowed);

219:   function convertShareToLiquidity(uint256 shares) public view override returns (uint256) {

224:   function convertCollateralToLiquidity(uint256 collateral) public view override returns (uint256) {

229:   function convertLiquidityToCollateral(uint256 liquidity) public view override returns (uint256) {

248:     uint256 totalLiquiditySupplied = totalLiquidity + _totalLiquidityBorrowed; // SLOAD

250:     uint256 borrowRate = getBorrowRate(_totalLiquidityBorrowed, totalLiquiditySupplied);

252:     uint256 dilutionLPRequested = (FullMath.mulDiv(borrowRate, _totalLiquidityBorrowed, 1e18) * timeElapsed) / 365 days;

253:     uint256 dilutionLP = dilutionLPRequested > _totalLiquidityBorrowed ? _totalLiquidityBorrowed : dilutionLPRequested;

257:     rewardPerPositionStored += FullMath.mulDiv(dilutionSpeculative, 1e18, totalPositionSize);

```

```solidity
File: src/core/Pair.sol

8: import { IPairMintCallback } from "./interfaces/callback/IPairMintCallback.sol";

23:   event Burn(uint256 amount0Out, uint256 amount1Out, uint256 liquidity, address indexed to);

25:   event Swap(uint256 amount0Out, uint256 amount1Out, uint256 amount0In, uint256 amount1In, address indexed to);

53:   function invariant(uint256 amount0, uint256 amount1, uint256 liquidity) public view override returns (bool) {

81:     if (!invariant(_reserve0 + amount0In, _reserve1 + amount1In, _totalLiquidity + liquidity)) {

93:   function burn(address to, uint256 liquidity) internal returns (uint256 amount0, uint256 amount1) {

106:     if (!invariant(_reserve0 - amount0, _reserve1 - amount1, _totalLiquidity - liquidity)) revert InvariantError();

116:   function swap(address to, uint256 amount0Out, uint256 amount1Out, bytes calldata data) external override nonReentrant {

131:     if (!invariant(_reserve0 + amount0In - amount0Out, _reserve1 + amount1In - amount1Out, totalLiquidity)) {

135:     reserve0 = _reserve0 + SafeCast.toUint120(amount0In) - SafeCast.toUint120(amount0Out); // SSTORE

136:     reserve1 = _reserve1 + SafeCast.toUint120(amount1In) - SafeCast.toUint120(amount1Out); // SSTORE

```

```solidity
File: src/core/libraries/Position.sol

64:     if (tokensOwed > 0) positionInfo.tokensOwed = _positionInfo.tokensOwed + tokensOwed;

69:   function newTokensOwed(Position.Info memory position, uint256 rewardPerPosition) internal pure returns (uint256) {

70:     return FullMath.mulDiv(position.size, rewardPerPosition - position.rewardPerPositionPaid, 1 ether);

83:       totalLiquiditySupplied == 0 ? liquidity : FullMath.mulDiv(liquidity, totalPositionSize, totalLiquiditySupplied);

95:     return FullMath.mulDiv(position, totalLiquiditySupplied, totalPositionSize);

```

```solidity
File: src/libraries/Balance.sol

14:       token.staticcall(abi.encodeWithSelector(bytes4(keccak256(bytes("balanceOf(address)"))), address(this)));

```

```solidity
File: src/periphery/LendgineRouter.sol

11: import { IPairMintCallback } from "../core/interfaces/callback/IPairMintCallback.sol";

20: contract LendgineRouter is Multicall, Payment, SelfPermit, SwapHelper, IMintCallback, IPairMintCallback {

25:   event Mint(address indexed from, address indexed lendgine, uint256 collateral, uint256 shares, address indexed to);

27:   event Burn(address indexed from, address indexed lendgine, uint256 collateral, uint256 shares, address indexed to);

100:       factory, decoded.token0, decoded.token1, decoded.token0Exp, decoded.token1Exp, decoded.upperBound

142:   function mint(MintParams calldata params) external payable checkDeadline(params.deadline) returns (uint256 shares) {

144:       factory, params.token0, params.token1, params.token0Exp, params.token1Exp, params.upperBound

190:   function pairMintCallback(uint256 liquidity, bytes calldata data) external override {

191:     PairMintCallbackData memory decoded = abi.decode(data, (PairMintCallbackData));

194:       factory, decoded.token0, decoded.token1, decoded.token0Exp, decoded.token1Exp, decoded.upperBound

213:     if (amount0 < decoded.amount0Min || amount1 < decoded.amount1Min) revert AmountError();

231:     uint256 collateralTotal = ILendgine(msg.sender).convertLiquidityToCollateral(liquidity);

236:       SafeTransferLib.safeTransfer(decoded.token1, decoded.recipient, collateralOut);

257:   function burn(BurnParams calldata params) external payable checkDeadline(params.deadline) returns (uint256 amount) {

259:       factory, params.token0, params.token1, params.token0Exp, params.token1Exp, params.upperBound

262:     address recipient = params.recipient == address(0) ? address(this) : params.recipient;

264:     SafeTransferLib.safeTransferFrom(lendgine, msg.sender, lendgine, params.shares);

```

```solidity
File: src/periphery/LiquidityManager.sol

9: import { IPairMintCallback } from "../core/interfaces/callback/IPairMintCallback.sol";

16: contract LiquidityManager is Multicall, Payment, SelfPermit, IPairMintCallback {

40:   event Collect(address indexed from, address indexed lendgine, uint256 amount, address indexed to);

105:     PairMintCallbackData memory decoded = abi.decode(data, (PairMintCallbackData));

108:       factory, decoded.token0, decoded.token1, decoded.token0Exp, decoded.token1Exp, decoded.upperBound

112:     if (decoded.amount0 > 0) pay(decoded.token0, decoded.payer, msg.sender, decoded.amount0);

113:     if (decoded.amount1 > 0) pay(decoded.token1, decoded.payer, msg.sender, decoded.amount1);

135:   function addLiquidity(AddLiquidityParams calldata params) external payable checkDeadline(params.deadline) {

137:       factory, params.token0, params.token1, params.token0Exp, params.token1Exp, params.upperBound

151:       amount0 = FullMath.mulDivRoundingUp(params.liquidity, r0, totalLiquidity);

152:       amount1 = FullMath.mulDivRoundingUp(params.liquidity, r1, totalLiquidity);

155:     if (amount0 < params.amount0Min || amount1 < params.amount1Min) revert AmountError();

177:     (, uint256 rewardPerPositionPaid,) = ILendgine(lendgine).positions(address(this));

178:     position.tokensOwed += FullMath.mulDiv(position.size, rewardPerPositionPaid - position.rewardPerPositionPaid, 1e18);

184:     emit AddLiquidity(msg.sender, lendgine, params.liquidity, size, amount0, amount1, params.recipient);

201:   function removeLiquidity(RemoveLiquidityParams calldata params) external payable checkDeadline(params.deadline) {

203:       factory, params.token0, params.token1, params.token0Exp, params.token1Exp, params.upperBound

206:     address recipient = params.recipient == address(0) ? address(this) : params.recipient;

208:     (uint256 amount0, uint256 amount1, uint256 liquidity) = ILendgine(lendgine).withdraw(recipient, params.size);

209:     if (amount0 < params.amount0Min || amount1 < params.amount1Min) revert AmountError();

213:     (, uint256 rewardPerPositionPaid,) = ILendgine(lendgine).positions(address(this));

214:     position.tokensOwed += FullMath.mulDiv(position.size, rewardPerPositionPaid - position.rewardPerPositionPaid, 1e18);

220:     emit RemoveLiquidity(msg.sender, lendgine, liquidity, params.size, amount0, amount1, recipient);

230:   function collect(CollectParams calldata params) external payable returns (uint256 amount) {

233:     address recipient = params.recipient == address(0) ? address(this) : params.recipient;

237:     (, uint256 rewardPerPositionPaid,) = ILendgine(params.lendgine).positions(address(this));

238:     position.tokensOwed += FullMath.mulDiv(position.size, rewardPerPositionPaid - position.rewardPerPositionPaid, 1e18);

241:     amount = params.amountRequested > position.tokensOwed ? position.tokensOwed : params.amountRequested;

246:     uint256 collectAmount = ILendgine(params.lendgine).collect(recipient, amount);

247:     if (collectAmount != amount) revert CollectError(); // extra check for safety

```

```solidity
File: src/periphery/Payment.sol

25:   function unwrapWETH(uint256 amountMinimum, address recipient) public payable {

35:   function sweepToken(address token, uint256 amountMinimum, address recipient) public payable {

45:     if (address(this).balance > 0) SafeTransferLib.safeTransferETH(msg.sender, address(this).balance);

52:   function pay(address token, address payer, address recipient, uint256 value) internal {

```

```solidity
File: src/periphery/SwapHelper.sol

6: import { IUniswapV3SwapCallback } from "./UniswapV3/interfaces/callback/IUniswapV3SwapCallback.sol";

38:   function uniswapV3SwapCallback(int256 amount0Delta, int256 amount1Delta, bytes calldata data) external override {

42:     SafeTransferLib.safeTransfer(tokenIn, msg.sender, amount0Delta > 0 ? uint256(amount0Delta) : uint256(amount1Delta));

69:   function swap(SwapType swapType, SwapParams memory params, bytes memory data) internal returns (uint256 amount) {

71:       address pair = UniswapV2Library.pairFor(uniswapV2Factory, params.tokenIn, params.tokenOut);

74:         UniswapV2Library.getReserves(uniswapV2Factory, params.tokenIn, params.tokenOut);

77:         ? UniswapV2Library.getAmountOut(uint256(params.amount), reserveIn, reserveOut)

78:         : UniswapV2Library.getAmountIn(uint256(-params.amount), reserveIn, reserveOut);

81:         params.amount > 0 ? (uint256(params.amount), amount) : (amount, uint256(-params.amount));

83:       (address token0,) = UniswapV2Library.sortTokens(params.tokenIn, params.tokenOut);

85:         params.tokenIn == token0 ? (uint256(0), amountOut) : (amountOut, uint256(0));

88:       IUniswapV2Pair(pair).swap(amount0Out, amount1Out, params.recipient, bytes(""));

99:           uniswapV3Factory, PoolAddress.getPoolKey(params.tokenIn, params.tokenOut, uniV3Data.fee)

115:         (amount, amountOutReceived) = zeroForOne ? (uint256(amount0), amount1) : (uint256(amount1), amount0);

```

```solidity
File: src/periphery/UniswapV2/libraries/UniswapV2Library.sol

10:   function sortTokens(address tokenA, address tokenB) internal pure returns (address token0, address token1) {

17:   function pairFor(address factory, address tokenA, address tokenB) internal pure returns (address pair) {

27:               hex"e18a34eb0e04b04f7a0ac29a6e80748dca96319b42c54d679cb821dca90c6303" // init code hash

46:     (uint256 reserve0, uint256 reserve1,) = IUniswapV2Pair(pairFor(factory, tokenA, tokenB)).getReserves();

47:     (reserveA, reserveB) = tokenA == token0 ? (reserve0, reserve1) : (reserve1, reserve0);

61:     require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");

79:     require(reserveIn > 0 && reserveOut > 0, "UniswapV2Library: INSUFFICIENT_LIQUIDITY");

```

```solidity
File: src/periphery/libraries/LendgineAddress.sol

7:     71_695_300_681_742_793_458_567_320_549_603_773_755_938_496_017_772_337_363_704_152_556_600_186_974;

28:               keccak256(abi.encode(token0, token1, token0Exp, token1Exp, upperBound)),

```

#### **Recommendation**

The lines should be split when they reach that length.

## Low Issues

|     | Issue                             |
| --- | :-------------------------------- |
| L-1 | Potential underflow               |

### [L-1] Potential underflow

In the line `uint256 denominator = (reserveOut - amountOut) * 997;` there is a potential for underflow if `amountOut >= reserveOut`, causing `(reserveOut - amountOut)` to result in a negative number. In this case, the value of `denominator` will be incorrect and cause an error.

##### **Proof Of Concept**

```solidity
File: src/periphery/UniswapV2/libraries/UniswapV2Library.sol

81:   uint256 denominator = (reserveOut - amountOut) * 997;

```