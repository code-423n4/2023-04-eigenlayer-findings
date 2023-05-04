1. Incompleted Require statement

This string of requirement statement was incompleted, so it can be update later. Since some of the code using the same logic, so the one below was incompleted :

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/strategies/StrategyBase.sol#L41)

```
    modifier onlyStrategyManager() {
        require(msg.sender == address(strategyManager), "StrategyBase.onlyStrategyManager");
        _;
    }
```
update into : 
```
    modifier onlyStrategyManager() {
        require(msg.sender == address(strategyManager), "StrategyBase.onlyStrategyManager, not an StrategyManager");
        _;
    }
```
