# Floating Pragma: 

Code: https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/ImmutableState.sol#L2-L3

(and across the repo)

Mitigation: Consider fixing the pragma version to avoid breaking changes from compiler versions.


# Consider adding chainId to the `selfPermitAllowed`

https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/periphery/SelfPermit.sol#L42-L43