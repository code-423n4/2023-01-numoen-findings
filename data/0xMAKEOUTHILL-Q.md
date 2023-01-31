Function JumpRouter.getBorrowRate - https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/JumpRate.sol#L13

Its stated to return (uint256 rate), but it doesn't. Either remove the return of rate in the function or use it in the function 

Same goes for JumpRouter.getSupplyRate - https://github.com/code-423n4/2023-01-numoen/blob/2ad9a73d793ea23a25a381faadc86ae0c8cb5913/src/core/JumpRate.sol#L25
