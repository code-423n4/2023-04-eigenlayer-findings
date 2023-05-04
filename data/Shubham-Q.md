## Low Risk Issues

| |Issue|Instances|
|-|:-|:-:|
| [L-01](#L-01) | Update codes to avoid Compile Errors | 2 |

## Non-Critical Issues

| |Issue|Instances|
|-|:-|:-:|
| [NC-1](#NC-1) | Unnecessary `return` statement | 2 |
| [NC-2](#NC-2) | Confusing NatSpec | 1 |

## [L-01] Update codes to avoid Compile Errors

*There are several instances of this issue.*
https://docs.soliditylang.org/en/develop/security-considerations.html#take-warnings-seriously

```solodity

warning[2519]: Warning: This declaration shadows an existing declaration.
   --> src/test/DepositWithdraw.t.sol:137:13:
    |
137 |             uint32 serveUntilBlock = 5;
    |             ^^^^^^^^^^^^^^^^^^^^^^
Note: The shadowed declaration is here:
   --> src/test/DepositWithdraw.t.sol:177:9:
    |
177 |             uint32 serveUntilBlock = 7;
    |             ^^^^^^^^^^^^^^^^^^^^^^

```


## [NC-1] Unnecessary `return` statement

The return variable is already declared in the first line while declaring the function parameters. So its unnecessary to use `return` at the end of the function. 

```solidity
File: contracts/core/StrategyManager.sol

L:671             return shares;
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L671

```solidity
File: contracts/strategies/StrategyBase.sol

L:111             return newShares;
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L111

## [NC-2] Confusing NatSpec

```solidity
File: contracts/core/StrategyManager.sol

L:160        * @param amount is the amount of beaconchain ETH being restaked,
L:161        * @param amount is the amount of token to be deposited in the strategy by the depositor
```
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L160-L161