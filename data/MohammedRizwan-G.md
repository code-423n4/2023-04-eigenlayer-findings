## Summary

### Gas Optimizations
| |Issue|Instances| |
|-|:-|:-:|:-:|
| [G&#x2011;01] | Use nested if and, avoid multiple check combinations | 2 |


### [G&#x2011;01]  Use nested if and, avoid multiple check combinations
Using nested if is cheaper than using && multiple check combinations. There are more advantages, such as easier to read code and better coverage reports.
There are 2 instances of this issue.

```solidity
File: src/contracts/core/StrategyManager.sol

422        if (undelegateIfPossible && stakerStrategyList[msg.sender].length == 0) {
```
[Link to code](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L422)

```solidity
File: src/contracts/core/StrategyManager.sol

562            if (indicesToSkipIndex < indicesToSkip.length && indicesToSkip[indicesToSkipIndex] == i) {
```
[Link to code](https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L562)