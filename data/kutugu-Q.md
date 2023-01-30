# Findings Summary

| ID     | Title                        | Severity      |
| ------ | ---------------------------- | ------------- |
| [L-01] | Restrict risk at the point   | Low           |
| [N-01] | Zero address check           | Non-Critical  |

# Detailed Findings

# [L-01] Restrict risk at the point

## Description

The `mint` function of the abstract contract `Pair` is not protected by `ReentrancyGuard`, instead it relies on the `ReentrancyGuard` protection of the subcontract entry. This is risky and should be restricted directly within the `Pair` contract.

If the `Pair` contract is inherited and forget to add `ReentrancyGuard` protection, it will lead to a reentry attack.

An abstract attack flow for the Pair `mint` function is as follows:

> 1_balance_before -> 100 / 100
> 1_callback -> reentry `mint`

>>   2_balance_before -> 100 / 100
>>   2_callback
>>   2_balance_after -> 200 / 200
>>   2_liquidity -> add 100

> 1_balance_after -> 200 / 200
> 1_liquidity -> add 100

# [N-01] Zero address check

## Description

Zero address leads to permanent loss of tokens, check list here:

- https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L71
- https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L105
- https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L152
- https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Lendgine.sol#L194
- https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Pair.sol#L93
- https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Pair.sol#L116
