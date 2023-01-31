
# Gas Optimizations Report

This report focuses on Numoen contest, in context of various improvements that can be made in terms of gas cost.

Some of the opportunities identified for improving gas efficiency throughout the codebase of Numoen Core Protocol are categorised into 06 main areas; with further multiple instances in each of the category.


# Summary

[G-01] State variable values are by default internal (01 Instance)
[G-02] Immutable has more gas efficiency than constant (07 Instances)
[G-03] Uint/Int lower than 32 bytes incurs overhead (80 Instances)
[G-04] Wastage of deployed gas when return is not present for returns function (16 Instances)
[G-05] 0perator assignment is more gas efficient than compound assignment (10 Instances)
[G-06] Functions with public visibility, if not called within the contract needed to be changed external (04 Instances) 
# [G-01] State variable values are by default internal (01 Instance)

When visibility for a state variable is not specified, the default value is already internal.

Declaring visibility of the variable internal consumes extra gas which is not needed.

Link to the Code:
https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/libraries/LendgineAddress.sol#L6

# [G-02] Immutable has more gas efficiency than constant (07 Instances)

Using immutable instead of constant, save more gas due to avoiding storage access for state variables.

Variable values are set through constructor when using immutable, which also eliminates the need of assigning initial values to state variable making them more efficient in terms of gas cost

Link to the Code:

1.	https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol#L7
2.	https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol#L9
3.	https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol#L11
4.	https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol#L7
5.	https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol#L9
6.	https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/JumpRate.sol#L11
7.	https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/libraries/LendgineAddress.sol#L6
 
# [G-03] Uint/Int lower than 32 bytes consumes incurs overhead (80 Instances)

Contract gas usage increases as EVM standard operation are of 32 bytes. If any element is smaller than 32 bytes (i.e.; 256 bits) it will cause EVM to consume more gas which can be around 12 gas depending on size for reducing the size to given output like uint8.


Link to the Code:
1.	https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Factory.sol#L50
2.	https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Factory.sol#L51
3.	https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Factory.sol#L52
4.	https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Factory.sol#L66
5.	https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Factory.sol#L67
6.	https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/ImmutableState.sol#L30
7.	https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/ImmutableState.sol#L31
8.	https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/SwapHelper.sol#L62https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/SwapHelper.sol#L62
9.	https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Pair.sol#L40
10.	https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Pair.sol#L43
11.	https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Pair.sol#L94
12.	https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Pair.sol#L95
13.	https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Pair.sol#L119
14.	https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Pair.sol#L120
15.	https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Pair.sol#L85
16.	https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Pair.sol#L86
17.	https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Pair.sol#L108
18.	https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Pair.sol#L109
19.	https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Pair.sol#L135
20.	https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Pair.sol#L136
21.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L54
22.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L88
23.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L105
24.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L122
25.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L139
26.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L156
27.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L173
28.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L190
29.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L207
30.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L224
31.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L241
32.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L258
33.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L275
34.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L292
35.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L309
36.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L326
37.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L343
38.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L360
39.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L377
40.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L394
41.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L411
42.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L428
43.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L445
44.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L462
45.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L479
46.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L496
47.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L513
48.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L530
49.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L547
50.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L561
51.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L596
52.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L614
53.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L632
54.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L650
55.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L668
56.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L686
57.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L704
58.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L722
59.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L740
60.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L758
61.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L776
62.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L794
63.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L812
64.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L830
65.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L848
66.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L866
67.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L884
68.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L902
69.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L920
70.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L938
71.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L956
72.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L974
73.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L992
74.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L1010
75.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L1028
76.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L1046
77.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L1064
78.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L1082
79.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L1100
80.	https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol#L1118


# [G-04] Wastage of deployed gas when return is not present for returns function (16 Instances)

Wastage of gas during deployment; when return is absent for named variable when function returns.

Link to the Code:
1.	https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Factory.sol#L63
2.	https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L71
3.	https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L105
4.	https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L123
5.	https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L152
6.	https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L194
7.	https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L230
8.	https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LendgineRouter.sol#L142
9.	https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/SwapHelper.sol#L69
10.	https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Pair.sol#L93
11.	https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/libraries/PositionMath.sol#L12
12.	https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/libraries/LendgineAddress.sol#L9
13.	https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L17
14.	https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L36
15.	https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L51
16.	https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/UniswapV2/libraries/UniswapV2Library.sol#L69


# [G-05] 0perator assignment is more gas efficient than compound assignment (10 Instances)

Compound assignment operators (+= / -=) are more expensive in terms of gas consumption and needs to be avoided.

Operator assignments (a = a + b / a - b) are preferable in terms of gas optimization.

Link to the Code:
1.	https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L91
2.	https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L114
3.	https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L176
4.	https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L257
5.	https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L178
6.	https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L180
7.	https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L214
8.	https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L216
9.	https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L238
10.	https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/LiquidityManager.sol#L242


# [G-06] Public visibility consumes more gas as compared to external functions (04 Instances)

Functions with public visibility, if not called within the contract needed to be changed external.

Link to the Code:
1.	https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/Payment.sol#L25
2.	https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/Payment.sol#L35
3.	https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/Payment.sol#L44
4.	https://github.com/code-423n4/2023-01-numoen/blob/main/src/periphery/Payment.sol#L52