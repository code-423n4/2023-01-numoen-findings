## **Summary**

State updated and deleted inside `createLendgine()` but never used.

## **Vulnerability Detail**

On `Factory.sol` , inside `createLendgine()`, the parameters state struct is updated `parameters`:

```js
parameters = Parameters({
	token0: token0,
	token1: token1,
	token0Exp: token0Exp,
	token1Exp: token1Exp,
	upperBound: upperBound
}); 
```

Then, the new Lendgine contract is created with the values stored in the struct passed directly:

```js
lendgine = address(new Lendgine{ salt: keccak256(abi.encode(token0, token1, token0Exp, token1Exp, upperBound)) }());
```

And afterwards the `parameters` are deleted:

```js
delete parameters;
```

So, the `parameters` struct is not after being updated and is later deleted. This is an unnecessary step in the code, as the values stored in the struct are passed directly to the creation of the Lendgine contract.

## **Impact**

This could lead to confusion or potential bugs in the code. Additionally, the extra gas consumption and increased bytecode size could potentially cause issues for users with limited gas or for those who are deploying to the network with limited space in their blocks. These users might face higher deployment fees or slower deployment times, which could lead to a negative user experience.

## **Code Snippet**

https://github.com/code-423n4/2023-01-numoen/blob/main/src/core/Factory.sol#L79-L84


## **Tool used**

Manual Review

## **Recommendations**

To improve the efficiency and reduce the complexity of code, it is recommended to:
a) Delete the unused parameters and just create the new Lendgine passing values directly, or
b) Pass the `parameters` struct as the data to `abi.encode`:

```js
lendgine = address(new Lendgine{salt: keccak256(abi.encode(parameters))}());
```