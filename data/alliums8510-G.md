# Gas Optimization Report

## Gas Optimizations

|                 | Issue                                                          | Instances |
| --------------- | :------------------------------------------------------------- | :-------: |
| [GAS-1](#GAS-1) | Avoid code repetition & use assembly to check for `address(0)` |     3     |
| [GAS-2](#GAS-2) | Dont initialize variables to default value in loop             |    27     |
| [GAS-3](#GAS-3) | Misplaced `require` statement                                  |     2     |


<a name="GAS-1"></a>

### [GAS-1] Avoid code repetition & use assembly to check for `address(0)`

_Instances (3)_:

```solidity
File: /code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol
343:             require(withdrawer != address(0), "StrategyManager.queueWithdrawal: cannot withdraw to zero address");
631:             require(depositor != address(0), "StrategyManager._addShares: depositor cannot be zero address");
684:             require(depositor != address(0), "StrategyManager._removeShares: depositor cannot be zero address");

```

[Link to code](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol)

_Recommended: Use modifier to avoid code repetition & use assembly to check for `address(0)`_

<hr/>


<a name="GAS-2"></a>

### [GAS-2] Dont initialize variables to default value in loop

_Instances (27)_:

```solidity
File: /code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol
358:        for (uint256 i = 0; i < strategies.length;) {
466:        for(uint256 i = 0; i < queuedWithdrawals.length; i++) {
498:        for (uint256 i = 0; i < strategiesLength;) {
```

_Recommended: `for(uint256 i; i < strategies.length;){`_
Also, change in the size of loop index from `uint256 i;` to `uint64 i;` can save gas cost as the loop would not possibly exceed the maximum dynamic memory allocation size of 2^64 - 1. And it's not viable to loop through so much data as well.

<hr/>


<a name="GAS-3"></a>

### [GAS-3] Misplaced `require` statement.

At the end of all require statements, it finally checks if the `msg.sender` is the withdrawer. Should have checked it at the beginning instead.

_Instances (2)_:

```solidity
File: /code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol
766:     require(
            msg.sender == queuedWithdrawal.withdrawerAndNonce.withdrawer,
            "StrategyManager.completeQueuedWithdrawal: only specified withdrawer can complete a queued withdrawal"
        );
```

[Link to code](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L745)


<hr/>