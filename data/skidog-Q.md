# Use updated Version of Solidity

## Context

All contracts

### Description

It is best practice to use updated versions of Solidity in order to take advantage of the latest security fixes and upgrades. Solidity version information can be found here https://github.com/ethereum/solidity/blob/develop/Changelog.md

#### Recommendation

Use an updated version of solidity, at least 0.8.10

# Follow Solidity Style Guide When Writing Functions

## Context

All contracts

### Description

Per the solidity style guide - 

Ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier.

Functions should be grouped according to their visibility and ordered:

constructor

receive function (if exists)

fallback function (if exists)

external

public

internal

private

Within a grouping, place the view and pure functions last.

# Test Coverage is incomplete

## Context 

All contracts. 

### Description

No contracts within src currently have complete coverage across all Lines, Statements, Branches, and Functions. 

#### Recommendation

Recommend an increase in test coverage to 100% across all Lines, Statements, Branches, and Functions for, at a minimum, all core contracts.

# Don't use Floating Pragmas

## Context

All contracts

### Description

Per SWC-103 https://swcregistry.io/docs/SWC-103 - Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

#### Recommendation

Per SWC-103 - Lock the pragma version and also consider known bugs ( https://github.com/ethereum/solidity/releases) for the compiler version that is chosen.

# Improve Natspec commentary 

## Context

All Contracts 

### Description

Per the solidity documentation - https://docs.soliditylang.org/en/v0.8.15/natspec-format.html - It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). Most functions in the core contracts are missing NatSpec documentation 

#### Recommendation

NatSpec comments should cover, at a minimum, all public interfaces per the solidity documentation. Refer to solidity documentation for NatSpec coverage examples


